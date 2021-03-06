---
title: 使用現有的 Azure Load Balancer Azure CLI 設定虛擬機器擴展集
description: 瞭解如何使用現有的 Azure Load Balancer 來設定虛擬機器擴展集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 2d734e5242ff2a250d332de78cfa3b7f017a3fff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809455"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>使用 Azure CLI 設定具有現有 Azure Load Balancer 的虛擬機器擴展集

在本文中，您將瞭解如何使用現有的 Azure Load Balancer 來設定虛擬機器擴展集。 

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。
- 訂用帳戶中的現有標準 sku 負載平衡器，將會在其中部署虛擬機器擴展集。
- 適用于虛擬機器擴展集的 Azure 虛擬網路。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果您選擇在本機使用 CLI，本文會要求您安裝 Azure CLI 版本的2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="sign-in-to-azure-cli"></a>登入 Azure CLI

登入 Azure。

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>使用現有的負載平衡器部署虛擬機器擴展集

將括弧中的值取代為您的設定中的資源名稱。

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

下列範例會使用下列內容來部署虛擬機器擴展集：

- 名為**myVMSS**的虛擬機器擴展集
- 名為**myLoadBalancer**的 Azure Load Balancer
- 名為**myBackendPool**的負載平衡器後端集區
- 名為**myVnet**的 Azure 虛擬網路
- 名為**mySubnet**的子網
- 名為**myResourceGroup**的資源群組
- 虛擬機器擴展集的 Ubuntu Server 映射

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> 建立擴展集之後，無法修改負載平衡器健全狀況探查所使用的負載平衡規則的後端埠。 若要變更埠，您可以藉由更新 Azure 虛擬機器擴展集來移除健康情況探查、更新埠，然後再次設定健康情況探查。

## <a name="next-steps"></a>後續步驟

在本文中，您已使用現有的 Azure Load Balancer 來部署虛擬機器擴展集。  若要深入瞭解虛擬機器擴展集和負載平衡器，請參閱：

- [什麼是 Azure Load Balancer？](load-balancer-overview.md)
- [什麼是虛擬機器擴展集？](../virtual-machine-scale-sets/overview.md)
                                