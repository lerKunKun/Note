# 跨境电商业务中台 - 快速参考卡片

> 一页纸了解整个系统 | 开发与运维必备

---

## 📌 系统概览

| 项目信息 | 详情 |
|:---|:---|
| **项目代号** | BIOU DATA CENTER |
| **英文名称** | E-commerce Middle Platform (EMP) |
| **技术栈** | Vue.js/React + FastAPI/Spring Boot + Casbin + MySQL + Redis |
| **核心价值** | 统一入口、权限中台化、环境安全隔离、数据闭环 |
| **MVP 周期** | 10-12 周 |

---

## 🎯 核心功能速查表

| 模块 | 功能 | 技术要点 | 优先级 |
|:---|:---|:---|:---:|
| **身份认证** | DingTalk 扫码登录 | OAuth 2.0 + 白名单校验 | P0 |
| **权限管理** | Casbin RBAC | gRPC 鉴权服务 + 策略热加载 | P0 |
| **产品库** | 一键刊登 | JSON 模板引擎 + Shopify API | P0 |
| **订单管理** | Webhook 同步 | 幂等性校验 + 补偿轮询 | P0 |
| **物流回传** | 混合模式 | HubStudio 环境 + API 直连 | P0 |
| **供应链** | 采购流程 | 状态机 + 审批流 | P1 |
| **财务报表** | 利润核算 | 多维度聚合 + 可视化 | P1 |
| **Hub 管理** | 环境控制 | Local API 调用 | P1 |

---

## 🔗 API 端点清单

### 认证授权
```
POST   /api/auth/dingtalk/callback   # 钉钉回调
GET    /api/auth/user/info           # 获取用户信息
POST   /api/auth/logout              # 登出
```

### 产品管理
```
GET    /api/products                 # 产品列表
POST   /api/products                 # 创建产品
PUT    /api/products/{id}            # 更新产品
DELETE /api/products/{id}            # 删除产品
POST   /api/products/{id}/publish    # 一键刊登
```

### 订单管理
```
GET    /api/orders                   # 订单列表
POST   /api/webhooks/shopify/orders  # Shopify Webhook 接收
POST   /api/orders/{id}/fulfill      # 物流回传
```

### 权限管理
```
GET    /api/permissions/roles        # 角色列表
POST   /api/permissions/policies     # 创建策略
POST   /api/casbin/enforce           # 鉴权检查（内部）
```

### HubStudio 集成
```
GET    /api/hubstudio/environments   # 环境列表
POST   /api/hubstudio/open/{env_id}  # 启动环境
GET    /api/hubstudio/status         # 服务状态
```

---

## 🔐 权限配置快速参考

### Casbin 策略格式
```
p, <subject>, <object>, <action>
```

### 常用策略示例
```ini
# 角色定义
g, user:alice, role:ops          # 用户 alice 拥有 ops 角色
g, role:ops, role:employee       # ops 继承 employee

# 资源权限
p, role:ops, /api/products, write         # 运营可写产品
p, role:finance, /api/reports, read       # 财务可读报表
p, role:admin, *, *                       # 管理员全权限
```

### 角色与权限映射
| 角色 | 权限范围 | 典型操作 |
|:---|:---|:---|
| **admin** | 所有资源 | 系统配置、权限管理 |
| **ops** | 产品、订单 | 刊登、订单处理 |
| **purchase** | 供应链 | 采购下单 |
| **logistics** | 物流 | 运单回传 |
| **finance** | 报表（只读） | 查看利润 |

---

## 🛠️ 常见问题排查指南

### 问题：用户登录失败
**可能原因**：
1. 不在白名单内
2. DingTalk AppKey 配置错误
3. Redis Session 过期

**排查步骤**：
```bash
# 1. 检查用户是否在白名单
SELECT * FROM users WHERE dingtalk_id = 'xxx';

# 2. 检查 DingTalk 配置
cat config/dingtalk.yaml

# 3. 检查 Redis 连接
redis-cli ping
```

---

### 问题：权限验证失败
**可能原因**：
1. Casbin 策略未配置
2. Casbin Server 不可用
3. 缓存未刷新

**排查步骤**：
```bash
# 1. 检查策略是否存在
curl -X POST http://casbin-server/api/enforce \
  -d '{"sub":"user:alice","obj":"/api/products","act":"read"}'

# 2. 检查 Casbin Server 状态
systemctl status casbin-server

# 3. 清除缓存
redis-cli DEL "casbin:policy:*"
```

---

### 问题：订单未同步
**可能原因**：
1. Shopify Webhook 未配置
2. Webhook 签名验证失败
3. 订单已存在（幂等性）

**排查步骤**：
```bash
# 1. 检查 Webhook 日志
tail -f logs/webhook.log

# 2. 验证 Shopify Webhook 配置
curl https://{shop}.myshopify.com/admin/api/2024-01/webhooks.json \
  -H "X-Shopify-Access-Token: {token}"

# 3. 检查订单是否已存在
SELECT * FROM orders WHERE shopify_order_id = 'xxx';
```

---

### 问题：HubStudio 环境无法启动
**可能原因**：
1. 本地服务未运行
2. 环境 ID 不存在
3. 网络连接问题

