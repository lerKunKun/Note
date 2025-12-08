# Product Requirements Document: 17-Track Waybill Monitoring Service (MVP)

## Enhanced Database Schema & Architecture

## 1. Executive Summary

### 1.1 Product Vision

Build a production-ready Java-based service that integrates with 17-track API to monitor waybill tracking across 1700+ carriers, with comprehensive database design supporting multi-carrier tracking, granular event history, intelligent caching, and automated synchronization workflows.

### 1.2 Key Enhancements

- **Multi-carrier support** with carrier metadata management
- **Event-level persistence** for complete tracking timeline
- **Intelligent status caching** with TTL strategies
- **Scheduled sync workflows** with retry and failure handling
- **Audit trail** for compliance and debugging

## 2. Database Schema Design

### 2.1 Core Tables

#### 2.1.1 Carriers Table

**Purpose**: Master data for supported carriers

```sql
CREATE TABLE carriers (
    carrier_id SERIAL PRIMARY KEY,
    carrier_code VARCHAR(50) UNIQUE NOT NULL,
    carrier_name VARCHAR(200) NOT NULL,
    carrier_name_cn VARCHAR(200),
    carrier_phone VARCHAR(50),
    carrier_url VARCHAR(500),
    tracking_url_template VARCHAR(500),
    is_active BOOLEAN DEFAULT true,
    supports_prediction BOOLEAN DEFAULT false,
    avg_delivery_days INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_carriers_code ON carriers(carrier_code);
CREATE INDEX idx_carriers_active ON carriers(is_active);
```

#### 2.1.2 Tracking Records Table

**Purpose**: Primary tracking number registration and status

```sql
CREATE TABLE tracking_records (
    tracking_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tracking_number VARCHAR(100) NOT NULL,
    carrier_id INTEGER NOT NULL REFERENCES carriers(carrier_id),
    
    -- Current Status
    current_status VARCHAR(50) NOT NULL,
    current_substatus VARCHAR(50),
    status_code INTEGER,
    
    -- Location Information
    current_location VARCHAR(500),
    current_location_country VARCHAR(2),
    current_location_city VARCHAR(200),
    
    -- Delivery Information
    origin_country VARCHAR(2),
    destination_country VARCHAR(2),
    estimated_delivery_date DATE,
    actual_delivery_date TIMESTAMP,
    recipient_name VARCHAR(200),
    
    -- Metadata
    customer_reference VARCHAR(100),
    order_id VARCHAR(100),
    
    -- Sync Control
    auto_refresh_enabled BOOLEAN DEFAULT true,
    sync_interval_minutes INTEGER DEFAULT 240,
    last_synced_at TIMESTAMP,
    next_sync_at TIMESTAMP,
    sync_retry_count INTEGER DEFAULT 0,
    max_sync_retries INTEGER DEFAULT 5,
    
    -- API Response Cache
    raw_api_response JSONB,
    api_response_cached_at TIMESTAMP,
    cache_ttl_minutes INTEGER DEFAULT 60,
    
    -- Status Flags
    is_delivered BOOLEAN DEFAULT false,
    is_exception BOOLEAN DEFAULT false,
    is_expired BOOLEAN DEFAULT false,
    requires_attention BOOLEAN DEFAULT false,
    
    -- Timestamps
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

#### 2.1.3 Tracking Events Table

**Purpose**: Event-level history persistence with full timeline

```sql
CREATE TABLE tracking_events (
    event_id BIGSERIAL PRIMARY KEY,
    tracking_id UUID NOT NULL REFERENCES tracking_records(tracking_id) ON DELETE CASCADE,
    
    -- Event Details
    event_sequence INTEGER NOT NULL,
    event_status VARCHAR(50) NOT NULL,
    event_substatus VARCHAR(50),
    event_status_code INTEGER,
    
    -- Location Details
    location_country VARCHAR(2),
    location_state VARCHAR(100),
    location_city VARCHAR(200),
    location_postal_code VARCHAR(20),
    location_full_address VARCHAR(500),
    location_timezone VARCHAR(50),
    
    -- Event Description
    event_description TEXT,
    event_description_cn TEXT,
    
    -- Timestamp Information
    event_timestamp TIMESTAMP NOT NULL,
    event_timezone_offset VARCHAR(10),
    
    -- Additional Metadata
    facility_name VARCHAR(200),
    carrier_event_code VARCHAR(50),
    weight_kg DECIMAL(10,2),
    dimensions_cm VARCHAR(50),
    
    -- Raw Data
    raw_event_data JSONB,
    
    -- Tracking
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT unique_tracking_event UNIQUE(tracking_id, event_sequence)
);

