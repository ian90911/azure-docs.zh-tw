---
title: 安裝混合式雲端擴充功能 (HCX)
description: 為您的 Azure VMware 解決方案 (AVS) 私人雲端設定 VMware 混合式雲端擴充功能 (HCX) 解決方案
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: b897a44fb6811c4e3564c59a8ab2c064506f0a4f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539154"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>安裝適用於 Azure VMware 解決方案的 HCX

在本文中，我們將逐步解說為您的 Azure VMWare 解決方案（AVS）私人雲端設定 VMWare 混合式雲端擴充功能（HCX）解決方案的程式。 HCX 可讓您透過各種內建 HCX 支援的遷移類型，將您的 VMware 工作負載遷移至雲端，以及其他已連線的網站。

HCX Advanced （預設安裝）支援最多三個 vCenters。 如果需要超過三個，客戶可以選擇透過支援啟用 HCX Enterprise 附加元件。 HCX Enterprise 安裝會在公開上市（GA）之後，對客戶收取額外費用，但會提供[額外的功能](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)。


[在開始之前](#before-you-begin)，請先仔細檢閱[軟體版本需求](#software-version-requirements)和[先決條件](#prerequisites)。 

然後，我們會逐步解說所有必要的程序，藉以：

> [!div class="checklist"]
> * 部署內部部署 HCX OVA
> * 啟用和設定 HCX
> * 設定網路上行連結和服務網格
> * 藉由檢查設備狀態來完成設定

完成安裝之後，您可以遵循本文結尾所提供的建議後續步驟。  

## <a name="before-you-begin"></a>開始之前
    
* 參閱基本 AVS 軟體定義資料中心 (SDDC) [教學課程系列](tutorial-network-checklist.md)
* 檢閱並參考 [VMware HCX 文件](https://docs.vmware.com/en/VMware-HCX/index.html)，包括 HCX 使用者指南
* 檢閱 VMware 文件[使用 VMware HCX 移轉虛擬機器](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* 可選擇檢閱 [VMware HCX 部署考量因素](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* 可選檢閱 HCX 上的相關 VMware 資料，例如 HCX 上 的 VMware vSphere [部落格系列](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)。 
* 透過 AVS 支援通道訂購 AVS HCX Enterprise 啟用。

在準備使用 AVS 私人雲端 HCX 解決方案時，針對計算和儲存體資源調整工作負載大小是必要的規劃步驟。 在初始私用雲端環境規劃過程中，解決調整大小的步驟。   

## <a name="software-version-requirements"></a>軟體版本需求
基礎結構元件必須執行所需的最低版本。 
                                                         
| 元件類型    | 來源環境需求    | 目的地環境需求   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>如果使用 5.5 U1 或更早的版本，請使用獨立 HCX 使用者介面來進行 HCX 作業。  | 6.0 U2 及更高版本   |
| ESXi   | 5.0    | ESXi 6.0 及更高版本   |
| NSX    | 針對位於來源的邏輯交換器 HCX 網路延伸模組：NSXv 6.2+ 或 NSX-T 2.4+   | NSXv 6.2+ 或 NSX-T 2.4+<br/><br/>針對 HCX 鄰近性路由： NSXv 6.4 + （不支援以 NSX-T 進行的鄰近性路由） |
| vCloud Director   | 非必要 - 來源網站沒有與 vCloud Director 的互通性 | 將目的地環境與 Vcloud 進行整合主管整合時，最小值為9.1.0.2。  |

## <a name="prerequisites"></a>Prerequisites

* 內部部署和 AVS SDDC ER 線路之間應設定全域觸及。

* 所有必要的連接埠都應該在內部部署和 AVS SDDC 之間開啟 (請參閱 [VMware HCX 文件](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html))。

* 一個用於內部部署 HCX 管理員的 IP 位址，以及至少兩個用於互連（IX）和網路延伸（NE）設備的 IP 位址。

* 內部部署 HCX IX 和 NE 設備應該能夠觸及 vCenter 和 ESXi 基礎結構。

* 除了在 Azure 入口網站中用於 SDDC 部署的 /22 CIDR 網路位址區塊以外，若要部署 WAN 互連設備，HCX 需要 /29 區塊。 請務必將這項需求納入您的網路規劃。

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>部署 VMware HCX OVA 內部部署

1. 登入 AVS SDDC vCenter，然後選取 **HCX**。

    ![選取 AVS vCenter 中的 HCX](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. 在 [**管理**] 底下，選取 [**系統更新**]，然後選取 [**要求下載連結**] 以下載 VMware HCX OVA 檔案。

    ![取得系統更新](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. 接下來，移至內部部署 vCenter 並選取 OVF 範本，以部署至您的內部部署 vCenter。  

    ![選取 OVF 範本](./media/hybrid-cloud-extension-installation/select-template.png)

1. 選取名稱和位置，然後選取需要部署 HCX 的資源/叢集。 然後，檢閱詳細資料和所需的資源。  

    ![檢閱範本詳細資料](./media/hybrid-cloud-extension-installation/configure-template.png)

1. 檢閱授權條款，如果您同意，請選取所需的儲存體和網路。 然後選取 [下一步]。

1. 在 [自訂範本] 中，輸入全部所需的資訊。 

    ![自訂範本](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. 選取 [下一步]，並驗證設定，然後選取 [完成] 以部署 HCX OVA。

## <a name="activate-hcx"></a>啟動 HCX

安裝後，執行下列步驟。

1. 登入位於的內部部署 HCX 管理員 `https://HCXManagerIP:9443` ，並使用您的使用者名稱和密碼來登入。 

   > [!IMPORTANT]
   > 請務必包含 `9443` 埠號碼與 HCX MANAGER IP 位址。

1. 在 [授權] 中，輸入您的 **HCX 進階金鑰**。  

    ![輸入 HCX 金鑰](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX 管理員必須有開放的網際網路存取或已設定 Proxy。

1. 在**vcenter**中，如有需要，請編輯 vcenter 資訊。

    ![設定 vCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. 在 [資料中心位置] 中，如有需要，請編輯資料中心位置。

    ![資料庫位置](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>設定 HCX 

1. 登入內部部署 vCenter，然後在 [**首頁**] 底下，選取 [ **HCX**]。

    ![VCenter 中的 HCX](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. 在 [**基礎結構**] 底下，選取 [**網站配對**] [  >  **新增網站配對**]。

    ![新增網站配對](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. 輸入 [遠端 HCX URL] 或 [IP 位址]、[AVS cloudadmin 使用者名稱和密碼]，然後選取 **[連線]**。

   系統會顯示連線的網站。
   
    ![站台連線](./media/hybrid-cloud-extension-installation/site-connection.png)

1. 在 [**基礎結構**] 底下，選取 [**互連**  >  **多網站服務網格**  >  **網路設定檔**] [  >  **建立網路設定檔**]。

    ![建立網路設定檔](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. 針對新的網路設定檔，輸入 HCX IX 和 NE IP 位址範圍（IX 和 NE 設備至少需要兩個 IP 位址）。
    
   ![輸入 IP 位址範圍](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > 網路延伸設備 (HCX-NE) 與分散式虛擬交換器 (DVS) 有一對一的關係。  

1. 此時，請選取 [計算設定檔] > [建立計算設定檔]。

1. 輸入計算設定檔名稱，然後選取 [繼續]。  

    ![建立計算設定檔](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. 選取要啟用的服務，例如 [遷移]、[網路延伸] 或 [嚴重損壞修復]，然後選取 [**繼續**]。

    ![選取服務](./media/hybrid-cloud-extension-installation/select-services.png)

1. 在 [選取服務資源] 中，選取所選 HCX 服務所應該啟用的一個或多個服務資源。 選取 [繼續]。
    
   ![選取服務資源](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > 選取特定叢集，其中已使用 HCX 鎖定來源 VM 進行移轉。

1. 選取 [資料存放區]，然後選取 [繼續]。 
      
    選取每個用於部署 HCX 互連設備的計算和儲存體資源。 選取多個資源時，HCX 會使用第一個選取的資源，直到其容量用盡為止。  
    
    ![選取部署資源](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. 選取在 [網路設定檔] 中建立的管理網路設定檔，然後選取 [繼續]。  
      
    選取可觸達 vCenter 和 ESXi 主機管理介面的網路設定檔。 如果您尚未定義這類網路設定檔，您可以在這裡建立。  
    
    ![選取管理網路設定檔](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. 選取 [網路上行連結]，然後選取 [繼續]。
      
    選取一或多個網路設定檔，讓下列其中一個條件成立：  
    * 可以透過此網路觸達遠端網站上的互連設備  
    * 遠端設備可以透過此網路觸達本機互連設備。  
    
    如果您的點對點網路 (例如 Direct Connect) 不會跨多個網站共用，您可以略過此步驟，因為計算設定檔會與多個網站共用。 在這種情況下，您可以在建立互連服務網格期間覆寫和指定上行連結網路設定檔。  
    
    ![選取上行連結網路設定檔](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. 選取 [VMotion 網路設定檔]，然後選取 [繼續]。
      
   選取可用來觸達 ESXi 主機 VMotion 介面的網路設定檔。 如果您尚未定義這類網路設定檔，您可以在這裡建立。 如果您沒有 VMotion 網路，請選取 [管理網路設定檔]。  
    
   ![選取 VMotion 網路設定檔](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. 從 [**選取 vSphere 複寫網路設定檔**] 中，選取要 ESXi 主機的 vSphere 複寫介面的網路設定檔，然後選取 [**繼續**]。
      
   在大多數情況下，此設定檔與管理網路設定檔相同。  
    
   ![選取 vSphere 複寫網路設定檔](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. 從 [**選取網路擴充功能的分散式交換器**] 中，選取您的網路上將會整合並聯機的 vm 所在的 DVS。  選取 \[繼續\]。  
      
    ![選取分散式虛擬交換器](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. 檢查連接規則，然後選取 [**繼續**]。  

    ![建立計算設定檔](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  選取 [完成] 以建立計算設定檔。

## <a name="configure-network-uplink"></a>設定網路上行連結

此時，請在適用於網路上行連結的 AVS SDDC 中設定網路設定檔變更。

1. 登入 SDDC NSX-T 以建立新的邏輯交換器，或使用可用於內部部署和 AVS SDDC 之間網路上行的現有邏輯交換器。

1. 在可用於內部部署到 AVS SDDC 通訊的 AVS SDDC 中，建立 HCX 上行連結的網路設定檔。  
    
   ![建立上行連結的網路設定檔](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. 輸入網路設定檔的名稱，並根據所需的 L2 網路延伸模組，輸入至少 4 至 5 個可用的 IP 位址。  
    
   ![設定上行連結的網路設定檔](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. 選取 [建立] 以完成 AVS SDDC 設定

## <a name="configure-service-mesh"></a>設定服務網格

此時，請在內部部署和 AVS SDDC 之間設定服務網格。

1. 登入 AVS SDDC vCenter，然後選取 **HCX**。

2. 在 [**基礎結構**] 底下，選取 [**互連**  >  **服務網格**] [  >  **建立服務網格**] 來設定在先前步驟中建立的網路和計算設定檔。    
      
    ![設定服務網格](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. 選取 [配對的網站] 以啟用混合式功能，然後選取 [**繼續**]。   
    
    ![選取配對網站](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. 選取來源和遠端計算設定檔以啟用混合服務，然後選取 [**繼續**]。
      
    這些選項會定義資源，讓 Vm 可以使用 HCX 服務。  
      
    ![啟用混合服務](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. 選取要啟用的服務，然後選取 [**繼續**]。  
      
    ![選取 HCX 服務](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. 在 [進階設定 - 覆寫上行連結網路設定檔] 中選取 [繼續]。  
      
    上行連結網路設定檔是用來連線到網路，可透過它來連線到遠端網站的互連設備。  
      
    ![覆寫上行連結設定檔](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. 選取 **[設定網路延伸設備] Scale Out**。 
      
    ![網路延伸模組擴增](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. 輸入對應於 DVS 交換器計數的設備計數。  
      
    ![設定設備計數](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. 選取 [**繼續**] 跳過。  
      
    ![設定流量工程](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. 檢閱拓撲預覽，然後選取 [繼續]。 

11. 輸入此服務網格的使用者易記名稱，然後選取 **[完成]** 以完成。  
      
    ![完成服務網格](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    如此便完成服務網格的部署和設定。  
      
    ![已部署服務網格](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>檢查設備狀態
若要檢查設備的狀態，請選取 [互連] > [設備]。 
      
![設備狀態](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>後續步驟

當設備互連**通道狀態**是 **UP** 且為綠色時，您就可以使用 HCX 遷移和保護 AVS VM。 請參閱 VMware 技術文件中的 [VMware HCX 文件](https://docs.vmware.com/en/VMware-HCX/index.html)和[移轉虛擬機器與 VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)。
