---
title: 針對 Azure VNet 閘道和連線進行疑難排解-Azure PowerShell
titleSuffix: Azure Network Watcher
description: 此頁面說明如何使用 Azure 網路監看員來針對 PowerShell Cmdlet 進行疑難排解
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 1461f7f9811145553da6c200111d4db1f013060e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738680"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>使用 Azure 網路監看員 PowerShell 來針對虛擬網路閘道和連線進行疑難排解

> [!div class="op_single_selector"]
> - [入口網站](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

網路監看員提供了許多功能，因為它的作用就是為了讓您了解您在 Azure 中的網路資源。 這些功能的其中之一便是資源疑難排解。 您可以透過入口網站、PowerShell、CLI 或 REST API 呼叫資源疑難排解。 一經呼叫，網路監看員就會檢查虛擬網路閘道或連線的健全狀況，並傳回其調查結果。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>開始之前

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。

如需支援的閘道類型清單，請瀏覽[支援的閘道類型](network-watcher-troubleshoot-overview.md#supported-gateway-types)。

## <a name="overview"></a>總覽

資源疑難排解可讓您針對虛擬網路閘道和連線所發生的問題進行疑難排解。 在要求進行資源疑難排解後，便會查詢並檢查記錄。 檢查完成時，就會傳回結果。 資源疑難排解要求是執行時間很長的要求，可能需要幾分鐘的時間才會傳回結果。 疑難排解記錄會儲存在指定儲存體帳戶的容器中。

## <a name="retrieve-network-watcher"></a>擷取網路監看員

第一步是擷取網路監看員執行個體。 `$networkWatcher`變數會在步驟 4 傳遞至 `Start-AzNetworkWatcherResourceTroubleshooting` Cmdlet。

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>擷取虛擬網路閘道連線

在此範例中，系統會對連線執行資源疑難排解。 您也可以將它傳遞給虛擬網路閘道。

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶

資源疑難排解會傳回資源健全狀況的相關資料，它也會將記錄儲存到儲存體帳戶以供檢閱。 在此步驟中，我們會建立儲存體帳戶，如果已有現有的儲存體帳戶，您也可以使用它。

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>執行網路監看員資源疑難排解

您可以使用 `Start-AzNetworkWatcherResourceTroubleshooting` Cmdlet 對資源進行疑難排解。 我們將網路監看員物件、連線或虛擬網路閘道的識別碼、儲存體帳戶識別碼和用來儲存結果的路徑，傳遞給此 Cmdlet。

> [!NOTE]
> `Start-AzNetworkWatcherResourceTroubleshooting` Cmdlet 執行時間較長，可能要花幾分鐘才能完成。

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

在執行此 Cmdlet 後，網路監看員會檢閱資源以驗證其健康狀態。 它會將結果傳回殼層，並將結果的記錄儲存在指定的儲存體帳戶中。

## <a name="understanding-the-results"></a>了解結果

動作文字會提供有關如何解決問題的一般指引。 如果問題有可行動作，則會提供附有其他指引的連結。 如果沒有其他指引，回應中會提供 URL 以供您開啟支援案例。  如需回應屬性和所含內容的詳細資訊，請瀏覽[網路監看員疑難排解概觀](network-watcher-troubleshoot-overview.md)

如需從 Azure 儲存體帳戶下載檔案的指示，請參閱[以 .NET 開始使用 Azure Blob 儲存體](../storage/blobs/storage-dotnet-how-to-use-blobs.md)。 另一項可用工具為儲存體總管。 如需儲存體總管的詳細資訊，請參閱下列連結：[儲存體總管](https://storageexplorer.com/)

## <a name="next-steps"></a>後續步驟

如果設定已變更而停止了 VPN 連線，請參閱[管理網路安全性群組](../virtual-network/manage-network-security-group.md)以追蹤可能有問題的網路安全性群組和安全性規則。
