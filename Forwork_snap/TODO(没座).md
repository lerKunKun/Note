
![[Pasted image 20250512162453.png]]
# Webhook实现shopify订单实时同步

### 常见的订单相关事件：

|事件类型|描述|
|---|---|
|`orders/create`|新订单创建（下单）时触发|
|`orders/paid`|订单支付成功时触发|
|`orders/fulfilled`|订单发货时触发|
|`orders/updated`|订单更新时触发|
|`orders/cancelled`|订单被取消时触发|
|`orders/partially_fulfilled`|部分发货时触发|


实时数据可视化看板
spend CPM CPC CTR Rev ROI CPA

| 中文名      | 英文全称                       | 缩写         | 说明                                                                                      | 公式（注释）                               |
| -------- | -------------------------- | ---------- | --------------------------------------------------------------------------------------- | ------------------------------------ |
| 消耗（广告花费） | **Ad Spend** 或 **Ad Cost** | spend/cost | 广告主在一段时间内为投放广告所实际花费的金额                                                                  | 广告花费的金额                              |
| 每千次展示费用  | Cost Per Mille             | CPM        | 每1000次展示所需费用                                                                            | ![[Pasted image 20250513094513.png]] |
| 每次点击费用   | Cost Per Click             | CPC        | 每次点击所需费用                                                                                | ![[Pasted image 20250513094527.png]] |
| 点击率      | Click Through Rate         | CTR        | 点击数 / 展示数 × 100%                                                                        | ![[Pasted image 20250513094639.png]] |
| 销售额      | Revenue                    | Rev        | 广告带来的销售总金额                                                                              | 广告的总销售金额                             |
| 投资回报率    | Return On Investment       | ROI        | （销售额 - 成本）/ 成本 × 100%                                                                   | ![[Pasted image 20250513094705.png]] |
| 每次行动成本   | **Cost Per Action**        | CPA        | 假设你在 Snapchat 上投放广告，总共花费了 $500 美元，获得了 50 个订单（每个用户下单一次）<br>说明你每获取一个订单（一次转化），平均花费了 10 美元。 | CPA = 总广告花费 ÷ 转化次数（Action 数）         |
| 广告支出回报率  | Return on Ad Spend         | ROAS       | 通常用比例或倍数表示，例如 ROAS = 4，表示你花了 1 美元广告费，带来了 4 美元销售收入                                       | ROAS = 广告带来的收入 ÷ 广告花费                |
cmd /c mklink /d "C:\Users\1\AppData\Local\Programs\Cursor" "D:\developtools\cursor"

Cost Per Action -> total spend / total number order (per product ? per shop)

per shop 

qu order -> shop id <- qu advertising_api (platform_id)

? new order
? paid

spend -> shop id 

