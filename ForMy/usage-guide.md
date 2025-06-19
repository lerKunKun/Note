# Shopify模板水印系统使用指南

## 1. 基础使用方法

### 在Shopify主题中集成

```liquid
<!-- 在theme.liquid的</head>前添加 -->
<script src="{{ 'watermark-system.js' | asset_url }}"></script>
<script>
  // 为每个员工配置不同的参数
  const watermark = new TemplateWatermark({
    employeeId: '{{ settings.employee_id | default: "emp-001" }}',
    version: '{{ settings.theme_version | default: "1.0.0" }}',
    projectId: '{{ shop.name | handle }}-template'
  });
</script>
```

### 直接在HTML中使用

```html
<!DOCTYPE html>
<html>
<head>
  <script src="watermark-system.js"></script>
  <script>
    const watermark = new TemplateWatermark({
      employeeId: 'jane-doe',    // 员工标识
      version: '2.1.3',         // 版本号
      projectId: 'client-abc'   // 项目标识
    });
  </script>
</head>
<body>
  <!-- 你的页面内容 -->
</body>
</html>
```

## 2. 员工版本管理

### 为不同员工分配标识

```javascript
// 员工A的配置
const watermarkA = new TemplateWatermark({
  employeeId: 'emp-001-alice',
  version: '1.0.0',
  projectId: 'shopify-template-2024'
});

// 员工B的配置
const watermarkB = new TemplateWatermark({
  employeeId: 'emp-002-bob',
  version: '1.0.0',
  projectId: 'shopify-template-2024'
});
```

### 批量生成员工版本脚本

```bash
#!/bin/bash
# generate-employee-versions.sh

employees=("alice" "bob" "charlie" "diana")
version="1.0.0"
project="shopify-template-2024"

for emp in "${employees[@]}"; do
  mkdir -p "dist/${emp}"
  cp -r src/* "dist/${emp}/"
  
  # 替换配置
  sed -i "s/EMPLOYEE_ID/${emp}/g" "dist/${emp}/assets/watermark-config.js"
  sed -i "s/VERSION/${version}/g" "dist/${emp}/assets/watermark-config.js"
  sed -i "s/PROJECT_ID/${project}/g" "dist/${emp}/assets/watermark-config.js"
  
  echo "Generated version for ${emp}"
done
```

## 3. 检测被盗模板

### 在浏览器控制台检测

```javascript
// 在怀疑的网站控制台运行
const results = WatermarkDetector.detectWatermarks();
console.log('检测结果:', results);

const analysis = WatermarkDetector.isInternalLeak(results);
console.log('分析结果:', analysis);
```

### 自动化检测脚本

```javascript
// automated-detection.js
function checkSuspiciousWebsite(url) {
  return new Promise((resolve) => {
    const iframe = document.createElement('iframe');
    iframe.style.display = 'none';
    iframe.src = url;
    
    iframe.onload = () => {
      try {
        const doc = iframe.contentDocument;
        const results = WatermarkDetector.detectWatermarks.call(doc);
        const analysis = WatermarkDetector.isInternalLeak(results);
        
        resolve({
          url: url,
          detected: results,
          analysis: analysis
        });
      } catch (e) {
        resolve({ url: url, error: '无法访问或跨域限制' });
      } finally {
        document.body.removeChild(iframe);
      }
    };
    
    document.body.appendChild(iframe);
  });
}

// 使用示例
checkSuspiciousWebsite('https://suspicious-site.com')
  .then(result => console.log(result));
```

## 4. 配置选项详解

### TemplateWatermark 配置参数

```javascript
const config = {
  employeeId: 'string',     // 必需：员工唯一标识
  version: 'string',        // 可选：模板版本号
  projectId: 'string',      // 可选：项目标识
  buildDate: 'datetime'     // 自动生成：构建时间
};
```

### 自定义水印位置

```javascript
class CustomWatermark extends TemplateWatermark {
  addCustomWatermarks() {
    // 在特定位置添加水印
    const productGrid = document.querySelector('.product-grid');
    if (productGrid) {
      productGrid.setAttribute('data-template-id', this.generateFingerprint());
    }
    
    // 在CSS变量中隐藏信息
    document.documentElement.style.setProperty(
      '--internal-trace', 
      `"${this.generateFingerprint()}"`
    );
  }
  
  init() {
    super.init();
    this.addCustomWatermarks();
  }
}
```

## 5. 安全最佳实践

### 混淆配置信息

```javascript
// 使用Base64编码隐藏敏感信息
const obfuscatedConfig = {
  employeeId: atob('YWxpY2UtZGV2LTAwMQ=='), // alice-dev-001
  version: atob('djEuMi4z'),                // v1.2.3
  projectId: atob('c2hvcGlmeS10ZW1wbGF0ZQ==') // shopify-template
};
```

### 动态水印生成

```javascript
// 基于时间戳的动态水印
function generateDynamicWatermark(baseId) {
  const timestamp = Math.floor(Date.now() / 86400000); // 每天变化
  return btoa(`${baseId}-${timestamp}`).slice(0, 12);
}
```

## 6. 报告生成

### 生成检测报告

```javascript
function generateDetectionReport(results, analysis) {
  const report = {
    检测时间: new Date().toISOString(),
    目标网站: window.location.href,
    检测结果: results,
    分析结论: analysis,
    建议行动: analysis.recommendation
  };
  
  console.log('=== 模板盗用检测报告 ===');
  console.table(report);
  
  // 导出为JSON
  const blob = new Blob([JSON.stringify(report, null, 2)], {
    type: 'application/json'
  });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `detection-report-${Date.now()}.json`;
  a.click();
}
```

## 7. 注意事项

1. **隐蔽性**：水印应该不影响网站正常功能
2. **唯一性**：每个员工版本必须有唯一标识
3. **可追溯性**：保留所有分发记录
4. **法律保护**：配合版权声明和法律条款

## 8. 故障排除

### 常见问题

1. **水印不生效**：检查JavaScript是否正确加载
2. **检测失败**：可能遇到跨域限制，使用代理服务器
3. **误报**：调整检测阈值和指标权重

### 调试模式

```javascript
// 开启调试模式
const watermark = new TemplateWatermark({
  employeeId: 'test-user',
  version: '1.0.0',
  debug: true // 在控制台显示详细信息
});
```