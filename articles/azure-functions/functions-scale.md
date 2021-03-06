---
title: Azure Functions 的規模調整和主控
description: 瞭解如何在 Azure Functions 耗用量方案和 Premium 方案之間進行選擇。
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 26924498f32b8aac2e3e7fb5cfd7c1965ee5884f
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86025823"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions 的規模調整和主控

當您在 Azure 中建立函數應用程式時，您必須為應用程式選擇主控方案。 有三個基本的主控方案可供 Azure Functions：取用[方案](#consumption-plan)、 [Premium 方案](#premium-plan)和[專用（App Service）方案](#app-service-plan)。 所有裝載方案在 Linux 和 Windows 虛擬機器上都已正式推出（GA）。

您選擇的主控方案會指示下列行為：

* 如何調整您的函式應用程式。
* 每個函數應用程式實例可用的資源。
* 支援先進的功能，例如 Azure 虛擬網路連線能力。

當您的程式碼執行時，耗用量和 Premium 方案都會自動增加計算能力。 您的應用程式會在需要時相應放大來處理負載，並在程式碼停止執行時進行調整。 針對取用方案，您也不需要支付閒置 Vm 的費用，或預先保留容量。  

Premium 方案提供額外的功能，例如高階計算實例、可無限期地保留實例，以及 VNet 連線能力。

App Service 方案可讓您利用您所管理的專用基礎結構。 您的函數應用程式不會根據事件進行調整，這表示永遠不會相應縮小為零。 （需要啟用[Always on](#always-on) ）。

如需各種主控方案（包括以 Kubernetes 為基礎的裝載）的詳細比較，請參閱[主控方案比較一節](#hosting-plans-comparison)。

## <a name="consumption-plan"></a>取用方案

當您使用取用方案時，會根據傳入事件的數目，動態新增和移除 Azure Functions 主機的實例。 此無伺服器方案會自動調整，您只需支付函式執行時使用的計算資源。 在取用方案中，函式執行會在一段可設定的時間之後逾時。

帳單是根據執行數目、執行時間以及使用的記憶體。 帳單會跨函式應用程式內的所有函式進行彙總。 如需詳細資訊，請參閱 [Azure Functions 價格頁面](https://azure.microsoft.com/pricing/details/functions/)。

取用方案是預設主控方案，具有下列優點︰

* 只有當您的函式執行時才需付費。
* 會自動調整規模，即使在高負載期間也會

相同區域中的函數應用程式可以指派給相同的取用方案。 在相同的取用方案中執行多個應用程式並沒有任何缺點或影響。 將多個應用程式指派給相同的取用方案，並不會影響每個應用程式的彈性、擴充性或可靠性。

若要深入瞭解如何在取用方案中執行時預估成本，請參閱[瞭解耗用量方案成本](functions-consumption-costs.md)。

## <a name="premium-plan"></a><a name="premium-plan"></a>進階方案

當您使用 Premium 方案時，會根據傳入事件的數目來新增和移除 Azure Functions 主機的實例，就像取用方案一樣。  Premium 方案支援下列功能：

* 永久暖實例以避免任何冷啟動
* VNet 連線
* 無限制的執行持續時間（保證為60分鐘）
* Premium 實例大小（一個核心、兩個核心和四個核心實例）
* 更可預測的定價
* 針對具有多個函數應用程式的方案進行高密度應用程式佈建

若要瞭解如何在 Premium 方案中建立函數應用程式，請參閱[Azure Functions premium 方案](functions-premium-plan.md)。

高階方案的計費是根據所需和預先準備就緒的實例所使用的核心秒數和記憶體，而不是每次執行計費和耗用記憶體。 在每個計畫中，至少有一個實例必須為暖。 這表示每個使用中計畫的每月最低成本，不論執行次數為何。 請記住，Premium 方案中的所有函式應用程式會共用預先準備就緒和作用中的實例。

在下列情況下，請考慮 Azure Functions Premium 方案：

* 您的函式應用程式會連續執行或接近連續執行。
* 您有很多的小型執行，而且在取用方案中具有高用量計費但低 GB 的第二個帳單。
* 您需要的 CPU 或記憶體選項比取用方案所提供的更多。
* 您的程式碼所需的執行時間超過取用方案允許的運行[時間上限](#timeout)。
* 您需要的功能僅適用于高階方案，例如虛擬網路連線能力。 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>專用（App Service）方案

您的函數應用程式也可以在與其他 App Service 應用程式相同的專用 Vm 上執行（基本、標準、高階和隔離的 Sku）。

在下列情況下，請考慮使用 App Service 方案：

* 您有現有的、使用量過低的 VM 已在執行其他 App Service 執行個體。
* 您想要提供要在其上執行函式的自訂映射。

在 App Service 方案中，函式應用程式的費用與其他 App Service 資源（例如 web apps）相同。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/overview-hosting-plans.md)。

透過 App Service 方案，您可以藉由新增更多 VM 實例來手動相應放大。 您也可以啟用自動調整。 如需詳細資訊，請參閱[手動或自動調整執行個體計數規模](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)。 您也可以透過選擇不同的 App Service 方案來相應增加。 如需詳細資訊，請參閱[在 Azure 中為應用程式進行擴大](../app-service/manage-scale-up.md)。 

在 App Service 方案上執行 JavaScript 函式時，您應該選擇 vCPU 數目較少的方案。 如需詳細資訊，請參閱[選擇單一核心 App Service 方案](functions-reference-node.md#choose-single-vcpu-app-service-plans)。 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

在[App Service 環境](../app-service/environment/intro.md)（ASE）中執行可讓您完全隔離您的函式，並利用高擴充功能。

### <a name="always-on"></a><a name="always-on"></a>Always On

如果您是在 App Service 計畫上執行，則應該啟用 [**永遠開啟**] 設定，讓您的函式應用程式正確執行。 在 App Service 方案中，函式的執行階段會在無作用幾分鐘後進入閒置狀態，因此只有 HTTP 觸發程序會「喚醒」您的函式。 只有 App Service 方案具備「永遠開啟」選項。 在取用方案中，平台會自動啟動函數應用程式。

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


即使已啟用 [永遠開啟] 選項，個別函式的執行逾時還是由 [host.json](functions-host-json.md#functiontimeout) 專案檔的 `functionTimeout` 設定來控制。

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>判斷現有應用程式的主控方案

若要判斷函數應用程式所使用的主控方案，請參閱[Azure 入口網站](https://portal.azure.com)中函式應用程式的 [**總覽**] 索引標籤中的**App Service 計畫**。 若要查看定價層，請選取**App Service 方案**的名稱，然後從左窗格中選取 [**屬性**]。

![在入口網站中檢視調整方案](./media/functions-scale/function-app-overview-portal.png)

您也可以使用 Azure CLI 來判斷方案，如下所示：

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

如果此命令的輸出是 `dynamic`，表示函式應用程式在取用方案中。 當此命令的輸出是時 `ElasticPremium` ，您的函數應用程式會在高階方案中。 所有其他值表示 App Service 方案的不同層級。

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

在任何方案上，函數應用程式都需要一般 Azure 儲存體帳戶，其支援 Azure Blob、佇列、檔案和表格儲存體。 這是因為 Azure Functions 依賴 Azure 儲存體進行作業，例如管理觸發程式和記錄函數執行，但是某些儲存體帳戶不支援佇列和資料表。 這些帳戶 (包括僅限 Blob 的儲存體帳戶 (包含進階儲存體) 和搭配區域備援儲存體複寫的一般用途儲存體帳戶) 會在您建立函式應用程式時，從現有**儲存體帳戶**選項中篩選出來。

您的函式應用程式所使用的相同儲存體帳戶也可供您的觸發程式和系結用來儲存應用程式資料。 不過，針對需要大量儲存體的作業，您應該使用個別的儲存體帳戶。  

可能有多個函數應用程式共用相同的儲存體帳戶，而沒有任何問題。 （有一個很好的例子，就是當您使用 Azure 儲存體模擬器在本機環境中開發多個應用程式時，它的作用就像一個儲存體帳戶）。 

<!-- JH: Does using a Premium Storage account improve perf? -->

若要深入瞭解儲存體帳戶類型，請參閱[Azure 儲存體服務簡介](../storage/common/storage-introduction.md#core-storage-services)。

## <a name="how-the-consumption-and-premium-plans-work"></a>耗用量和進階方案的運作方式

在「耗用量」和「高階」方案中，Azure Functions 基礎結構會根據其函數觸發所在的事件數目，新增函式主機的其他實例，以調整 CPU 和記憶體資源。 耗用量方案中的每個函式實例都限制為 1.5 GB 的記憶體和一個 CPU。  主機的實例是整個函式應用程式，這表示函數應用程式內的所有函式會共用實例內的資源，並同時進行調整。 共用相同取用方案的函數應用程式會個別進行調整。  在 Premium 方案中，您的方案大小會決定該實例上該方案中所有應用程式的可用記憶體和 CPU。  

函式程式碼檔案會儲存在函式主要儲存體帳戶的 Azure 檔案儲存體共用上。 當您刪除函式應用程式的主要儲存體帳戶時，函式程式碼檔案會被刪除，且無法復原。

### <a name="runtime-scaling"></a>執行階段調整

Azure Functions 使用名為「縮放控制器」** 的元件來監視事件的速率，並判斷是否相應放大或相應縮小。 縮放控制器會在每種觸發程序類型使用啟發學習法。 例如，當使用 Azure 佇列儲存體觸發程序時，會根據佇列長度和最舊佇列訊息的壽命調整規模。

Azure Functions 的尺規單位是函式應用程式。 當函式應用程式相應放大時，會配置額外資源來執行 Azure Functions 主機的多個執行個體。 反之，當計算需求降低時，縮放控制器會移除 Functions 主機的執行個體。 當函式應用程式中沒有任何函式正在執行時，實例的數目最後會相應*縮小*為零。

![縮放控制器能監視事件及建立執行個體](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>冷啟動

在您的函式應用程式閒置數分鐘後，平臺可能會將應用程式執行的實例數目調整為零。 下一個要求的增加延遲是從零調整為一。 此延遲稱為_冷啟動_。 您的函數應用程式必須載入的相依性數目可能會影響冷啟動時間。 冷啟動是同步作業的問題，例如必須傳迴響應的 HTTP 觸發程式。 如果冷啟動會影響您的函式，請考慮在高階計畫中執行，或在已啟用 Always on 的專用方案中執行。   

### <a name="understanding-scaling-behaviors"></a>了解規模調整行為

縮放比例會因為許多因素而有所不同，也會因為選取的觸發程序和語言不同，而進行不同的規模調整。 有幾個複雜的調整行為需要注意：

* 單一函數應用程式只會向外延展到最多200實例。 單一執行個體可能會一次處理一個以上的訊息或要求，因此沒有設定平行執行的數目上限。
* 針對 HTTP 觸發程式，每秒最多會配置一次新的實例。
* 針對非 HTTP 觸發程式，每隔30秒最多會配置新的實例一次。 在高階[計畫](#premium-plan)中執行時，調整速度會更快。
* 針對服務匯流排觸發程式，請使用資源的 [_管理_許可權] 來進行最有效率的調整。 使用_接聽_許可權時，調整不會正確，因為佇列長度無法用來通知調整決策。 若要深入瞭解如何在服務匯流排存取原則中設定許可權，請參閱[共用存取授權原則](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)。
* 如需事件中樞觸發程式，請參閱參考文章中的[調整指導](functions-bindings-event-hubs-trigger.md#scaling)方針。 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>可調整應用程式的最佳做法與模式

函數應用程式中有多個面向會影響其調整規模的效果，包括主機設定、執行階段耗用量和資源效率。  如需詳細資訊，請參閱[效能考量文章中的延展性一節](functions-best-practices.md#scalability-best-practices)。 您也應了解在縮放函式應用程式後，連線會如何運作。 如需詳細資訊，請參閱[如何管理 Azure Functions 中的連線](manage-connections.md)。

如需有關如何在 Python 和 Node.js 中進行調整的詳細資訊，請參閱[Azure Functions Python 開發人員指南-調整和並行](functions-reference-python.md#scaling-and-concurrency)和[Azure Functions Node.js 開發人員指南-調整和並行](functions-reference-node.md#scaling-and-concurrency)存取。

### <a name="billing-model"></a>計費模式

[Azure Functions 定價頁面](https://azure.microsoft.com/pricing/details/functions/)會詳細說明不同方案的計費方式。 使用量是在函式應用程式層級彙總，且只會計算函式程式碼執行的時間。 計費單位如下︰

* **以十億位元組-秒 (GB-s) 為單位的資源取用量**。 會計算為在函數應用程式中執行之所有函式的記憶體大小和執行時間組合。 
* **Executions**執行。 每次函式為回應事件觸發程序而執行，就算一次。

如需瞭解您的取用費用的實用查詢和資訊，請參閱[計費常見問題](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)。

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>主機方案比較

下列比較表顯示所有重要層面，以協助 Azure Functions 應用程式主控方案選項的決策：

### <a name="plan-summary"></a>方案摘要
| | |
| --- | --- |  
|**[使用量方案](#consumption-plan)**| 自動調整，而且只會在您的函式執行時支付計算資源的費用。 在取用方案中，會根據傳入事件的數目，動態新增和移除函數主機的實例。<br/> ✔預設的主控方案。<br/>✔只有在函式執行時才需付費。<br/>✔自動相應放大，即使在高負載期間也一樣。|  
|**[進階方案](#premium-plan)**|自動根據需求進行調整時，請使用預先準備就緒的背景工作角色來執行應用程式，而在閒置後不會延遲、在功能更強大的實例上執行，以及連線到 Vnet。 在下列情況下，除了 App Service 方案的所有功能之外，請考慮 Azure Functions Premium 方案： <br/>✔您的函式應用程式會持續或近乎連續執行。<br/>✔您有很多的小型執行，而且在取用方案中具有高用量計費但低 GB 的第二個帳單。<br/>✔您需要的 CPU 或記憶體選項比取用方案所提供的更多。<br/>✔您的程式碼所需的執行時間超過取用方案所允許的最長執行時間。<br/>✔您需要僅適用于高階方案的功能，例如虛擬網路連線能力。|  
|**[專用方案](#app-service-plan)**<sup>1</sup>|在 App Service 計畫中定期 App Service 計畫費率執行函數。 適用于長時間執行的作業，以及需要更多預測性調整和成本的情況。 在下列情況下，請考慮使用 App Service 方案：<br/>✔您有現有、使用量過低的 Vm 已在執行其他 App Service 實例。<br/>✔您想要提供要在其上執行函數的自訂映射。|  
|**[ASE](#app-service-plan)**<sup>1</sup>|App Service 環境（ASE）是 App Service 的功能，可提供完全隔離且專用的環境，以便安全地大規模執行 App Service 應用程式。 Ase 適用于需要下列內容的應用程式工作負載： <br/>✔非常大規模。<br/>✔隔離和安全的網路存取。<br/>✔高記憶體使用率。|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes 提供在 Kubernetes 平臺上執行的完全隔離且專用的環境。  Kubernetes 適用于需要下列內容的應用程式工作負載： <br/>✔自訂硬體需求。<br/>✔隔離和安全的網路存取。<br/>在混合式或多重雲端環境中執行的✔功能。<br/>✔與現有的 Kubernetes 應用程式和服務一起執行。|  

<sup>1</sup>如需各種 App Service 計畫選項的特定限制，請參閱[App Service 方案限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="operating-systemruntime"></a>作業系統/執行時間

| | Linux<sup>1</sup><br/>僅限程式碼 | Windows<sup>2</sup><br/>僅限程式碼 | Linux<sup>1、3</sup><br/>Docker 容器 |
| --- | --- | --- | --- |
| **[使用量方案](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | 不支援  |
| **[進階方案](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[專用方案](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n/a | n/a |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup>Linux 是 Python 執行時間堆疊唯一支援的作業系統。  
<sup>2</sup>Windows 是 PowerShell 執行時間堆疊唯一支援的作業系統。   
<sup>3</sup>Linux 是 Docker 容器唯一支援的作業系統。
<sup>4</sup>如需各種 App Service 計畫選項的特定限制，請參閱[App Service 方案限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="scale"></a>調整

| | 擴增 | 最大實例數目 |
| --- | --- | --- |
| **[使用量方案](#consumption-plan)** | 事件驅動。 自動調整規模，即使在高負載期間也會。 Azure Functions 基礎結構會根據其函數觸發所在的事件數目，新增函式主機的其他實例，以調整 CPU 和記憶體資源。 | 200 |
| **[進階方案](#premium-plan)** | 事件驅動。 自動調整規模，即使在高負載期間也會。 Azure Functions 基礎結構會根據其函數觸發所在的事件數目，新增函式主機的其他實例，以調整 CPU 和記憶體資源。 |100|
| **[專用方案](#app-service-plan)**<sup>1</sup> | 手動/自動調整 |10-20|
| **[ASE](#app-service-plan)**<sup>1</sup> | 手動/自動調整 |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | 使用[KEDA](https://keda.sh)之 Kubernetes 叢集的事件驅動自動調整。 | 依叢集而異 &nbsp; &nbsp; 。&nbsp;&nbsp;|

<sup>1</sup>如需各種 App Service 計畫選項的特定限制，請參閱[App Service 方案限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="cold-start-behavior"></a>冷啟動行為

|    |    | 
| -- | -- |
| **[耗用量 &nbsp; 方案](#consumption-plan)** | 如果閒置一段時間，應用程式可能會調整為零，表示某些要求可能會在啟動時有額外的延遲。  取用方案有一些優化可協助減少冷開始時間，包括從已經有函式主機和語言處理常式執行的預先準備就緒預留位置函式中提取。 |
| **[進階方案](#premium-plan)** | 永久暖實例，以避免任何冷啟動。 |
| **[專用方案](#app-service-plan)**<sup>1</sup> | 在專用計畫中執行時，函式主機可以連續執行，這表示冷啟動不是真正的問題。 |
| **[ASE](#app-service-plan)**<sup>1</sup> | 在專用計畫中執行時，函式主機可以連續執行，這表示冷啟動不是真正的問題。 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | 取決於 KEDA 設定。 應用程式可以設定為一律執行，而且永遠不會有冷啟動，或設定為調整為零，這會導致在新事件上進行冷啟動。 

<sup>1</sup>如需各種 App Service 計畫選項的特定限制，請參閱[App Service 方案限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="service-limits"></a>服務限制

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>網路功能

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>計費

| | | 
| --- | --- |
| **[使用量方案](#consumption-plan)** | 僅針對您的函式執行時間付費。 帳單是根據執行數目、執行時間以及使用的記憶體。 |
| **[進階方案](#premium-plan)** | 高階方案是根據所需和預先準備就緒的實例所使用的核心秒數和記憶體數目。 每個計畫的至少一個實例必須隨時保持暖。 此方案提供更可預測的定價。 |
| **[專用方案](#app-service-plan)**<sup>1</sup> | 在 App Service 方案中，函式應用程式的費用與其他 App Service 資源（例如 web apps）相同。|
| **[ASE](#app-service-plan)**<sup>1</sup> | ASE 的每月費率是針對基礎結構付費，並不會隨著 ASE 的大小而改變。 此外，每個 App Service 方案 vCPU 都有成本。 ASE 中裝載的所有應用程式都會位於隔離價格 SKU 中。 |
| **[Kubernetes](functions-kubernetes-keda.md)**| 您只需支付 Kubernetes 叢集的成本;沒有額外的函數計費。 您的函數應用程式會以應用程式工作負載的形式在叢集上執行，就像一般應用程式一樣。 |

<sup>1</sup>如需各種 App Service 計畫選項的特定限制，請參閱[App Service 方案限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

## <a name="next-steps"></a>後續步驟

+ [快速入門：在 Visual Studio Code 中建立 Azure Functions 專案](functions-create-first-function-vs-code.md)
+ [Azure Functions 中的部署技術](functions-deployment-technologies.md) 
+ [Azure Functions 開發人員指南](functions-reference.md)
