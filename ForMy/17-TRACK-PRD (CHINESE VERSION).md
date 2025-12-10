# **产品需求文档（PRD）：17-Track 运单监控服务（MVP）**

# **增强版数据库结构与系统架构（完整版中文专业版）**

---

# **1. 执行摘要（Executive Summary）**

## **1.1 产品愿景（Product Vision）**

构建一个 **可用于生产环境的 Java 运单监控服务**，深度集成 **17TRACK 官方 API**，实现对全球 1700+ 物流承运商的多渠道、实时、可持续更新的运单跟踪能力。本服务应支持：

- 全链路事件级别跟踪
    
- 高性能缓存
    
- 自动同步调度
    
- 审计与监控
    
- 可扩展的数据库设计
    

目标是实现稳定、低延迟、高吞吐、可扩展的生产级运单监控基础能力。

---

## **1.2 关键增强功能（Key Enhancements）**

- **多承运商支持**：统一的承运商元数据管理
    
- **事件级别持久化**：完整存储所有轨迹事件
    
- **智能缓存系统**：基于 TTL 的智能状态缓存
    
- **自动同步调度**：带重试、优先级、批量处理
    
- **审计日志**：确保所有外部 API 调用可追踪
    
- **优雅错误处理**：防止因第三方异常导致服务不可用
    

---

# **2. 数据库架构设计（Database Schema Design）**

数据库选型：**PostgreSQL 15+**

以下包含 **所有表结构，全部完整呈现**，无任何省略。

---

# **2.1 核心业务表（Core Tables）**

---

## **2.1.1 carriers — 承运商表**

**用途：**  
存储系统支持的所有承运商主数据，包括名称、查询 URL、电话等信息。

```sql
CREATE TABLE carriers (
    carrier_id SERIAL PRIMARY KEY,
    carrier_code VARCHAR(50) UNIQUE NOT NULL,    -- 承运商代码（如 USPS、YANWEN）
    carrier_name VARCHAR(200) NOT NULL,          -- 英文承运商名称
    carrier_name_cn VARCHAR(200),                -- 中文承运商名称
    carrier_phone VARCHAR(50),
    carrier_url VARCHAR(500),
    tracking_url_template VARCHAR(500),          -- 承运商官网跟踪 URL 模板
    is_active BOOLEAN DEFAULT true,
    supports_prediction BOOLEAN DEFAULT false,   -- 是否支持预计达达时间
    avg_delivery_days INTEGER,                   -- 平均派送时长

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_carriers_code ON carriers(carrier_code);
CREATE INDEX idx_carriers_active ON carriers(is_active);
```

---

## **2.1.2 tracking_records — 运单主记录表**

**用途：**  
每个运单（tracking number）的主记录，包括当前状态、位置、同步配置、API 缓存等。

```sql
CREATE TABLE tracking_records (
    tracking_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tracking_number VARCHAR(100) NOT NULL,       -- 运单号
    carrier_id INTEGER NOT NULL REFERENCES carriers(carrier_id),

    -- 当前状态
    current_status VARCHAR(50) NOT NULL,
    current_substatus VARCHAR(50),
    status_code INTEGER,

    -- 当前地理位置
    current_location VARCHAR(500),
    current_location_country VARCHAR(2),
    current_location_city VARCHAR(200),

    -- 派送相关信息
    origin_country VARCHAR(2),
    destination_country VARCHAR(2),
    estimated_delivery_date DATE,
    actual_delivery_date TIMESTAMP,
    recipient_name VARCHAR(200),

    -- 客户/订单关联
    customer_reference VARCHAR(100),
    order_id VARCHAR(100),

    -- 自动同步配置
    auto_refresh_enabled BOOLEAN DEFAULT true,
    sync_interval_minutes INTEGER DEFAULT 240,
    last_synced_at TIMESTAMP,
    next_sync_at TIMESTAMP,
    sync_retry_count INTEGER DEFAULT 0,
    max_sync_retries INTEGER DEFAULT 5,

    -- API 响应缓存
    raw_api_response JSONB,
    api_response_cached_at TIMESTAMP,
    cache_ttl_minutes INTEGER DEFAULT 60,

    -- 状态标志位
    is_delivered BOOLEAN DEFAULT false,
    is_exception BOOLEAN DEFAULT false,
    is_expired BOOLEAN DEFAULT false,
    requires_attention BOOLEAN DEFAULT false,

    -- 时间戳
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT unique_tracking_carrier UNIQUE(tracking_number, carrier_id)
);

CREATE INDEX idx_tracking_number ON tracking_records(tracking_number);
CREATE INDEX idx_tracking_status ON tracking_records(current_status);
CREATE INDEX idx_tracking_next_sync ON tracking_records(next_sync_at) WHERE auto_refresh_enabled = true;
CREATE INDEX idx_tracking_carrier ON tracking_records(carrier_id);
CREATE INDEX idx_tracking_customer_ref ON tracking_records(customer_reference);
CREATE INDEX idx_tracking_requires_attention ON tracking_records(requires_attention) WHERE requires_attention = true;
```

