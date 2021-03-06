---
title: 監視任何環境上的 JAVA 應用程式-Azure 監視器 Application Insights
description: 應用程式效能監視，適用于在任何環境中執行的 JAVA 應用程式，不需檢測應用程式。 分散式追蹤和應用程式對應。
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 3e3d108603ad6210143deea58049ff7b230bb6fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85319698"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>JAVA 無程式碼應用程式監視 Azure 監視器 Application Insights-公開預覽

監視 Java 無程式碼應用程式的重點在於簡單明瞭：不需進行任何程式碼變更，只要進行數個設定變更，就能啟用該 Java 代理程式。

 JAVA 代理程式適用于任何環境，並可讓您監視所有的 JAVA 應用程式。 換句話說，不論您是在內部部署的 Vm 上執行 JAVA 應用程式，在 AKS 中，在 Windows 上，Linux-您將它命名為，JAVA 3.0 代理程式會監視您的應用程式。

不再需要將 Application Insights JAVA SDK 新增至您的應用程式，因為3.0 代理程式會自行 autocollects 要求、相依性和記錄。

您仍然可以從您的應用程式傳送自訂遙測。 3.0 代理程式會追蹤並使其與所有實驗自動收集的遙測相互關聯。

## <a name="quickstart"></a>快速入門

**1. 下載代理程式**

下載[applicationinsights-agent-3.0.0-preview. 5 .jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

**2. 將 JVM 指向代理程式**

將新增 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.5.jar` 至應用程式的 JVM 引數

一般 JVM 引數包括 `-Xmx512m` 和 `-XX:+UseG1GC` 。 因此，如果您知道要將這些新增到何處，您就已經知道要將它加入何處。

如需設定應用程式 JVM 引數的其他協助，請參閱[3.0 Preview：更新 JVM 引數的秘訣](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments)。

**3. 將代理程式指向您的 Application Insights 資源**

如果您還沒有 Application Insights 資源，您可以遵循[資源建立指南](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)中的步驟來建立新的資源。

藉由設定環境變數，將代理程式指向您的 Application Insights 資源：

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

或者，藉由建立名為的設定檔 `ApplicationInsights.json` ，並將它放在與相同的目錄中 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` ，並包含下列內容：

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

您可以在 Application Insights 資源中找到您的連接字串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 連接字串":::

**4. 這樣就大功告成了！**

現在啟動您的應用程式，並移至 Azure 入口網站中的 Application Insights 資源，以查看您的監視資料。

> [!NOTE]
> 可能需要幾分鐘的時間，您的監視資料才會顯示在入口網站中。


## <a name="configuration-options"></a>設定選項

在檔案中 `ApplicationInsights.json` ，您可以另外設定：

* 雲端角色名稱
* 雲端角色執行個體
* 應用程式記錄檔捕獲
* JMX 計量
* Micrometer
* Heartbeat
* 取樣
* HTTP Proxy
* 自我診斷

請參閱[3.0 公開預覽：設定選項](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config)的詳細資料。

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>實驗自動收集要求、相依性、記錄和計量

### <a name="requests"></a>Requests

* JMS 取用者
* Kafka 取用者
* Netty/WebFlux
* Servlets
* 春季排程

### <a name="dependencies-with-distributed-trace-propagation"></a>分散式追蹤傳播的相依性

* Apache HttpClient 和 HttpAsyncClient
* gRPC
* HttpURLConnection
* JMS
* Kafka
* Netty 用戶端
* OkHttp

### <a name="other-dependencies"></a>其他相依性

* Cassandra
* JDBC
* MongoDB （非同步和同步）
* Redis （萵苣和 Jedis）

### <a name="logs"></a>記錄

* util。記錄
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>計量

* Micrometer （包括春季開機傳動標準）
* JMX 計量

## <a name="sending-custom-telemetry-from-your-application"></a>從您的應用程式傳送自訂遙測

我們在 3.0 + 的目標是要讓您使用標準 Api 來傳送自訂遙測。

我們支援 Micrometer、OpenTelemetry API 和熱門的記錄架構。 Application Insights JAVA 3.0 會自動捕捉遙測，並使其與所有實驗自動收集的遙測相互關聯。

基於這個理由，我們目前不打算發行具有 Application Insights 3.0 的 SDK。

Application Insights JAVA 3.0 已在接聽傳送至 Application Insights JAVA SDK 2.x 的遙測。 這項功能是現有2.x 使用者升級案例的重要部分，而且在 OpenTelemetry API 正式運作之前，會在自訂遙測支援中填滿一個重要的差距。

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>使用 Application Insights JAVA SDK 2.x 傳送自訂遙測

將新增 `applicationinsights-core-2.6.0.jar` 至您的應用程式（Application Insights JAVA 3.0 支援所有2.x 版本，但如果您有選擇，則值得使用最新版本）：

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

建立 TelemetryClient：

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

並使用它來傳送自訂遙測。

### <a name="events"></a>事件

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>計量

您可以透過[Micrometer](https://micrometer.io)傳送計量遙測：

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

或者，您也可以使用 Application Insights JAVA SDK 2.x：

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>相依性

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>記錄
您可以透過您最愛的記錄架構傳送自訂記錄檔遙測。

或者，您也可以使用 Application Insights JAVA SDK 2.x：

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>例外狀況
您可以透過您最愛的記錄架構傳送自訂的例外狀況遙測。

或者，您也可以使用 Application Insights JAVA SDK 2.x：

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>從 Application Insights JAVA SDK 2.x 升級

如果您已在應用程式中使用 Application Insights JAVA SDK 2.x，則不需要將它移除。 JAVA 3.0 代理程式將會偵測到它，並捕捉您透過 JAVA SDK 2.x 傳送的任何自訂遙測，同時抑制 JAVA SDK 2.x 所執行的任何 autocollection，以防止重複的捕捉。

> [!NOTE]
> 注意：使用3.0 代理程式時，將不會執行 JAVA SDK 2.x TelemetryInitializers 和 TelemetryProcessors。
