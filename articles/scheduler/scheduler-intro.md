---
title: 何謂 Azure 排程器？
description: 建立排程，並執行可在 Azure 內部或外部呼叫服務的自動化作業
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78898545"
---
# <a name="what-is-azure-scheduler"></a>何謂 Azure 排程器？

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 將會取代[即將淘汰的 Azure 排程器](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)。 若要繼續使用您在排程器中設定的作業，請儘快[遷移至 Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) 。 
>
> Azure 入口網站中已不再提供排程器，但目前仍然提供 [REST API](/rest/api/scheduler) 和 [Azure 排程器 PowerShell Cmdlets](scheduler-powershell-reference.md)，以便您管理作業和作業集合。

[Azure 排程器](https://azure.microsoft.com/services/scheduler/)可協助您藉由以宣告方式說明動作，建立在雲端中執行的[作業](../scheduler/scheduler-concepts-terms.md)。 接著，此服務會自動排程並執行這些動作。 例如，您可以在 Azure 內外呼叫服務 (像是呼叫 HTTP 或 HTTPS 端點)，以及將訊息發佈到 Azure 儲存體佇列和 Azure 服務匯流排佇列或主題。 您可以立即執行作業，或稍後再執行。 排程器可輕易支援[複雜的排程和進階週期](../scheduler/scheduler-advanced-complexity.md)。 排程器可指定何時要執行工作、保存可供您檢閱的作業結果歷程記錄，然後依預期可靠地排程要執行的工作負載。

此外也有其他 Azure 排程功能會在背景中使用排程器，例如 [Azure WebJobs](../app-service/webjobs-create.md)，這是 Azure App Service 中的一項 [Web Apps](https://azure.microsoft.com/services/app-service/web/) 功能。 您可以使用排程器[REST API](https://docs.microsoft.com/rest/api/scheduler/)來管理這些動作的通訊，這可協助您管理這些動作的通訊。

以下排程器可派上用場的一些案例：

* 執行週期性應用程式動作：例如，定期將 Twitter 中的資料收集到摘要中。

* 執行每日維護：例如每日剪除記錄、執行備份及其他維護工作。

  比方說，身為系統管理員，您可能會想要在未來 9 個月的每天凌晨 1:00 備份資料庫。

雖然您可以使用排程器來建立、維護和執行已排程的工作負載，但排程器並不會裝載工作負載或執行程式碼。 此服務只會叫用** 裝載於其他位置的服務或程式碼，例如在 Azure、內部部署或另一個提供者中。 排成器可透過 HTTP、HTTPS、儲存體佇列、服務匯流排佇列或服務匯流排主題執行叫用。

若要建立、排程、管理、更新或刪除作業和[工作集合](../scheduler/scheduler-concepts-terms.md)，您可以使用程式碼、排程器[REST API](https://docs.microsoft.com/rest/api/scheduler/)或 Azure 排程器[PowerShell Cmdlet](scheduler-powershell-reference.md)。

## <a name="next-steps"></a>後續步驟

* [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)
* [Azure 排程器的方案與計費](scheduler-plans-billing.md)
* [如何使用 Azure 排程器建置複雜的排程和進階週期](scheduler-advanced-complexity.md)
* [Azure 排程器 REST API 參考](/rest/api/scheduler)
* [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)
