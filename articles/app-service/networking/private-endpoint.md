---
title: 使用私用端點私下連接到 Azure Web 應用程式
description: 使用 Azure 私人端點私下連線至 Web 應用程式
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 07/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: fdad2f7c2ce4f82529866b4235ebebab8da664d3
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054571"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>使用 Azure Web 應用程式的私人端點 (預覽)

> [!Note]
> 在預覽重新整理中，我們發行了資料外泄保護功能。
>
> 預覽適用于 PremiumV2 Windows 和 Linux Web Apps 和彈性 Premium 功能的所有公用區域。 

您可以使用 Azure Web 應用程式的私人端點來允許位於私人網路中的用戶端透過 Private Link 安全地存取應用程式。 私人端點會使用 Azure VNet 位址空間中的 IP 位址。 私人網路上的用戶端與 Web 應用程式之間的網路流量會流經 VNet 和 Microsoft 骨幹網路上的 Private Link，以排除公開網際網路的風險。

使用 Web 應用程式的私人端點可讓您：

- 藉由設定私人端點來保護您的 Web 應用程式，以排除公開風險。
- 使用 VPN 或 ExpressRoute 私人對等互連，安全地從連線至 VNet 的內部部署網路連線到 Web 應用程式。
- 避免從您的 VNet 外泄任何資料。 

如果您只需要 VNet 與 Web 應用程式之間的安全連線，服務端點是最簡單的解決方案。 如果您也需要透過 Azure 閘道、區域內對等互連 VNet 或全域對等互連 VNet，從內部部署連線到 web 應用程式，則私用端點是解決方案。  

如需詳細資訊，請參閱[服務端點][serviceendpoint]。

## <a name="conceptual-overview"></a>概念概觀

私人端點是您虛擬網路 (VNet) 的子網路中，Azure Web 應用程式的特殊網路介面 (NIC)。
當您建立 Web 應用程式的私人端點時，其會在您私人網路的用戶端與 Web 應用程式之間提供安全的連線。 私人端點會從 VNet 的 IP 位址範圍指派 IP 位址。
私人端點與 Web 應用程式之間的連線會使用安全的 [Private Link][privatelink]。 私人端點僅用於您 Web 應用程式的傳入流量。 傳出流量不會使用此私人端點，但您可以透過 [VNet 整合功能][vnetintegrationfeature]，將傳出流量插入不同子網路中的網路。

您插入私人端點的子網路在其中可以有其他資源，您不需要專用的空白子網路。
您也可以在與 Web 應用程式不同的區域中部署私人端點。 

> [!Note]
>VNet 整合功能無法使用與私人端點相同的子網，這是 VNet 整合功能的限制。

從安全性的觀點來看：

- 當您啟用 Web 應用程式的私人端點時，會停用所有公用存取。
- 您可以在其他 VNet 和子網路啟用多個私人端點，包括其他區域中的 VNet。
- 私人端點 NIC 的 IP 位址必須是動態的，但在您刪除私人端點之前，其會保持不變。
- 私人端點的 NIC 不能與 NSG 建立關聯。
- 裝載私人端點的子網路可與 NSG 建立關聯，但您必須停用私人端點的網路原則強制執行：請參閱[停用私人端點的網路原則][disablesecuritype]。 因此，您無法依任何 NSG 來篩選您私人端點的存取權。
- 當您啟用 Web 應用程式的私人端點時，不會評估 Web 應用程式的[存取限制][accessrestrictions]設定。
- 您可以移除目的地為標記網際網路或 Azure 服務的所有 NSG 規則，藉此排除 VNet 中的資料外泄風險。 當您部署 Web 應用程式的私人端點時，只能透過私人端點存取此特定 Web 應用程式。 如果您有另一個 Web 應用程式，則必須針對這個其他 Web 應用程式部署另一個專用的私人端點。

在 Web 應用程式的 Web HTTP 記錄中，您會找到用戶端來源 IP。 這項功能是使用 TCP Proxy 通訊協定來執行，將用戶端 IP 屬性轉送到 Web 應用程式。 如需詳細資訊，請參閱[使用 TCP Proxy v2 取得連線資訊][tcpproxy]。


  > [!div class="mx-imgBorder"]
  > ![Web 應用程式私人端點全域概觀](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

當您使用 Web 應用程式的私用端點時，要求的 URL 必須符合您的 Web 應用程式名稱。 預設為 mywebappname.azurewebsites.net。

根據預設，如果沒有私用端點，web 應用程式的公用名稱就是叢集的標準名稱。
例如，名稱解析會是：

|名稱 |類型 |值 |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


當您部署私用端點時，我們會更新 DNS 專案，以指向正式名稱 mywebapp.privatelink.azurewebsites.net。
例如，名稱解析會是：

|名稱 |類型 |值 |備註 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|<--此公用 IP 不是您的私人端點，您會收到403錯誤|

您必須設定私人 DNS 伺服器或 Azure DNS 私人區域，才能進行測試，您可以修改測試電腦的主項目目。
您需要建立的 DNS 區域為： **privatelink.azurewebsites.net**。 針對您的 Web 應用程式，使用 A 記錄和私人端點 IP 來註冊記錄。
例如，名稱解析會是：

|名稱 |類型 |值 |備註 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|<--您會在您的 DNS 系統中管理此專案，以指向您的私人端點 IP 位址|

在此 DNS 設定之後，您可以使用預設名稱 mywebappname.azurewebsites.net，私下連線到您的 Web 應用程式。


如果您需要使用自訂 DNS 名稱，則必須在 Web 應用程式中新增自訂名稱。 在預覽期間，您必須使用公用 DNS 解析來驗證自訂名稱，就如同任何自訂名稱一樣。 如需詳細資訊，請參閱[自訂 DNS 驗證][dnsvalidation]。

針對 Kudu 主控台或 Kudu REST API （例如，使用 Azure DevOps 自我裝載代理程式部署），您必須在 Azure DNS 私人區域或自訂 DNS 伺服器中建立兩個記錄。 

| 名稱 | 類型 | 值 |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價][pricing]。

## <a name="limitations"></a>限制

當您在彈性進階方案中使用 Azure 函式搭配私人端點時，若要在 Azure Web 入口網站中執行函式，您必須具有直接網路存取權，否則會收到 HTTP 403 錯誤。 換句話說，您的瀏覽器必須能夠連線到私人端點，才能從 Azure 入口網站執行此函式。 

在預覽期間，只有生產位置會在私人端點後方公開，而其他位置必須由公用端點進行連線。

我們會定期改善 Private Link 功能和私人端點，請查看[這篇文章][pllimitations]，以取得有關限制的最新資訊。

## <a name="next-steps"></a>後續步驟

- 若要透過入口網站部署 Web 應用程式的私用端點，請參閱[如何使用入口網站私下連接到 Web 應用程式][howtoguide1]
- 若要使用 Azure CLI 部署 Web 應用程式的私用端點，請參閱[如何使用 Azure CLI 私下連接到 Web 應用程式][howtoguide2]
- 若要使用 PowerShell 為您的 Web 應用程式部署私用端點，請參閱[如何使用 powershell 以私下連線到 Web 應用程式][howtoguide3]



<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
[howtoguide2]: https://docs.microsoft.com/azure/app-service/scripts/cli-deploy-privateendpoint
[howtoguide3]: https://docs.microsoft.com/azure/app-service/scripts/powershell-deploy-private-endpoint
