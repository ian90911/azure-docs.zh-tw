---
title: 診斷記錄
titleSuffix: Azure Cognitive Services
description: 本指南提供逐步指示，說明如何啟用 Azure 認知服務的診斷記錄。 這些記錄會提供有關用於問題識別和偵測之資源作業的豐富、經常性資料。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "71827907"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>啟用 Azure 認知服務的診斷記錄

本指南提供逐步指示，說明如何啟用 Azure 認知服務的診斷記錄。 這些記錄會提供有關用於問題識別和偵測之資源作業的豐富、經常性資料。 在繼續之前，您必須擁有至少有一個認知服務訂用帳戶的 Azure 帳戶，例如[Bing Web 搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview)、[語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)或[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)。

## <a name="prerequisites"></a>先決條件

若要啟用診斷記錄，您將需要一個位置來儲存記錄資料。 本教學課程使用 Azure 儲存體和 Log Analytics。

* [Azure 儲存體](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs)-保留用於原則 audit、靜態分析或備份的診斷記錄。 儲存體帳戶不一定要和資源發出記錄屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -彈性的記錄搜尋和分析工具，可讓您分析 Azure 資源所產生的原始記錄。

> [!NOTE]
> 還有其他設定選項可供使用。 若要深入瞭解，請參閱[收集並取用來自 Azure 資源的記錄資料](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)。

## <a name="enable-diagnostic-log-collection"></a>啟用診斷記錄收集  

讓我們從使用 Azure 入口網站啟用診斷記錄開始。

> [!NOTE]
> 若要使用 PowerShell 或 Azure CLI 啟用此功能，請使用[從 Azure 資源收集和取用記錄資料](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)中提供的指示。

1. 瀏覽至 Azure 入口網站。 然後，找出並選取 [認知服務資源]。 例如，您要 Bing Web 搜尋的訂用帳戶。   
2. 接下來，從左側導覽功能表中找出 [**監視**]，然後選取 [**診斷設定**]。 此畫面包含先前為此資源建立的所有診斷設定。
3. 如果先前建立的資源是您想要使用的，您現在可以選取它。 否則，請選取 [ **+ 新增診斷設定**]。
4. 輸入設定的名稱。 然後選取 [封存**至儲存體帳戶**] 並**傳送至 log Analytics**。
5. 當系統提示您設定時，請選取您想要用來儲存診斷記錄的儲存體帳戶和 OMS 工作區。 **注意**：如果您沒有儲存體帳戶或 OMS 工作區，請依照提示來建立一個。
6. 選取 [ **Audit**]、[**要求回應**] 和 [ **AllMetrics**]。 然後設定診斷記錄資料的保留期限。 如果保留原則設定為零，則會無限期儲存該記錄類別的事件。
7. 按一下 **[儲存]** 。

最多可能需要兩個小時的時間，記錄資料才可供查詢和分析。 如果您沒有立即看到任何專案，請不要擔心。

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>從 Azure 儲存體查看和匯出診斷資料

Azure 儲存體是健全的物件儲存體解決方案，已針對儲存大量非結構化資料進行優化。 在本節中，您將瞭解如何在30天的時間範圍內查詢儲存體帳戶的總交易，並將資料匯出至 excel。

1. 從 [Azure 入口網站] 中，找出您在上一節中建立的 Azure 儲存體資源。
2. 從左側導覽功能表中，尋找 [**監視**]，然後選取 [**計量**]。
3. 使用可用的下拉式清單來設定您的查詢。 在此範例中，讓我們將時間範圍設為 [**過去30天**]，並將度量設定為 [**交易**]。
4. 當查詢完成時，您會看到過去30天內的交易視覺效果。 若要匯出此資料，請使用位於頁面頂端的 [**匯出至 Excel** ] 按鈕。

深入瞭解您可以在[Azure 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)中使用診斷資料執行的動作。

## <a name="view-logs-in-log-analytics"></a>檢視 Log Analytics 中的記錄

請遵循這些指示來探索資源的 log analytics 資料。

1. 從 [Azure 入口網站] 中，找出並從左側導覽功能表中選取 [ **Log Analytics** ]。
2. 找出並選取您在啟用診斷時所建立的資源。
3. 在 **[一般**] 底下，找出並選取 [**記錄**]。 在此頁面中，您可以對記錄執行查詢。

### <a name="sample-queries"></a>範例查詢

以下是您可以用來流覽記錄資料的幾個基本 Kusto 查詢。

在一段指定的時間內，從 Azure 認知服務的所有診斷記錄執行此查詢：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

執行此查詢以查看10個最新的記錄檔：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

執行此查詢，依**資源**將作業分組：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
執行此查詢，以找出執行作業所需的平均時間：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

執行此查詢，以針對每個10s，依 OperationName 分割一段時間，以分類收納的計數來查看作業量。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何啟用記錄，以及各種 Azure 服務支援的計量和記錄類別，請閱讀 Microsoft Azure 的[計量總覽](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)和[azure 診斷記錄](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)文章的總覽。
* 閱讀下列文章來了解事件中樞：
  * [Azure 事件中樞是什麼？](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [開始使用事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* 閱讀[從 Azure 儲存體下載計量和診斷記錄](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)。
* 閱讀[瞭解 Azure 監視器記錄中的記錄搜尋](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)。