---

## **2.1.3 tracking_events — 运单事件表（全轨迹）**

**用途：**  
保存每个运单的事件级轨迹，用于构建完整运输时间线。

```sql
CREATE TABLE tracking_events (
    event_id BIGSERIAL PRIMARY KEY,
    tracking_id UUID NOT NULL REFERENCES tracking_records(tracking_id) ON DELETE CASCADE,

    -- 事件基本信息
    event_sequence INTEGER NOT NULL,
    event_status VARCHAR(50) NOT NULL,
    event_substatus VARCHAR(50),
    event_status_code INTEGER,

    -- 位置详情
    location_country VARCHAR(2),
    location_state VARCHAR(100),
    location_city VARCHAR(200),
    location_postal_code VARCHAR(20),
    location_full_address VARCHAR(500),
    location_timezone VARCHAR(50),

    -- 描述信息
    event_description TEXT,
    event_description_cn TEXT,

    -- 时间戳信息
    event_timestamp TIMESTAMP NOT NULL,
    event_timezone_offset VARCHAR(10),

    -- 额外元数据
    facility_name VARCHAR(200),
    carrier_event_code VARCHAR(50),
    weight_kg DECIMAL(10,2),
    dimensions_cm VARCHAR(50),

    -- 原始事件数据
    raw_event_data JSONB,

    -- 创建时间
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT unique_tracking_event UNIQUE(tracking_id, event_sequence)
);

CREATE INDEX idx_events_tracking_id ON tracking_events(tracking_id);
CREATE INDEX idx_events_timestamp ON tracking_events(event_timestamp DESC);
CREATE INDEX idx_events_status ON tracking_events(event_status);
CREATE INDEX idx_events_location ON tracking_events(location_country, location_city);
```

---

## **2.1.4 sync_jobs — 同步任务表**

**用途：**  
记录所有同步任务的执行记录、状态、结果，用于调度、监控、重试。

```sql
CREATE TABLE sync_jobs (
    job_id BIGSERIAL PRIMARY KEY,
    job_batch_id UUID DEFAULT gen_random_uuid(),
    tracking_id UUID NOT NULL REFERENCES tracking_records(tracking_id),

    -- 任务配置
    job_type VARCHAR(50) NOT NULL,   -- SCHEDULED, MANUAL, RETRY, BATCH
    job_priority INTEGER DEFAULT 5,  -- 1=最高，10=最低

    -- 执行状态
    job_status VARCHAR(50) NOT NULL, -- PENDING, RUNNING, COMPLETED, FAILED, CANCELLED
    scheduled_at TIMESTAMP NOT NULL,
    started_at TIMESTAMP,
    completed_at TIMESTAMP,

    -- 重试信息
    attempt_number INTEGER DEFAULT 1,
    max_attempts INTEGER DEFAULT 3,
    retry_delay_minutes INTEGER,

    -- 结果数据
    events_fetched INTEGER DEFAULT 0,
    events_new INTEGER DEFAULT 0,
    status_changed BOOLEAN DEFAULT false,

    -- 错误信息
    error_code VARCHAR(50),
    error_message TEXT,
    error_details JSONB,

    -- 性能指标
    api_response_time_ms INTEGER,
    processing_time_ms INTEGER,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_sync_jobs_status ON sync_jobs(job_status);
CREATE INDEX idx_sync_jobs_scheduled ON sync_jobs(scheduled_at) WHERE job_status = 'PENDING';
CREATE INDEX idx_sync_jobs_tracking ON sync_jobs(tracking_id);
CREATE INDEX idx_sync_jobs_batch ON sync_jobs(job_batch_id);
CREATE INDEX idx_sync_jobs_priority ON sync_jobs(job_priority, scheduled_at) WHERE job_status = 'PENDING';
```

