---
title: 媒體服務實體的篩選、排序和分頁
titleSuffix: Azure Media Services
description: 深入瞭解 Azure 媒體服務 v3 實體的篩選、排序和分頁。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7e4f1141a9d4bd58451782e8412063a22565556d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80584524"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序和分頁

本主題討論當您在列出 Azure 媒體服務 v3 實體時可用的 OData 查詢選項和分頁支援。

## <a name="considerations"></a>考量

* 屬於類型之實體的屬性 `Datetime` 一律為 UTC 格式。
* 在您傳送要求之前，查詢字串中的空白字元應以 URL 編碼。

## <a name="comparison-operators"></a>比較運算子

您可以使用下列運算子來比較欄位與常數值：

等號比較運算子：

- `eq`：測試欄位是否*等於*常數值。
- `ne`：測試欄位是否*不等於*常數值。

範圍運算子：

- `gt`：測試欄位是否*大於*常數值。
- `lt`：測試欄位是否*小於*常數值。
- `ge`：測試欄位是否*大於或等於*常數值。
- `le`：測試欄位是否*小於或等於*常數值。

## <a name="filter"></a>篩選

使用 `$filter` 提供 OData 篩選參數，只尋找您感興趣的物件。

下列 REST 範例會篩選資產的 `alternateId` 值：

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

下列 c # 範例會篩選資產的建立日期：

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>排序依據

使用 `$orderby` ，依指定的參數排序傳回的物件。 例如：  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

若要以遞增或遞減順序排序結果，請將 `asc` 或附加 `desc` 至功能變數名稱，並以空格分隔。 例如： `$orderby properties/created desc` 。

## <a name="skip-token"></a>略過權杖

如果查詢回應包含許多專案，則服務會傳回一個 `$skiptoken` （ `@odata.nextLink` ）值，讓您用來取得下一個結果頁面。 使用它來逐頁查看整個結果集。

在媒體服務 v3 中，您無法設定頁面大小。 頁面大小會因實體的類型而異。 閱讀下列各節以取得詳細資料。

當您在分頁時建立或刪除實體時，這些變更會反映在傳回的結果中（如果這些變更是在尚未下載之集合的部分中）。

> [!TIP]
> 請一律使用 `nextLink` 來列舉集合，而不會相依于特定頁面大小。
>
> `nextLink`只有當實體有多個頁面時，才會出現此值。

請考慮下列使用的範例 `$skiptoken` 。 請務必將 amstestaccount** 取代為您的帳戶名稱，並將 api-version** 值設為最新版本。

如果您要求的資產清單如下所示：

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

您會得到類似下面的回應：

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

然後您會藉由傳送 get 要求來要求下一個頁面：

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

下列 c # 範例顯示如何列舉帳戶中的所有串流定位器。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>使用邏輯運算子結合查詢選項

媒體服務 v3 支援**或**和**和**邏輯運算子。 

下列 REST 範例會檢查作業的狀態：

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

您可以用 c # 來建立相同的查詢，如下所示： 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>實體的篩選和排序選項

下表顯示如何將篩選和排序選項套用至不同的實體：

|實體名稱|屬性名稱|篩選|單|
|---|---|---|---|
|[Assets](https://docs.microsoft.com/rest/api/media/assets/)|NAME|`eq`, `gt`, `lt`, `ge`, `le`|`asc` 和 `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` 和 `desc`|
|[內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|NAME|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.policyId|`eq`, `ne`||
|[作業](https://docs.microsoft.com/rest/api/media/jobs)| NAME  | `eq`            | `asc` 和 `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` 和 `desc`| 
|[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)|NAME|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` 和 `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
|[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)|NAME|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 和 `desc`|
|[轉換](https://docs.microsoft.com/rest/api/media/transforms)| NAME | `eq`            | `asc` 和 `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|

## <a name="next-steps"></a>後續步驟

* [列出資產](https://docs.microsoft.com/rest/api/media/assets/list)
* [列出內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [列出作業](https://docs.microsoft.com/rest/api/media/jobs/list)
* [列出串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [列出串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [串流處理檔案](stream-files-dotnet-quickstart.md)
* [配額和限制](limits-quotas-constraints.md)
