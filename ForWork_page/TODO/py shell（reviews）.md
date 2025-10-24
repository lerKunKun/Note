

### csv style

| product_handle | rating | author | email | body | created_at | photo_url | verified_purchase | title |
| -------------- | ------ | ------ | ----- | ---- | ---------- | --------- | ----------------- | ----- |
|                |        |        |       |      |            |           |                   |       |

### MVP 
csvmaker -> version1.0

- [ ]  POC
```
suggestion:




```

1. 生成携带部分数据csv
```
pront
product_handle rating author email body created_at photo_url verified_purchase title  
此为csv表头，verified_purchase列全为TRUE，rating为3-5之间生成随机整数python代码（4，5权重80%以上），email随机生成邮箱比如johnd@johnd.com，created_at列全生成2024-09-22 13:33:50 UTC这种格式，最近一年的随机时间，凌晨1-5点权重20%，2025年权重70%以上，剩下列全空着，做一个csv生成脚本
```

3. 八爪鱼导出csv文件和生成模板csv合并
4. 简易web或exe
### 拓展功能
1. 每日闲时动态拉取评论生成csv
2. 自动导入拉取的csv，AI 接入判断评论性质（好差评），差评重写优化，改星（rating）
