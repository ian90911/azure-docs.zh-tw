---
title: 使用 Azure CLI 設定負載平衡和輸出規則
titleSuffix: Azure Load Balancer
description: 本文說明如何使用 Azure CLI 在 Standard Load Balancer 中設定負載平衡和輸出規則。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 2b326c6c58b4685b6e73d0f9a641a2f90807d705
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84803752"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>使用 Azure CLI 在 Standard Load Balancer 中設定負載平衡和輸出規則

本快速入門說明如何使用 Azure CLI 在 Standard Load Balancer 中設定輸出規則。  

當您完成時，Load Balancer 資源會包含兩個前端以及與其相關聯的規則：一個用於輸入，另一個用於輸出。  每個前端都有公用 IP 位址的參考，本案例會對輸入與輸出使用不同的公用 IP 位址。   負載平衡規則只會提供輸入負載平衡，輸出規則則可控制提供給 VM 的輸出 NAT。  本快速入門會使用兩個不同的後端集區，一個用於輸入，另一個用於輸出，以說明功能並允許此案例的彈性。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果您選擇在本機安裝和使用 CLI，在執行本教學課程時，您必須執行 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-resource-group"></a>建立資源群組

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus2** 位置建立名為 myresourcegroupoutbound** 的資源群組：

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>建立虛擬網路
使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet)，在 myresourcegroupoutbound** 中建立名為 myvnetoutbound** 且子網路名為 mysubnetoutbound** 的虛擬網路。

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>建立輸入公用 IP 位址 

若要在網際網路上存取您的 Web 應用程式，您需要負載平衡器的公用 IP 位址。 標準負載平衡器只支援標準公用 IP 位址。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)，在 myresourcegroupoutbound** 中建立名為 mypublicipinbound** 的標準公用 IP 位址。

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>建立輸出公用 IP 位址 

使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)，針對 Load Balancer 的前端輸出組態建立標準 IP 位址。

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>建立 Azure Load Balancer

本節將詳細說明如何建立及設定下列負載平衡器元件：
  - 前端 IP，可接收負載平衡器上的連入網路流量。
  - 後端集區，前端 IP 會在其中傳送負載平衡的網路流量。
  - 輸出連線能力的後端集區。 
  - 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  - 負載平衡器輸入規則，可定義如何將流量分散至 VM。
  - 負載平衡器輸出規則，可定義如何從 VM 分散流量。

### <a name="create-load-balancer"></a>建立負載平衡器

使用[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest)命名*lb*建立包含輸入前端 ip 設定和後端集區*bepoolinbound* （與您在上一個步驟中建立的公用 IP 位址*mypublicipinbound*相關聯）的輸入 ip 位址的 Load Balancer。

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>建立輸出集區

建立一個額外的後端位址集區，以定義具有[az network lb 位址集區 create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest)且名稱為*bepooloutbound*的 vm 集區的輸出連線能力。  建立個別的輸出集區可提供最大的彈性，但您也可以省略此步驟，也只能使用輸入*bepoolinbound* 。

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>建立輸出前端 IP
使用 [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 建立 Load Balancer 的輸出前端 IP 組態，其包含名為 myfrontendoutbound** 且與公用 IP 位址 mypublicipoutbound** 相關聯的輸出前端 IP 組態

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>建立健全狀況探查

健全狀況探查會檢查所有虛擬機器執行個體，確認它們可以傳送網路流量。 探查檢查失敗的虛擬機器執行個體會從負載平衡器上移除，直到其恢復正常運作且探查判斷其健全狀況良好為止。 使用 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) 建立健康狀態探查，以檢視虛擬機器的健康狀態。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>建立負載平衡規則

負載平衡器規則可定義連入流量的前端 IP 組態及接收流量的後端集區，以及所需的來源和目的地連接埠。 使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) 建立負載平衡器規則 myinboundlbrule**，用來接聽前端集區 myfrontendinbound** 中的連接埠 80，以及用來將負載平衡的網路流量傳送到後端位址集區 bepool** (也是使用連接埠 80)。 

>[!NOTE]
>此負載平衡規則會因為具有 --disable-outbound-snat 參數而停用自動輸出 (S)NAT。 只有輸出規則會提供輸出 NAT。

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>建立輸出規則

輸出規則會定義前端公用 IP (由前端 myfrontendoutbound** 來表示)，以供用於所有輸出 NAT 流量以及要套用此規則的後端集區。  建立輸出規則 myoutboundrule** 以便轉譯 bepool** 後端集區中所有虛擬機器 的輸出網路 (NIC IP 組態)。  下列命令也會將輸出閒置逾時從 4 分鐘變更為 15 分鐘，並配置 10000 個 SNAT 連接埠，而不是 1024 個。  如需詳細資訊，請檢閱[輸出規則](https://aka.ms/lboutboundrules)。

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

如果您不想要使用個別的輸出集區，您可以變更上述命令中的位址集區引數，改為指定*bepoolinbound* 。  我們建議使用不同的集區，以提供所產生設定的彈性和可讀性。

此時，您可以使用[az network NIC ip 設定位址集區新增](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)來更新個別 NIC 資源的 IP 設定，以繼續將 VM 新增至後端集區*bepoolinbound* __和__ *bepooloutbound* 。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組、負載平衡器和所有相關資源。

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>後續步驟
在本文中，您建立了 Standard Load Balancer、設定了兩個輸入負載平衡器流量規則，並設定了後端集區 VM 的健康情況探查。 若要深入了解 Azure Load Balancer，請繼續 Azure Load Balancer 的教學課程。

> [!div class="nextstepaction"]
> [Azure Load Balancer 教學課程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
