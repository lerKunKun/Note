
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
spend CPM CPC CTR Rev ROI

| 中文名      | 英文全称                       | 缩写         | 说明                     | 公式（注释）                               |
| -------- | -------------------------- | ---------- | ---------------------- | ------------------------------------ |
| 消耗（广告花费） | **Ad Spend** 或 **Ad Cost** | spend/cost | 广告主在一段时间内为投放广告所实际花费的金额 | 广告花费的金额                              |
| 每千次展示费用  | Cost Per Mille             | CPM        | 每1000次展示所需费用           | ![[Pasted image 20250513094513.png]] |
| 每次点击费用   | Cost Per Click             | CPC        | 每次点击所需费用               | ![[Pasted image 20250513094527.png]] |
| 点击率      | Click Through Rate         | CTR        | 点击数 / 展示数 × 100%       | ![[Pasted image 20250513094639.png]] |
| 销售额      | Revenue                    | Rev        | 广告带来的销售总金额             | 广告的总销售金额                             |
| 投资回报率    | Return On Investment       | ROI        | （销售额 - 成本）/ 成本 × 100%  | ![[Pasted image 20250513094705.png]] |