---

## **2.1.5 status_cache — 状态缓存表**

**用途：**  
临时缓存运单状态，减少频繁调用 17TRACK API。

```sql
CREATE TABLE status_cache (
    cache_id BIGSERIAL PRIMARY KEY,
    tracking_id UUID NOT NULL REFERENCES tracking_records(tracking_id) ON DELETE CASCADE,

    -- 缓存键
    cache_key VARCHAR(200) UNIQUE NOT NULL,

    -- 缓存内容
    cached_status VARCHAR(50) NOT NULL,
    cached_location VARCHAR(500),
    cached_event_count INTEGER,
    cached_response JSONB NOT NULL,

    -- TTL 管理
    cache_strategy VARCHAR(50) NOT NULL, -- FIXED, DYNAMIC, ADAPTIVE
    ttl_minutes INTEGER NOT NULL,
    cached_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,

    -- 缓存命中情况
    hit_count INTEGER DEFAULT 0,
    last_hit_at TIMESTAMP,

    -- 有效性
    is_valid BOOLEAN DEFAULT true,
    invalidated_at TIMESTAMP,
    invalidation_reason VARCHAR(200),

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_cache_key ON status_cache(cache_key);
CREATE INDEX idx_cache_tracking ON status_cache(tracking_id);
CREATE INDEX idx_cache_expires ON status_cache(expires_at) WHERE is_valid = true;
CREATE INDEX idx_cache_cleanup ON status_cache(expires_at) WHERE is_valid = true;
```

---

## **2.1.6 event_notifications — 通知记录表**

**用途：**  
用于推送事件/状态变化通知，如邮件、短信、Webhook 等。

```sql
CREATE TABLE event_notifications (
    notification_id BIGSERIAL PRIMARY KEY,
    tracking_id UUID NOT NULL REFERENCES tracking_records(tracking_id),
    event_id BIGINT REFERENCES tracking_events(event_id),

    -- 通知类型
    notification_type VARCHAR(50) NOT NULL, -- STATUS_CHANGE, DELIVERED, EXCEPTION, DELAY
    notification_channel VARCHAR(50),       -- EMAIL, SMS, WEBHOOK, PUSH
    recipient VARCHAR(200),

    -- 内容
    subject VARCHAR(500),
    message TEXT,
    notification_payload JSONB,

    -- 投递状态
    status VARCHAR(50) DEFAULT 'PENDING', -- PENDING, SENT, FAILED, CANCELLED
    sent_at TIMESTAMP,
    delivered_at TIMESTAMP,
    failed_at TIMESTAMP,
    failure_reason TEXT,

    -- 重试逻辑
    retry_count INTEGER DEFAULT 0,
    max_retries INTEGER DEFAULT 3,
    next_retry_at TIMESTAMP,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_notifications_tracking ON event_notifications(tracking_id);
CREATE INDEX idx_notifications_status ON event_notifications(status);
CREATE INDEX idx_notifications_retry ON event_notifications(next_retry_at) WHERE status = 'FAILED';
```

---

## **2.1.7 api_audit_log — API 审计日志表**

**用途：**  
记录每一次外部 API 请求，用于监控、追踪、排错与合规。