**排查步骤**：
```bash
# 1. 检查 HubStudio Local API 是否运行
curl http://localhost:50005/api/v1/browser/active

# 2. 验证环境 ID
curl http://localhost:50005/api/v1/browser/list

# 3. 检查网络连接
ping hub-api-server
```

---

### 问题：刊登失败
**可能原因**：
1. Shopify Token 过期
2. 产品数据不完整
3. API Rate Limit

**排查步骤**：
```bash
# 1. 验证 Token
curl https://{shop}.myshopify.com/admin/api/2024-01/shop.json \
  -H "X-Shopify-Access-Token: {token}"

# 2. 检查产品必填字段
SELECT * FROM products WHERE sku = 'xxx';

# 3. 查看 API 限流状态
grep "429" logs/shopify_api.log
```

---

## 📊 监控指标阈值

| 指标 | 正常值 | 告警阈值 | 紧急阈值 |
|:---|:---:|:---:|:---:|
| **系统可用性** | > 99.5% | < 99% | < 95% |
| **API P99 响应时间** | < 300ms | > 500ms | > 1000ms |
| **Casbin 鉴权延迟** | < 10ms | > 20ms | > 50ms |
| **订单同步延迟** | < 3s | > 5s | > 10s |
| **CPU 使用率** | < 50% | > 70% | > 90% |
| **内存使用率** | < 60% | > 80% | > 95% |
| **数据库连接数** | < 50 | > 80 | > 100 |
| **Redis 命中率** | > 90% | < 80% | < 70% |

---

## 🚨 紧急联系方式

### 技术支持
```
钉钉群：BIOU 中台技术支持群
值班电话：_待填写_
紧急联系人：_待填写_
```

### 第三方服务支持
| 服务 | 支持渠道 |
|:---|:---|
| **Shopify** | https://help.shopify.com |
| **HubStudio** | _厂商技术支持群_ |
| **DingTalk** | https://open.dingtalk.com |

---

## 💻 关键命令速查

### 服务管理
```bash
# 启动所有服务
docker-compose up -d

# 查看日志
docker-compose logs -f [service_name]

# 重启服务
systemctl restart emp-backend
systemctl restart casbin-server

# 查看服务状态
systemctl status emp-backend
```

### 数据库操作
```bash
# 连接数据库
mysql -u root -p emp_db

# 备份数据库
mysqldump -u root -p emp_db > backup_$(date +%Y%m%d).sql

# 恢复数据库
mysql -u root -p emp_db < backup_20251208.sql

# 查看慢查询
mysql -u root -p -e "SELECT * FROM mysql.slow_log ORDER BY query_time DESC LIMIT 10;"
```

### Redis 操作
```bash
# 连接 Redis
redis-cli

# 查看所有 key
KEYS *

# 清除缓存
FLUSHDB

# 查看内存使用
INFO memory
```

### 日志查看
```bash
# 实时查看应用日志
tail -f /var/log/emp/app.log

# 查看错误日志
grep "ERROR" /var/log/emp/app.log

# 统计今日错误数量
grep "ERROR" /var/log/emp/app.log | grep $(date +%Y-%m-%d) | wc -l

# 查看 Webhook 日志
tail -f /var/log/emp/webhook.log
```

---

## 🔄 常用业务流程

### 新增店铺流程
```
1. 在 Shopify 生成 API Token（Admin API 权限）
2. 在中台「店铺管理」创建店铺记录
3. 配置 Webhook（订单创建、订单更新）
4. 绑定 HubStudio 环境 ID（如需）
5. 测试订单同步
```

### 新增用户流程
```
1. 将用户添加到钉钉企业通讯录
2. （可选）将钉钉 ID 添加到白名单
3. 用户使用钉钉扫码登录（自动创建账号）
4. 管理员分配角色和权限
5. 用户刷新页面生效
```

### 产品刊登流程
```
1. 在「产品库」创建产品（填写 SKU、标题、图片）
2. 选择详情页模板
3. 勾选目标店铺
4. 点击「一键刊登」
5. 系统返回 Shopify Product ID
```

### 订单处理流程
```
1. 客户在 Shopify 下单
2. Webhook 自动推送到中台
3. 中台入库订单数据
4. 运营人员录入物流单号
5. 系统通过 HubStudio/API 回传 Shopify
6. 客户收到发货通知
```

---

## 📚 快速链接

| 资源 | 链接 |
|:---|:---|
| **PRD 文档** | [BIOU DATA CENTER.md](file:///d:/develop/PRD-Collections/BIOU%20DATA%20CENTER.md) |
| **实施检查清单** | [implementation_checklist.md](file:///C:/Users/Administrator/.gemini/antigravity/brain/e8e00d47-3ea7-4c0b-84eb-bfd3180adb1a/implementation_checklist.md) |
| **API 文档** | _待部署 Swagger UI_ |
| **Shopify API 文档** | https://shopify.dev/api |
| **Casbin 文档** | https://casbin.org/docs |
| **钉钉开放平台** | https://open.dingtalk.com |

---

**快速参考卡片版本**: V1.0  
**更新日期**: 2025-12-08  
**适用人员**: 开发、测试、运维、运营人员
