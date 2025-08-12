t-star 为例
## 1. 选择字体文件格式

- **.woff2** → 推荐主力格式（体积小、加载快、现代浏览器支持好）
    
- **.woff** → 兼容旧浏览器（可选）


## 2. 上传到 Shopify 主题

1. 在 Shopify 后台进入 **Online Store → Themes → Edit code**
    
2. 找到并进入 **Assets** 文件夹
    
3. 点击 **Add a new asset**，上传你的字体文件（`Tstar.woff2` 和 `Tstar.woff`）

## 3. 定义字体（CSS）

在你的主题 CSS 文件（如 `base.css` 或 `theme.css`）开头添加：
```CSS
@font-face {
  font-family: 'Tstar';
  src: url('{{ "Tstar.woff2" | asset_url }}') format('woff2'),
       url('{{ "Tstar.woff" | asset_url }}') format('woff');
  font-weight: normal;
  font-style: normal;
  font-display: swap;
}

```
## 4. 应用字体

假设你要把网站的主字体换成 Tstar，可以在 CSS 里加：


```css
`body {   font-family: 'Tstar', sans-serif; }`
```

如果只想改标题（`h1, h2, h3`）：

```css
`h1, h2, h3 {   font-family: 'Tstar', sans-serif; }`
```

## 5. 清缓存 & 测试

- 保存修改
    
- 刷新前端（建议用 Ctrl+F5 强制刷新）
    
- 检查字体是否生效