```sql
CREATE TABLE api_audit_log (
    audit_id BIGSERIAL PRIMARY KEY,

    -- 请求信息
    tracking_id UUID REFERENCES tracking_records(tracking_id),
    endpoint VARCHAR(200) NOT NULL,
    http_method VARCHAR(10) NOT NULL,
    request_params JSONB,
    request_headers JSONB,

    -- 外部 API 调用
    external_api_called BOOLEAN DEFAULT false,
    api_provider VARCHAR(50),  -- 17TRACK, CUSTOM
    api_endpoint VARCHAR(500),
    api_request_payload JSONB,

    -- 响应信息
    response_status_code INTEGER,
    response_body JSONB,
    response_time_ms INTEGER,

    -- 操作结果
    operation_status VARCHAR(50), -- SUCCESS, FAILURE, PARTIAL
    error_code VARCHAR(50),
    error_message TEXT,

    -- 用户上下文
    user_id VARCHAR(100),
    ip_address INET,
    user_agent VARCHAR(500),

    -- 时间戳
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_tracking ON api_audit_log(tracking_id);
CREATE INDEX idx_audit_created ON api_audit_log(created_at DESC);
CREATE INDEX idx_audit_status ON api_audit_log(operation_status);
CREATE INDEX idx_audit_endpoint ON api_audit_log(endpoint);
```

---

## **2.1.8 sync_schedule_config — 同步调度配置表**

**用途：**  
用于动态调整不同承运商/区域/状态的同步频率。

```sql
CREATE TABLE sync_schedule_config (
    config_id SERIAL PRIMARY KEY,

    -- 规则定义
    rule_name VARCHAR(100) UNIQUE NOT NULL,
    rule_description TEXT,
    is_active BOOLEAN DEFAULT true,

    -- 匹配条件
    carrier_id INTEGER REFERENCES carriers(carrier_id),
    status_pattern VARCHAR(50),          -- IN_TRANSIT, DELIVERED 等
    destination_country VARCHAR(2),

    -- 调度配置
    sync_interval_minutes INTEGER NOT NULL,
    max_age_days INTEGER,
    stop_on_delivered BOOLEAN DEFAULT true,
    stop_on_exception BOOLEAN DEFAULT false,

    -- 重试策略
    retry_strategy VARCHAR(50), -- EXPONENTIAL, FIXED, LINEAR
    initial_retry_delay_minutes INTEGER DEFAULT 30,
    max_retry_attempts INTEGER DEFAULT 5,

    -- 优先级
    priority_level INTEGER DEFAULT 5,

    -- 活跃时间窗口（JSON）
    active_time_windows JSONB,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_sync_config_active ON sync_schedule_config(is_active);
CREATE INDEX idx_sync_config_carrier ON sync_schedule_config(carrier_id);
```

---

# **2.2 Redis 缓存 Schema（完整中文版）**

```
# 状态缓存（动态 TTL）
tracking:status:{tracking_id}
→ { status, location, timestamp }

# 事件缓存
tracking:events:{tracking_id}
→ [events...]

# 承运商元信息缓存
carrier:metadata:{carrier_code}

# API 调用限流
ratelimit:ip:{ip_address}
ratelimit:api:17track

# 活跃同步任务
sync:active:{tracking_id}

# 热点运单
tracking:hot:{tracking_number}
```

---

# **3. 功能需求（Functional Requirements）**

## **3.1 多承运商支持**

### **3.1.1 承运商管理**

- 维护 1700+ 承运商主数据
    
- 支持启用/禁用承运商
    
- 记录承运商历史统计（时效）
    

### **3.1.2 自动识别承运商**

- 根据 tracking number 正则识别
    
- 支持人工覆盖指定
    

---

## **3.2 事件级（Event-Level）历史持久化**

### **3.2.1 事件采集**

- 每一次状态变化都作为独立事件保存
    
- 支持轨迹事件去重
    
- 保存完整地理与时间信息
    

### **3.2.2 事件查询**

- 全事件 timeline 按时间排序
    
- 筛选（日期/状态/地点）
    
