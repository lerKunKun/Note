# business-sdk-java


## 系统要求

构建API客户端库需要：
1. Java 1.8+
2. Maven (3.8.3+)/Gradle (7.2+)

## 安装

CAPI Business SDK将在maven中央仓库提供。（对于试点用户，正式发布前请联系我们获取预构建jar包）。请添加以下代码片段来导入依赖项。

### Maven用户

将此依赖项添加到您的项目POM文件中：

```xml
<dependency>
  <groupId>com.snap.business.sdk</groupId>
  <artifactId>capi-java</artifactId>
  <version>1.1.9</version>
  <scope>compile</scope>
</dependency>
```

### Gradle用户

将此依赖项添加到您的项目构建文件中：

```groovy
  repositories {
    mavenCentral()     // 如果'capi-java' jar已发布到maven中央仓库，则需要此配置
    mavenLocal()       // 如果'capi-java' jar已发布到本地maven仓库，则需要此配置
  }

  dependencies {
     implementation "com.snap.business.sdk:capi-java:1.1.9"
  }
```

## 入门指南

请使用以下代码示例作为向Snap转化API发送转化事件的模板。在实际应用中，需要提供更多的转化参数。

```java

// 导入类：
import com.snap.business.sdk.api.ConversionApi;
import com.snap.business.sdk.model.CapiEvent;

import java.util.Arrays;

public class SendEvents {
    private final static String API_TOKEN = "YOUR_API_TOKEN";
    private final static String PIXEL_ID = "YOUR_PIXEL_ID";
    private final static String LAUNCH_PAD_URL = "YOUR_LAUNCH_PAD_URL";

    public static void main(String[] args) {
        ConversionApi capi = new ConversionApi(API_TOKEN);
        // 如果LaunchPad可用，请使用以下构造函数
        // ConversionApi capi = new ConversionApi(API_TOKEN, LAUNCH_PAD_URL);

        // （可选）启用日志记录
        capi.setDebugging(true);

        // 用例1：发送单个事件
        // 构建capi事件
        CapiEvent e1 = new CapiEvent()
                .pixelId(PIXEL_ID)
                .eventConversionType(CapiEvent.EventConversionTypeEnum.WEB)
                .eventType(CapiEvent.EventTypeEnum.CUSTOM_EVENT_1)
                .timestamp("1662655468")
                .uuidC1("mocking-cookie1")
                // 以下PII字段在发送到CAPI之前会通过SHA256进行哈希处理
                .email("usr1@gmail.com")
                .ipAddress("202.117.0.20")
                .phoneNumber("123-456-7890");

        // 异步发送事件
        capi.sendEvent(e1);

        // 用例2：批量发送事件（每个请求最多2000个事件）
        CapiEvent e2 = new CapiEvent()
                .pixelId(PIXEL_ID)
                .eventConversionType(CapiEvent.EventConversionTypeEnum.WEB)
                .eventType(CapiEvent.EventTypeEnum.CUSTOM_EVENT_2)
                .timestamp("1662655468")
                .email("usr1@gmail.com")
                .ipAddress("202.117.0.20")
                .phoneNumber("123-456-7890");

        // 异步批量发送事件
        capi.sendEvents(Arrays.asList(e1, e2));

      // 用例3：发送测试事件
      TestResponse res1 = capi.sendTestEvent(e2);
      System.out.println(res1);

      TestResponse res2 = capi.sendTestEvents(Arrays.asList(e1, e2));
      System.out.println(res2);

      // 获取过去24小时内的测试事件日志
      ResponseLogs res3 = capi.getTestEventLogs(PIXEL_ID);
      System.out.println(res3);

      // 获取过去一小时内的测试事件统计
      ResponseStats res4 = capi.getTestEventStats(PIXEL_ID);
      System.out.println(res4);

    }
}



```

### 初始化ConversionApi
- 如果已在您的域名下设置了Launch Pad，请使用ConversionApi(String longLivedToken, String launchPadUrl)。转化事件将透明地转发给Snap。（其他MPC功能将在后续版本中引入）。
- 否则，您可以使用ConversionApi(String longLivedToken)初始化实例。转化事件将直接从业务SDK发送回Snap。
- 建议为每个线程创建一个专用实例，以避免潜在问题。

### API令牌
- 要使用转化API，您需要使用访问令牌进行身份验证。请查看此处生成令牌。

### 构建CapiEvent
- 请查看[转化参数](https://marketingapi.snapchat.com/docs/conversion.html#additional-data-formatting-guidelines)部分，并在创建CapiEvent对象时提供尽可能多的信息。
- 每个CAPI属性在CapiEvent类中都有一个对应的setter，遵循驼峰命名约定。
- 通过转化API成功发送事件至少需要以下参数之一。如果可能，我们建议传递所有以下参数以提高性能：

  - hashed_email
  - hashed_phone
  - hashed_ip和user_agent
  - hashed_mobile_ad_id

- 任何以"hashed"前缀开头的setter（例如hashedEmail()）仅接受哈希后的PII值（参见[数据卫生](https://marketingapi.snapchat.com/docs/conversion.html#data-hygiene)）。如果您希望业务SDK代表您对PII字段进行标准化和哈希处理，请使用未哈希的setter（例如email()）。
- 我们强烈建议传递cookie1 uuid_c1（如果可用），因为这将提高您的匹配率。如果您使用Pixel SDK，可以通过查看域名下的_scid值来访问第一方cookie。

### 异步发送事件
- 可以通过sendEvent(CapiEvent capiEvent)单独发送转化事件。
- 如果事件在应用程序中已缓冲，可以使用sendEvents(List<CapiEvent> capiEvents)批量报告转化事件。
- 在这两种解决方案中，事件都被封装在异步请求中，您的应用程序不会被阻塞。响应由默认回调记录（在调试模式下）。
- 如果需要自定义回调，请使用sendEvents(List<CapiEvent> capiEvents, ApiCallback<Response> callback)。
- 我们建议向我们发送请求的限制为每秒1000个QPS。每个批处理请求最多可发送2000个事件，因此每秒最多可发送200万个事件。每批发送超过2000个事件将导致400错误。

### 测试事件、日志和统计信息
Snap的转化API提供验证、日志和统计端点，供广告主测试其事件并获取有关每个测试事件处理方式的更多信息。
- 可以通过sendTestEvent(event)发送转化事件进行测试和验证。
- 转化API还提供日志端点。它返回过去一天内发送到测试端点的测试CAPI事件的摘要。
- 转化API的统计端点提供发送的测试事件的基本统计和摘要。
- 更多信息请查看example/SendTestEvents.java。

### 调试模式
- 启用调试模式后，我们将使用[SLF4J](https://www.slf4j.org/manual.html)记录器记录事件、API调用响应和异常。
  - 默认情况下，SLF4J记录器将绑定到[java.util.logging](https://www.slf4j.org/api/org/slf4j/jul/JDK14LoggerAdapter.html)（JUL）并记录到控制台。
  - SLF4J记录器可以在部署时从您的类路径中检测日志框架并绑定日志框架。它可以支持各种日志框架（例如log4j、reload4j、JUL、logback等）。

### 自定义HTTP客户端
- 创建ConversionApi实例时，默认使用OkHttpClient。但是，您可以选择提供自定义客户端作为构造函数的参数。有关更多详细信息，请参阅SendEventsSync中的示例。

如有bug报告、功能增强建议或其他产品反馈，请在GitHub上提交issue。 