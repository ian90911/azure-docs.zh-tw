---
title: 使用 Azure CLI 建立事件中樞 - Azure 事件中樞 | Microsoft Docs
description: 本快速入門說明如何使用 Azure CLI 建立事件中樞，然後使用 Java 傳送和接收事件。
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 90828a09b925fd3af774b22db3102094c1dd0c6d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537151"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>快速入門：使用 Azure CLI 建立事件中樞

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

在此快速入門中，您將使用 Azure CLI 建立事件中樞。

## <a name="prerequisites"></a>Prerequisites
若要完成本快速入門，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶][]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 Azure CLI，本教學課程會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 來檢查您的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sign-in-to-azure"></a>登入 Azure

如果您在 Cloud Shell 中執行命令，則不需要執行下列步驟。 如果您在本機執行 CLI，請執行下列步驟來登入 Azure 並設定您目前的訂用帳戶：

執行下列命令以登入 Azure：

```azurecli-interactive
az login
```

設定目前的訂用帳戶環境。 以您要使用的 Azure 訂用帳戶名稱取代 `MyAzureSub`：

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>建立資源群組
資源群組是 Azure 資源的邏輯集合。 所有資源都會在資源群組中部署與管理。 執行下列命令以建立資源群組：

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間
事件中樞命名空間提供唯一的範圍容器 (依其完整網域名稱來參考)，您可以在其中建立一或多個事件中樞。 若要在資源群組中建立命名空間，請執行下列命令：

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>建立事件中樞
執行以下命令來建立事件中樞：

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

恭喜！ 您已使用 Azure CLI 建立事件中樞命名空間和該命名空間內的事件中樞。 

## <a name="next-steps"></a>後續步驟

在本文中，您已建立資源群組、事件中樞命名空間和事件中樞。 如需將事件傳送至事件中樞或從事件中樞接收事件的逐步指示，請參閱**傳送及接收事件**教學課程： 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (僅傳送)](event-hubs-c-getstarted-send.md)
- [Apache Storm (僅接受)](event-hubs-storm-getstarted-receive.md)

[建立免費帳戶]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