- 支持分页和导出（CSV、JSON）
    

---

## **3.3 智能缓存系统**

### **3.3.1 缓存策略**

- FIXED：固定 TTL
    
- DYNAMIC：按状态调整 TTL
    
- ADAPTIVE：根据更新频率自动调整
    

### **3.3.2 缓存失效**

- 手动/自动失效
    
- 状态变化 → 自动清除缓存
    

---

## **3.4 自动同步调度（Scheduled Sync）**

### **3.4.1 调度器**

- 基于优先级（1-10）
    
- 按状态调整同步间隔
    
- 支持批量同步（50~100 条）
    

### **3.4.2 同步执行流程**

- 调用 17TRACK API
    
- 合并新事件
    
- 写入 tracking_records + tracking_events
    
- 若状态变化 → 通知系统触发
    
- 写入审计日志
    

### **3.4.3 重试机制**

- 指数退避
    
- 最大重试次数
    
- Dead Letter Queue（DLQ）
    

### **3.4.4 同步优化**

- 已签收超过 7 天 → 停止同步
    
- 长时间无更新 → 降低同步频率
    

---

# **4. 系统技术架构（Technical Architecture）**

完整专业中文架构图：

```
┌─────────────────────────────────────────────┐
│                REST API 层（Spring MVC）     │
├─────────────────────────────────────────────┤
│                服务层（Service Layer）        │
│  - TrackingService                           │
│  - SyncService                               │
│  - CacheService                              │
│  - NotificationService                       │
├─────────────────────────────────────────────┤
│              数据访问层（Repository）        │
├─────────────────────────────────────────────┤
│    Redis 缓存层            │    PostgreSQL 数据库 │
│    - 状态缓存              │    - 主数据           │
│    - 限流                  │    - 事件历史         │
│    - 热点数据              │    - 审计日志         │
└────────────────────────────┴─────────────────┘
```

---

# **4.2 技术栈（Technology Stack）**

- Java 17+
    
- Spring Boot 3.2+
    
- PostgreSQL 15+
    
- Redis 7+
    
- Spring Data JPA
    
- Quartz Scheduler / Spring Scheduling
    
- Spring WebClient
    
- Flyway Migration
    
- RabbitMQ（未来扩展）
    

---

# **4.3 核心服务设计（Core Service Design）**

## **4.3.1 TrackingService**

```java
public interface TrackingService {
    TrackingRecord registerTracking(RegisterTrackingRequest request);
    TrackingRecord getTrackingStatus(UUID trackingId);
    List<TrackingEvent> getTrackingHistory(UUID trackingId);
    void refreshTracking(UUID trackingId);
    Page<TrackingRecord> searchTrackings(TrackingSearchCriteria criteria);
}
```

## **4.3.2 SyncService**

```java
public interface SyncService {
    void scheduleSync(UUID trackingId, LocalDateTime scheduledTime);
    void executeSyncJob(Long jobId);
    void retryFailedJob(Long jobId);
    List<SyncJob> getPendingJobs(int limit);
    SyncJobStatistics getJobStatistics();
}
```

## **4.3.3 CacheService**

```java
public interface CacheService {
    <T> Optional<T> get(String key, Class<T> type);
    void set(String key, Object value, Duration ttl);
    void invalidate(String key);
    void invalidateByPattern(String pattern);
    CacheStatistics getStatistics();
}
```

---

# **5. API 接口设计（API Endpoints）**

## **5.1 运单操作**

```
POST   /api/v1/tracking/register
POST   /api/v1/tracking/batch-register
GET    /api/v1/tracking/{trackingId}
GET    /api/v1/tracking/{trackingId}/events
GET    /api/v1/tracking/{trackingId}/refresh
POST   /api/v1/tracking/search
DELETE /api/v1/tracking/{trackingId}
```

## **5.2 同步任务管理**

```
POST   /api/v1/sync/schedule/{trackingId}
POST   /api/v1/sync/execute/{jobId}
GET    /api/v1/sync/jobs
GET    /api/v1/sync/statistics
POST   /api/v1/sync/retry/{jobId}
```

