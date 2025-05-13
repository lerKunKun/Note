## Get Ad Account Stats  获取广告账户统计[​](https://developers.snap.com/api/marketing-api/Ads-API/measurement#get-ad-account-stats "Copy to clipboard")

```json
curl "https://adsapi.snapchat.com/v1/adaccounts/22335ba6-7558-4100-9663-baca7adff5f2/stats?granularity=TOTAL&fields=spend" \  
-H "Authorization: Bearer meowmeowmeow"
```

上述命令返回如下结构的 JSON：

```json
{
  "request_status": "success",
  "request_id": "57b24d9c00ff0d85622341e7c60001737e616473617069736300016275676c642b34326313636139312d332d31312d390001010d",
  "total_stats": [
    {
      "sub_request_status": "success",
      "total_stat": {
        "id": "22335ba6-7558-4100-9663-baca7adff5f2",
        "type": "AD_ACCOUNT",
        "granularity": "TOTAL",
        "stats": {
          "spend": 89196290
        },
        "finalized_data_end_time": "2019-08-29T03:00:00.000-07:00"
      }
    }
  ]
}
```
This endpoint retrieves the spend metric for the specified Ad Account, the spend metric is the only metric available for the ad account entity.  
此端点检索指定广告账户的花费指标，花费指标是广告账户实体的唯一指标。