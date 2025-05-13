## 📈 常用指标（Metrics）
| 字段名                                                       | 说明                              |
| --------------------------------------------------------- | ------------------------------- |
| `impressions`                                             | 展示次数                            |
| `spend`                                                   | 广告花费                            |
| `swipes`                                                  | 上滑次数                            |
| `swipe_up_percent`                                        | 上滑率（swipes / impressions × 100） |
| `clicks`                                                  | 点击次数                            |
| `video_views`                                             | 视频观看次数                          |
| `video_views_15s`                                         | 观看15秒视频的次数                      |
| `quartile_1`                                              | 视频观看至25%的次数                     |
| `quartile_2`                                              | 视频观看至50%的次数                     |
| `quartile_3`                                              | 视频观看至75%的次数                     |
| `view_completion`                                         | 视频观看完成次数                        |
| `video_view_time_millis`                                  | 视频观看总时长（毫秒）                     |
| `avg_screen_time_millis`                                  | 平均屏幕停留时间（毫秒）                    |
| `story_opens`                                             | 故事打开次数                          |
| `story_completes`                                         | 故事完成次数                          |
| `shares`                                                  | 分享次数                            |
| `saves`                                                   | 保存次数                            |
| `total_installs`                                          | 应用安装总数                          |
| `android_installs`                                        | Android 应用安装数                   |
| `ios_installs`                                            | iOS 应用安装数                       |
| `conversion_purchases`                                    | 转化购买次数                          |
| `conversion_purchases_value`                              | 转化购买总金额                         |
| `conversion_add_cart`                                     | 加入购物车次数                         |
| `conversion_start_checkout`                               | 开始结账次数                          |
| `conversion_sign_ups`                                     | 注册次数                            |
| `conversion_page_views`                                   | 页面浏览次数                          |
| `conversion_app_opens`                                    | 应用打开次数                          |
| `conversion_view_content`                                 | 查看内容次数                          |
| `conversion_add_billing`                                  | 添加账单信息次数                        |
| `conversion_searches`                                     | 搜索次数                            |
| `conversion_level_completes`                              | 完成等级次数                          |
| `conversion_save`                                         | 保存次数                            |
| `conversion_share`                                        | 分享次数                            |
| `conversion_invite`                                       | 邀请次数                            |
| `conversion_login`                                        | 登录次数                            |
| `conversion_rate`                                         | 提交评分次数                          |
| `conversion_subscribe`                                    | 订阅次数                            |
| `conversion_start_trial`                                  | 开始试用次数                          |
| `conversion_list_view`                                    | 查看列表次数                          |
| `conversion_custom_event_1` 至 `conversion_custom_event_5` | 自定义事件1至5的次数                     |

## 📊 常用维度（Dimensions）

|字段名|说明|
|---|---|
|`date`|日期|
|`hour`|小时|
|`week`|周|
|`month`|月|
|`quarter`|季度|
|`year`|年|
|`ad_account_id`|广告账户 ID|
|`ad_account_name`|广告账户名称|
|`campaign_id`|活动 ID|
|`campaign_name`|活动名称|
|`ad_squad_id`|广告组 ID|
|`ad_squad_name`|广告组名称|
|`ad_id`|广告 ID|
|`ad_name`|广告名称|
|`ad_type`|广告类型|
|`campaign_objective`|活动目标|
|`placement`|广告展示位置|
|`device`|设备类型|
|`device_os`|设备操作系统|
|`country`|国家|
|`region`|地区|
|`dma`|指定市场区域|
|`gender`|性别|
|`age`|年龄|
|`user_interest`|用户兴趣|
|`call_to_action`|行动号召|
|`creative_id`|创意 ID|
|`creative_name`|创意名称|
|`creative_type`|创意类型|
|`creative_web_view_url`|创意网页查看 URL|
|`creative_deep_link_url`|创意深度链接 URL|

## ⚙️ 报告请求参数说明

在调用 Snapchat Marketing API 的报告接口时，可以使用以下参数：

- `metrics`：要检索的指标列表（如上所列）
    
- `dimensions`：要分组的维度列表（如上所列）
    
- `start_time` 和 `end_time`：数据的起始和结束时间
    
- `granularity`：数据的时间粒度（如 `DAY`、`WEEK`、`MONTH`）
    
- `breakdown`：数据的细分方式（如按广告组、广告等）
    
- `swipe_up_attribution_window`：上滑归因窗口（如 `28d`）
    
- `view_attribution_window`：观看归因窗口（如 `7d`）
    
- `conversion_source_types`：转化来源类型（如 `WEB`、`MOBILE_APP`、`OFFLINE`）


需求
```text
消耗 cpm cpc ctr 销售额 roi
```


```json
/stats 接口
```
通过 **Snapchat Marketing API** 计算以下核心广告指标：

- **消耗（Spend）**
    
- **CPM（每千次展示成本）**
    
- **CPC（每次点击成本）**
    
- **CTR（点击率）**
    
- **销售额（Purchase Value）**
    
- **ROI（投资回报率）**


## ✅ 所需字段说明（来自 API）

|指标|字段名|说明|
|---|---|---|
|消耗|`spend`|广告在报告时间段内花费的总金额（美元）|
|展示|`impressions`|被用户看到广告的次数|
|点击|`swipes`|用户上滑、点击广告的次数（Snapchat 上滑即点击）|
|销售额|`purchase_value`|用户转化后实际购买产生的金额总和|

## 🧮 计算方式
| 指标               | 公式                                         |
| ---------------- | ------------------------------------------ |
| **CPM**（每千次展示成本） | `(spend / impressions) * 1000`             |
| **CPC**（每次点击成本）  | `spend / swipes`                           |
| **CTR**（点击率）     | `(swipes / impressions) * 100`             |
| **ROI**（投资回报率）   | `((purchase_value - spend) / spend) * 100` |


```java
double impressions = record.path("impressions").asDouble();
double spend = record.path("spend").asDouble();
double swipes = record.path("swipes").asDouble();
double purchases = record.path("purchase_value").asDouble();

double cpm = impressions > 0 ? (spend / impressions) * 1000 : 0;
double cpc = swipes > 0 ? (spend / swipes) : 0;
double ctr = impressions > 0 ? (swipes / impressions) * 100 : 0;
double roi = spend > 0 ? ((purchases - spend) / spend) * 100 : 0;
```