## **5.3 缓存管理**

```
DELETE /api/v1/cache/{trackingId}
DELETE /api/v1/cache/pattern
POST   /api/v1/cache/warm
GET    /api/v1/cache/statistics
```

## **5.4 承运商相关**

```
GET    /api/v1/carriers
GET    /api/v1/carriers/{carrierCode}
POST   /api/v1/carriers/detect
```

---

# **6. 性能要求（Performance Requirements）**

- 数据库查询响应时间：< **100ms**
    
- 批量事件插入：≥ **1000 条/秒**
    
- Redis 响应时间：< **10ms**
    
- 缓存命中率：≥ **80%**
    
- 同步任务处理速率 ≥ **100 条/分钟**
    
- 最大并发同步任务：20 线程
    

---

# **7. 监控与可观测性（Monitoring）**

监控指标：

- 活跃运单数量
    
- 同步成功率/失败率
    
- API 响应 p50、p95、p99
    
- 缓存命中率
    
- 17TRACK API 调用配额
    
- 数据库连接池占用率
    

告警：

- 同步失败率 > 10%
    
- Redis 缓存命中率 < 70%
    
- DB 连接耗尽
    
- API 接近配额（80%+）
    
- Sync 队列积压 > 1000
    

---

# **8. 数据保留策略**

- 活跃运单：长期保留
    
- 已送达运单：90 天
    
- 事件历史：180 天
    
- 同步任务：30 天
    
- 审计日志：365 天
    
- 缓存：TTL 自动失效
    
- 失败任务：7 天保留
    

---

# **9. 实施路线图（Roadmap）**

### **第 1-2 周：数据库与基础架构**

- 创建所有表
    
- 配置 Flyway
    
- 搭建 Redis
    
- 索引优化
    

### **第 3-4 周：核心服务**

- TrackingService
    
- 17TRACK API 客户端
    
- 缓存服务
    
- 轨迹事件持久层
    

### **第 5-6 周：同步调度系统**

- SyncService
    
- Quartz 调度器
    
- 批量同步
    
- 重试逻辑
    

### **第 7-8 周：高级功能开发**

- 通知系统
    
- 审计日志
    
- 搜索 & 过滤
    
- 蜘蛛式性能优化
    

### **第 9-10 周：测试与上线**

- 单测覆盖率 80%
    
- TestContainers 集成测试
    
- 压测
    
- 部署上线
    

---

# **10. 成功标准（Success Criteria）**

### 功能成功：

- 可同时监控 ≥ **100,000 运单**
    
- 支持前 50 名主流承运商
    
- 状态更新准确率 ≥ **95%**
    
- 轨迹事件完整无缺失
    
- 不发生数据丢失
    

### 性能成功：

- API 响应 < 200ms
    
- 缓存命中率 > 80%
    
- 同步吞吐 ≥ 100 条/分钟
    
- 同步失败率 < 5%
    
- 服务可用性 ≥ **99.5%**
    

### 运营成功：

- 自动同步无需人工干预
    
- 同步失败自动恢复
    
- 审计日志完整
    
- 支持水平扩展至 100 万条运单
    

---

# **11. 风险与缓解（Risk Mitigation）**

|风险|影响|缓解策略|
|---|---|---|
|17TRACK 宕机|高|熔断 + 缓存兜底|
|数据库压力上升|高|分区、索引、读写分离|
|内存过高|中|流式查询、分页|
|缓存不一致|中|TTL + 主动失效|
|同步任务堆积|中|优先级队列 + 扩容|

---

# **12. 待确认问题（Open Questions）**

1. 是否需要对 UPS / FedEx 等高端物流给予更高优先级？
    
2. 是否有区域数据存储（Data Sovereignty）要求？
    
3. 是否计划支持 17TRACK Webhook 模式？
    
4. 未来是否需要多租户隔离？
    
5. SLA 要求是什么？
    
6. 17TRACK 的 API 成本预算是多少？
    
7. 预计未来一年运单规模增长多少？
    


