---
title: 使用 C# 從預覽環境查詢資料 - Azure 時間序列見解 | Microsoft Docs
description: 了解如何使用以 C# 撰寫的應用程式，從 Azure 時間序列見解環境查詢資料。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81379819"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>使用 C# 從 Azure 時間序列見解預覽環境查詢資料

此 C# 範例示範如何從 Azure 時間序列見解預覽環境中的[預覽資料存取 API](https://docs.microsoft.com/rest/api/time-series-insights/preview) 查詢資料。

> [!TIP]
> 請檢視 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample) 中的預覽 C# 程式碼範例。

## <a name="summary"></a>摘要

以下範例程式碼會示範下列功能：

* [Azure AutoRest](https://github.com/Azure/AutoRest) 中的 SDK 自動產生支援。
* 如何使用 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 透過 Azure Active Directory 取得存取權杖。
* 如何在後續資料存取 API 要求的 `Authorization` 標頭中傳遞該取得的存取權杖。 
* 本範例會提供主控台介面，其示範如何針對下列項目提出 HTTP 要求：

    * [預覽環境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis) (機器翻譯)
        * [取得環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) (機器翻譯) 和[取得事件結構描述 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema) (機器翻譯)
    * [預覽查詢 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis) (機器翻譯)
        * [取得事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) (機器翻譯)、[取得數列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries) (機器翻譯) 和[取得彙總數列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries) (機器翻譯)
    * [時間序列模型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries) (機器翻譯)
        * [取得階層 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) (機器翻譯) 和[階層批次 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch) (機器翻譯)
        * [取得類型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) (機器翻譯) 和[類型批次 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch) (機器翻譯)
        * [取得執行個體 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) (機器翻譯) 和[執行個體批次 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch) (機器翻譯)
* 進階[搜尋](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) (機器翻譯) 和 [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) (機器翻譯) 功能。

## <a name="prerequisites-and-setup"></a>先決條件和設定

編譯及執行範例程式碼之前，您必須先完成下列步驟：

1. [佈建 Azure 時間序列見解預覽](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) (機器翻譯) 環境。
1. 針對 Azure Active Directory 設定 Azure 時間序列見解環境，如[驗證和授權](time-series-insights-authentication-and-authorization.md)中所述。 
1. 執行 [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) 中所指定的 [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat)，以產生時間序列見解預覽用戶端相依性。
1. 在 Visual Studio 中開啟 `TSIPreviewDataPlaneclient.sln` 方案，並將 `DataPlaneClientSampleApp` 設定為預設專案。
1. 使用[以下](#project-dependencies)所述步驟來安裝必要的專案相依性，並將範例編譯為可執行檔 `.exe`。
1. 按兩下 `.exe` 檔案加以執行。

## <a name="project-dependencies"></a>專案相依性

建議使用最新版的 Visual Studio：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Version 16.4.2+

本範例程式碼有數個必要的相依性，您可在 [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) 檔案中檢視這些相依性。

選取 [建置] > [建置方案]，以下載 Visual Studio 2019 中的套件。 

或者，使用 [NuGet 2.12+](https://www.nuget.org/) 新增每個套件。 例如：

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 範例程式碼

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * 您可在不改變預設環境變數的情況下執行此程式碼範例。
> * 此程式碼範例會編譯為 .NET 可執行檔主控台應用程式。

## <a name="next-steps"></a>後續步驟

- 若要深入了解查詢，請參閱[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/preview-query) (機器翻譯)。

- 請參閱如何[使用用戶端 SDK 將 JavaScript 應用程式連線到時間序列見解](https://github.com/microsoft/tsiclient) (英文)。