CREATE INDEX idx_events_tracking_id ON tracking_events(tracking_id);
CREATE INDEX idx_events_timestamp ON tracking_events(event_timestamp DESC);
CREATE INDEX idx_events_status ON tracking_events(event_status);
CREATE INDEX idx_events_location ON tracking_events(location_country, location_city);
```

#### 2.1.4 Sync Jobs Table

**Purpose**: Scheduled synchronization workflow management

```sql
CREATE TABLE sync_jobs (
    job_id BIGSERIAL PRIMARY KEY,
    job_batch_id UUID DEFAULT gen_random_uuid(),
    tracking_id UUID NOT NULL REFERENCES tracking_records(tracking_id),
    
    -- Job Configuration
    job_type VARCHAR(50) NOT NULL, -- SCHEDULED, MANUAL, RETRY, BATCH
    job_priority INTEGER DEFAULT 5, -- 1=highest, 10=lowest
    
    -- Execution Status
    job_status VARCHAR(50) NOT NULL, -- PENDING, RUNNING, COMPLETED, FAILED, CANCELLED
    scheduled_at TIMESTAMP NOT NULL,
    started_at TIMESTAMP,
    completed_at TIMESTAMP,
    
    -- Execution Details
    attempt_number INTEGER DEFAULT 1,
    max_attempts INTEGER DEFAULT 3,
    retry_delay_minutes INTEGER,
    
    -- Results
    events_fetched INTEGER DEFAULT 0,
    events_new INTEGER DEFAULT 0,
    status_changed BOOLEAN DEFAULT false,
    
    -- Error Handling
    error_code VARCHAR(50),
    error_message TEXT,
    error_details JSONB,
    
    -- Performance Metrics
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

#### 2.1.5 Status Cache Table

**Purpose**: Intelligent caching with TTL strategies

```sql
CREATE TABLE status_cache (
    cache_id BIGSERIAL PRIMARY KEY,
    tracking_id UUID NOT NULL REFERENCES tracking_records(tracking_id) ON DELETE CASCADE,
    
    -- Cache Key
    cache_key VARCHAR(200) UNIQUE NOT NULL,
    
    -- Cached Data
    cached_status VARCHAR(50) NOT NULL,
    cached_location VARCHAR(500),
    cached_event_count INTEGER,
    cached_response JSONB NOT NULL,
    
    -- TTL Management
    cache_strategy VARCHAR(50) NOT NULL, -- FIXED, DYNAMIC, ADAPTIVE
    ttl_minutes INTEGER NOT NULL,
    cached_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    
    -- Cache Performance
    hit_count INTEGER DEFAULT 0,
    last_hit_at TIMESTAMP,
    
    -- Validation
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

#### 2.1.6 Event Notifications Table

**Purpose**: Track notification triggers for status changes

```sql
CREATE TABLE event_notifications (
    notification_id BIGSERIAL PRIMARY KEY,
    tracking_id UUID NOT NULL REFERENCES tracking_records(tracking_id),
    event_id BIGINT REFERENCES tracking_events(event_id),
    
    -- Notification Details
    notification_type VARCHAR(50) NOT NULL, -- STATUS_CHANGE, DELIVERED, EXCEPTION, DELAY
    notification_channel VARCHAR(50), -- EMAIL, SMS, WEBHOOK, PUSH
    recipient VARCHAR(200),
    
    -- Content
    subject VARCHAR(500),
    message TEXT,
    notification_payload JSONB,
    
    -- Delivery Status
    status VARCHAR(50) DEFAULT 'PENDING', -- PENDING, SENT, FAILED, CANCELLED
    sent_at TIMESTAMP,
    delivered_at TIMESTAMP,
    failed_at TIMESTAMP,
    failure_reason TEXT,
    
    -- Retry Logic
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

#### 2.1.7 API Audit Log Table

**Purpose**: Comprehensive audit trail for compliance

```sql
CREATE TABLE api_audit_log (
    audit_id BIGSERIAL PRIMARY KEY,
    
    -- Request Details
    tracking_id UUID REFERENCES tracking_records(tracking_id),
    endpoint VARCHAR(200) NOT NULL,
    http_method VARCHAR(10) NOT NULL,
    request_params JSONB,
    request_headers JSONB,
    
    -- API Call Details
    external_api_called BOOLEAN DEFAULT false,
    api_provider VARCHAR(50), -- 17TRACK, CUSTOM
    api_endpoint VARCHAR(500),
    api_request_payload JSONB,
    
    -- Response Details
    response_status_code INTEGER,
    response_body JSONB,
    response_time_ms INTEGER,
    
    -- Result
    operation_status VARCHAR(50), -- SUCCESS, FAILURE, PARTIAL
    error_code VARCHAR(50),
    error_message TEXT,
    
    -- User Context
    user_id VARCHAR(100),
    ip_address INET,
    user_agent VARCHAR(500),
    
    -- Timestamps
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_tracking ON api_audit_log(tracking_id);
CREATE INDEX idx_audit_created ON api_audit_log(created_at DESC);
CREATE INDEX idx_audit_status ON api_audit_log(operation_status);
CREATE INDEX idx_audit_endpoint ON api_audit_log(endpoint);
```

#### 2.1.8 Sync Schedule Configuration Table

**Purpose**: Flexible sync scheduling rules

```sql
CREATE TABLE sync_schedule_config (
    config_id SERIAL PRIMARY KEY,
    
    -- Rule Definition
    rule_name VARCHAR(100) UNIQUE NOT NULL,
    rule_description TEXT,
    is_active BOOLEAN DEFAULT true,
    
    -- Matching Criteria
    carrier_id INTEGER REFERENCES carriers(carrier_id),
    status_pattern VARCHAR(50), -- IN_TRANSIT, DELIVERED, etc.
    destination_country VARCHAR(2),
    
    -- Schedule Configuration
    sync_interval_minutes INTEGER NOT NULL,
    max_age_days INTEGER, -- Stop syncing after X days
    stop_on_delivered BOOLEAN DEFAULT true,
    stop_on_exception BOOLEAN DEFAULT false,
    
    -- Retry Configuration
    retry_strategy VARCHAR(50), -- EXPONENTIAL, FIXED, LINEAR
    initial_retry_delay_minutes INTEGER DEFAULT 30,
    max_retry_attempts INTEGER DEFAULT 5,
    
    -- Priority
    priority_level INTEGER DEFAULT 5,
    
    -- Time Windows (JSON array of time ranges)
    active_time_windows JSONB,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_sync_config_active ON sync_schedule_config(is_active);
CREATE INDEX idx_sync_config_carrier ON sync_schedule_config(carrier_id);
```

### 2.2 Redis Cache Schema Design

```
# Cache Keys Structure

# Status Cache (TTL: 1-24 hours based on status)
tracking:status:{tracking_id} -> JSON {status, location, timestamp}

# Event Cache (TTL: 6 hours)
tracking:events:{tracking_id} -> JSON [array of events]

# Carrier Metadata (TTL: 24 hours)
carrier:metadata:{carrier_code} -> JSON {carrier details}

# Rate Limiting (TTL: 60 seconds)
ratelimit:ip:{ip_address} -> Integer (request count)
ratelimit:api:17track -> Integer (API call count)

# Active Sync Jobs (TTL: 1 hour)
sync:active:{tracking_id} -> JSON {job details}

# Hot Tracking Numbers (TTL: 15 minutes)
tracking:hot:{tracking_number} -> tracking_id
```

## 3. Functional Requirements

### 3.1 Multi-Carrier Support

#### 3.1.1 Carrier Management

- Maintain master carrier database with 1700+ carriers
- Support carrier metadata (name, URL, tracking template)
- Enable/disable carriers dynamically
- Track carrier-specific delivery statistics

#### 3.1.2 Auto-Detection

- Automatically detect carrier from tracking number format
- Support manual carrier override
- Handle multi-carrier scenarios (handoff between carriers)

### 3.2 Event-Level History Persistence

#### 3.2.1 Event Tracking

- Capture every status change as discrete event
- Maintain chronological event sequence
- Store granular location data per event
- Preserve original carrier event descriptions
- Support event deduplication

#### 3.2.2 Event Queries

- Retrieve full event timeline
- Filter events by date range, status, location
- Support pagination for long histories
- Export event history (JSON, CSV)

### 3.3 Intelligent Status Caching

#### 3.3.1 Cache Strategies

- **FIXED**: Standard TTL for all records
- **DYNAMIC**: TTL based on delivery status
    - In Transit: 1 hour
    - Out for Delivery: 15 minutes
    - Delivered: 24 hours
    - Exception: 30 minutes
- **ADAPTIVE**: Adjust TTL based on update frequency

#### 3.3.2 Cache Invalidation

- Manual cache invalidation API
- Auto-invalidation on status change
- Batch cache warming for frequently accessed records

### 3.4 Scheduled Synchronization Workflows

#### 3.4.1 Sync Job Scheduler

- Priority-based job queue
- Configurable sync intervals per carrier/status
- Batch processing (50-100 tracking numbers per batch)
- Parallel execution with concurrency control

#### 3.4.2 Sync Execution

- Fetch latest data from 17-track API
- Detect status changes and new events
- Update tracking records and events tables
- Trigger notifications on status changes
- Log all operations in audit table

#### 3.4.3 Retry Logic

- Exponential backoff for failed syncs
- Maximum retry attempts with configurable limits
- Dead letter queue for permanently failed jobs
- Alert on repeated failures

#### 3.4.4 Sync Optimization

- Skip delivered packages after 7 days
- Reduce frequency for stale tracking numbers
- Prioritize customer-facing packages
- Respect 17-track API rate limits (100 req/min)

## 4. Technical Architecture

### 4.1 Application Layers

```
┌─────────────────────────────────────────────┐
│         REST API Layer (Spring MVC)         │
├─────────────────────────────────────────────┤
│       Service Layer (Business Logic)        │
│  - TrackingService                          │
│  - SyncService                              │
│  - CacheService                             │
│  - NotificationService                      │
├─────────────────────────────────────────────┤
│     Repository Layer (Spring Data JPA)      │
├─────────────────────────────────────────────┤
│    Cache Layer (Redis)    │  Database       │
│    - Status Cache          │  (PostgreSQL)   │
│    - Rate Limiting         │  - Master Data  │
│    - Hot Data              │  - History      │
└────────────────────────────┴─────────────────┘
```

### 4.2 Technology Stack

- **Java**: 17+ with Records and Sealed Classes
- **Framework**: Spring Boot 3.2+
- **Database**: PostgreSQL 15+
- **Cache**: Redis 7+
- **ORM**: Spring Data JPA + Hibernate
- **Scheduler**: Spring @Scheduled + Quartz
- **Queue**: Spring Integration or RabbitMQ (future)
- **HTTP Client**: Spring WebClient (reactive)
- **Connection Pool**: HikariCP
- **Migration**: Flyway

### 4.3 Core Services Design

#### 4.3.1 TrackingService

```java
public interface TrackingService {
    TrackingRecord registerTracking(RegisterTrackingRequest request);
    TrackingRecord getTrackingStatus(UUID trackingId);
    List<TrackingEvent> getTrackingHistory(UUID trackingId);
    void refreshTracking(UUID trackingId);
    Page<TrackingRecord> searchTrackings(TrackingSearchCriteria criteria);
}
```

#### 4.3.2 SyncService

```java
public interface SyncService {
    void scheduleSync(UUID trackingId, LocalDateTime scheduledTime);
    void executeSyncJob(Long jobId);
    void retryFailedJob(Long jobId);
    List<SyncJob> getPendingJobs(int limit);
    SyncJobStatistics getJobStatistics();
}
```

#### 4.3.3 CacheService

```java
public interface CacheService {
    <T> Optional<T> get(String key, Class<T> type);
    void set(String key, Object value, Duration ttl);
    void invalidate(String key);
    void invalidateByPattern(String pattern);
    CacheStatistics getStatistics();
}
```

## 5. API Endpoints

### 5.1 Tracking Operations

```
POST   /api/v1/tracking/register
POST   /api/v1/tracking/batch-register
GET    /api/v1/tracking/{trackingId}
GET    /api/v1/tracking/{trackingId}/events
GET    /api/v1/tracking/{trackingId}/refresh
POST   /api/v1/tracking/search
DELETE /api/v1/tracking/{trackingId}
```

### 5.2 Sync Management

```
POST   /api/v1/sync/schedule/{trackingId}
POST   /api/v1/sync/execute/{jobId}
GET    /api/v1/sync/jobs
GET    /api/v1/sync/statistics
POST   /api/v1/sync/retry/{jobId}
```

### 5.3 Cache Management

```
DELETE /api/v1/cache/{trackingId}
DELETE /api/v1/cache/pattern
POST   /api/v1/cache/warm
GET    /api/v1/cache/statistics
```

### 5.4 Carrier Operations

```
GET    /api/v1/carriers
GET    /api/v1/carriers/{carrierCode}
POST   /api/v1/carriers/detect
```

## 6. Performance Requirements

### 6.1 Database Performance

- Query response time: < 100ms (indexed queries)
- Bulk insert: 1000 events/second
- Connection pool: 20-50 connections
- Partition large tables (events, audit logs) by date

### 6.2 Cache Performance

- Cache hit ratio: > 80%
- Cache response time: < 10ms
- Redis memory: 2-4GB for 100K tracking records

### 6.3 Sync Performance

- Process 100 tracking numbers per minute
- Batch size: 50 items per API call
- Concurrent sync jobs: 10-20 threads
- Job queue processing: < 5 second delay

## 7. Monitoring & Observability

### 7.1 Key Metrics

- Active tracking records count
- Sync job success/failure rate
- API response times (p50, p95, p99)
- Cache hit/miss ratio
- 17-track API rate limit usage
- Database connection pool usage

### 7.2 Alerts

- Sync failure rate > 10%
- Cache hit ratio < 70%
- Database connection pool exhaustion
- API rate limit approaching (> 80%)
- Job queue backlog > 1000 items

## 8. Data Retention Policies

### 8.1 Retention Rules

- **Active Tracking Records**: Indefinite (until manually deleted)
- **Delivered Packages**: 90 days (configurable)
- **Tracking Events**: 180 days
- **Sync Jobs**: 30 days
- **Audit Logs**: 365 days (compliance requirement)
- **Cache Entries**: TTL-based (15min - 24 hours)
- **Failed Jobs**: 7 days in DLQ

### 8.2 Archival Strategy

- Archive delivered records > 90 days to cold storage
- Compress event history for archived records
- Maintain indexes only on active data

## 9. Implementation Roadmap

### Phase 1: Core Database & Infrastructure (Week 1-2)

- Create database schema with all tables
- Set up Flyway migrations
- Configure PostgreSQL with proper indexes
- Set up Redis with key structures
- Implement connection pooling

### Phase 2: Core Services (Week 3-4)

- Implement TrackingService with CRUD operations
- Build 17-track API client with retry logic
- Implement CacheService with TTL strategies
- Build carrier detection logic
- Create event persistence layer

### Phase 3: Sync Workflow (Week 5-6)

- Implement SyncService and job scheduler
- Build priority queue with Spring Scheduler
- Add retry logic with exponential backoff
- Implement batch processing
- Add sync job monitoring

### Phase 4: Advanced Features (Week 7-8)

- Implement notification system
- Build audit logging
- Add search and filtering
- Implement cache warming
- Performance optimization

### Phase 5: Testing & Deployment (Week 9-10)

- Unit tests (80%+ coverage)
- Integration tests with TestContainers
- Load testing (1000 concurrent operations)
- Database migration testing
- Production deployment

## 10. Success Criteria

### 10.1 Functional Success

- ✅ Track 100,000+ waybills simultaneously
- ✅ Support top 50 carriers with auto-detection
- ✅ 95%+ accuracy in status updates
- ✅ Complete event history for all packages
- ✅ Zero data loss during failures

### 10.2 Performance Success

- ✅ < 200ms API response time (cached)
- ✅ > 80% cache hit ratio
- ✅ Process 100 sync jobs/minute
- ✅ < 5% sync job failure rate
- ✅ 99.5% uptime

### 10.3 Operational Success

- ✅ Automated sync with zero manual intervention
- ✅ Self-healing retry mechanisms
- ✅ Comprehensive monitoring and alerting
- ✅ Audit trail for all operations
- ✅ Scalable to 1M+ tracking records

## 11. Risk Mitigation

### 11.1 Technical Risks

|Risk|Impact|Mitigation|
|---|---|---|
|17-track API downtime|High|Implement circuit breaker, queue jobs, use cached data|
|Database performance degradation|High|Table partitioning, index optimization, read replicas|
|Memory overflow from large result sets|Medium|Pagination, streaming queries, connection limits|
|Cache inconsistency|Medium|TTL-based invalidation, version tracking|
|Sync job backlog|Medium|Priority queuing, auto-scaling, load shedding|

### 11.2 Operational Risks

|Risk|Impact|Mitigation|
|---|---|---|
|API rate limit exhaustion|High|Rate limiting, request queuing, backoff strategies|
|Data retention compliance|High|Automated archival, audit logging, retention policies|
|Monitoring blind spots|Medium|Comprehensive metrics, distributed tracing|
|Deployment failures|Medium|Blue-green deployment, rollback procedures|

## 12. Open Questions

1. **Carrier Priority**: Should premium carriers (UPS, FedEx) have higher sync priority?
2. **Data Sovereignty**: Are there regional data storage requirements?
3. **Webhook Support**: Should we support real-time webhooks from 17-track?
4. **Multi-Tenancy**: Future requirement for tenant isolation?
5. **SLA Requirements**: What are acceptable downtime windows for maintenance?
6. **Cost Optimization**: Budget for 17-track API calls (pricing per request)?
7. **Scalability Target**: Expected growth rate for next 12 months?