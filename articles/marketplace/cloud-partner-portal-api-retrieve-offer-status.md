---
title: 取得供應專案狀態-Azure Marketplace
description: API 以取得供應專案的目前狀態。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: 5652e7b6123a9836c574059e83101a073eea56ea
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535870"
---
# <a name="retrieve-offer-status"></a>擷取供應項目狀態

> [!NOTE]
> Cloud Partner 入口網站 Api 會與整合，並會繼續在合作夥伴中心運作。 轉換會引進微小的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼在轉換至合作夥伴中心後繼續運作。 只有在轉換至合作夥伴中心之前已整合的現有產品，才應該使用 CPP Api;新產品應使用合作夥伴中心提交 Api。

擷取供應項目的目前狀態。

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 參數

|  **名稱**       |   **說明**                            |  **Data type** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | 發行者識別碼，例如 `Contoso`  |     字串     |
|  offerId        | 可唯一識別供應項目的 GUID      |     字串     |
|  api-version    | API 的最新版本                        |     日期       |
|  |  |

## <a name="header"></a>頁首


|  名稱           |  值               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  授權  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>本文範例

### <a name="response"></a>回應

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```

### <a name="response-body-properties"></a>回應主體屬性

|  **名稱**             |    **說明**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | 供應項目的狀態。 如需可能值清單，請參閱下面的[供應項目狀態](#offer-status)。 |
|  messages             | 與供應項目相關聯的訊息陣列                                                    |
|  steps                | 供應項目發行期間，供應項目所歷經的步驟陣列                      |
|  estimatedTimeFrame   | 完成此步驟要花費的估計時間 (易記格式)                       |
|  id                   | 步驟的識別碼                                                                         |
|  stepName             | 步驟的名稱                                                                               |
|  description          | 步驟的說明                                                                        |
|  status               | 步驟的狀態。 如需可能值清單，請參閱下面的[步驟狀態](#step-status)。    |
|  messages             | 與步驟相關聯的訊息陣列                                                          |
|  processPercentage    | 步驟的完成百分比                                                              |
|  previewLinks         | *目前未執行*                                                                    |
|  liveLinks            | *目前未執行*                                                                    |
|  notificationEmails   | 已針對遷移至合作夥伴中心的供應專案淘汰。 遷移供應專案的通知電子郵件將會傳送至 [帳戶設定] 中的 [賣方連絡人資訊] 底下指定的電子郵件。<br><br>若為非遷移的供應專案，則為以逗號分隔的電子郵件地址清單，以取得作業進度的通知        |
|  |  |

### <a name="response-status-codes"></a>回應狀態碼

| **Code** |   **說明**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - 已成功處理要求，並已傳回供應項目的最新狀態。 |
|  400     | `Bad/Malformed request` - 錯誤回應本文可能包含更多資訊。                 |
|  404     | `Not found` - 指定的實體不存在。                                                |
|  |  |

### <a name="offer-status"></a>供應項目狀態

|  **名稱**                    |    **說明**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | 供應項目從未發行。                          |
|  NotStarted                  | 供應項目是新的，且未啟動。                            |
|  WaitingForPublisherReview   | 供應項目正在等候發行者核准。                 |
|  正在執行                     | 正在處理供應項目提交。                     |
|  成功                   | 已完成處理供應項目提交。               |
|  已取消                    | 已取消供應項目提交。                           |
|  失敗                      | 供應項目提交失敗。                                 |
|  |  |

### <a name="step-status"></a>步驟狀態

|  **名稱**                    |    **說明**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | 步驟尚未啟動。                        |
|  InProgress                  | 步驟正在執行。                             |
|  WaitingForPublisherReview   | 步驟正在等候發行者核准。      |
|  WaitingForApproval          | 步驟正在等候處理核准。        |
|  封鎖                     | 步驟遭到封鎖。                             |
|  已拒絕                    | 步驟遭到拒絕。                            |
|  完成                    | 步驟已完成。                            |
|  已取消                    | 步驟已取消。                           |
|  |  |
