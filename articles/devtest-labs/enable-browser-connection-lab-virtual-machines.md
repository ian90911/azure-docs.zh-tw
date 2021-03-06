---
title: 在 Azure DevTest Labs 虛擬機器上啟用瀏覽器連接
description: DevTest Labs 現在與 Azure 防禦整合，身為實驗室的擁有者，您可以啟用透過瀏覽器存取所有實驗室虛擬機器的程式。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ff55aa033ee9a024c8bc89cc9d39bc8d3ad25da
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537519"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>在 Azure DevTest Labs 虛擬機器上啟用瀏覽器連接 
DevTest Labs 與[Azure](https://docs.microsoft.com/azure/bastion/)防禦整合，可讓您透過瀏覽器連接到您的虛擬機器。 您必須先在實驗室虛擬機器上啟用瀏覽器連線。

身為實驗室的擁有者，您可以啟用透過瀏覽器存取所有實驗室虛擬機器的程式。 不需要額外用戶端、代理程式或軟體。 Azure 防禦可透過 TLS 直接在 Azure 入口網站中，為您的虛擬機器提供安全且順暢的 RDP/SSH 連線能力。 當您透過 Azure 防禦進行連接時，您的虛擬機器不需要公用 IP 位址。 如需詳細資訊，請參閱[什麼是 Azure 防禦？](../bastion/bastion-overview.md)


本文說明如何在實驗室虛擬機器上啟用瀏覽器連線。

## <a name="prerequisites"></a>先決條件 
在現有實驗室的虛擬網路中部署防禦主機 **（或）** 將您的實驗室與防禦設定的虛擬網路連線。 

若要瞭解如何在虛擬網路中部署防禦主機，請參閱[建立 Azure 防禦主機](../bastion/bastion-create-host-portal.md)。 建立防禦主機時，請選取實驗室的虛擬網路。 

首先，您必須在防禦虛擬網路中建立第二個子網，因為 AzureBastionSubnet 不允許建立其中的非防禦資源。 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>在防禦虛擬網路中建立第二個子網
您無法在 Azure 防禦子網中建立實驗室 Vm。 在防禦虛擬網路中建立另一個子網，如下圖所示：

![Azure 防禦虛擬網路中的第二個子網](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>在子網中啟用 VM 建立
現在，請遵循下列步驟，以啟用在此子網中建立 Vm： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側導覽功能表上的 [**所有服務**]。 
1. 從清單中選取 [ **DevTest Labs** ]。 
1. 從實驗室清單中，選取*您的實驗室*。 

    > [!NOTE]
    > Azure 防禦功能現已在下欄區域正式推出：美國西部、美國東部、西歐、美國中南部、澳大利亞東部和日本東部。 因此，如果您的實驗室不在其中一個區域中，請在其中一個區域中建立實驗室。 
    
1. 在左側功能表的 [設定] 區段中，選取 [**設定****和原則**]。 
1. 選取 [**虛擬網路**]。
1. 從工具列中選取 [**新增**]。 
1. 選取已部署防禦主機的**虛擬網路**。 
1. 選取 Vm 的子網，而不是您稍早建立的**AzureBastionSubnet**。 關閉頁面，如果您在底部的清單中看不到子網，請將它重新開啟。 

    ![在子網中啟用 VM 建立](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. 選取 [**在虛擬機器建立時使用**] 選項。 
1. 在工具列上選取 [儲存]。 
1. 如果您有實驗室的舊虛擬網路，請選取 [**...* ]，將它移除。 和**移除**。 

## <a name="enable-browser-connection"></a>啟用瀏覽器連接 

一旦您在實驗室中設定了防禦虛擬網路之後，身為實驗室擁有者，您就可以在實驗室虛擬機器上啟用瀏覽器連線。

若要在實驗室虛擬機器上啟用瀏覽器連接，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至*您的實驗室*。
1. 選取 [組態和原則]****。
1. 在 [**設定**] 中，選取 **[瀏覽器連線]**。 如果您沒有看到此選項，請關閉 [設定**原則**] 頁面，然後重新開啟它。 

    ![啟用瀏覽器連接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>後續步驟
請參閱下列文章，以瞭解如何使用瀏覽器連線到您的 Vm：[透過瀏覽器連線到您的虛擬機器](connect-virtual-machine-through-browser.md)
