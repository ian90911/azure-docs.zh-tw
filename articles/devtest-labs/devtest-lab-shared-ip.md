---
title: 了解 Azure DevTest Labs 中的共用 IP 位址 | Microsoft Docs
description: 了解 Azure DevTest Labs 如何使用共用 IP 位址，將需要存取您的實驗室 VM 的公用 IP 位址減到最少。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 36a5da1b2b6252d0adb480a622c461b33425e675
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85484089"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>了解 Azure DevTest Labs 中的共用 IP 位址

Azure DevTest Labs 讓實驗室 VM 共用相同公用 IP 位址，將需要存取您的個人實驗室 VM 的公用 IP 位址數目減到最少。  本文說明共用 IP 的運作方式和其相關的組態選項。

## <a name="shared-ip-setting"></a>共用 IP 設定

當您建立實驗室時，它會建立在虛擬網路的子網中。  根據預設，建立這個子網路時會將 [啟用共用公用 IP]**** 設為 [是]**。  此設定會為整個子網路建立一個公用 IP 位址。  如需設定虛擬網路和子網路的詳細資訊，請參閱[設定 Azure DevTest Labs 中的虛擬網路](devtest-lab-configure-vnet.md)。

![新的實驗室子網路](media/devtest-lab-shared-ip/lab-subnet.png)

針對現有的實驗室，您可以藉由選取 [設定和原則 > 虛擬網路]**** 來啟用這個選項。 然後，從清單中選取虛擬網路，並且針對選取的子網路選擇 [啟用共用公用 IP]****。 如果您不想要在實驗室 VM 之間共用公用 IP 位址，您也可以在任何實驗室中停用這個選項。

此實驗室中建立任何 VM 會預設為使用共用 IP。  建立 VM 時，可以在 [ **IP 位址**設定] 下的 [ **Advanced settings** ] 頁面中觀察這項設定。

![新的 VM](media/devtest-lab-shared-ip/new-vm.png)

- **共用：** 建立為**共用**的所有 VM 歸類到一個資源群組 (RG)。 針對該 RG 指派單一 IP 位址，RG 中的所有 VM 將會使用該 IP 位址。
- **公用：** 您建立的每個 VM 有它自己的 IP 位址，而且建立在它自己的資源群組。
- **私人：** 您建立的每個 VM 使用私人 IP 位址。 您無法使用遠端桌面直接從網際網路連接到此 VM。

每當已啟用共用 IP 的 VM 新增至子網路時，DevTest Labs 就會自動將 VM 新增至負載平衡器，並且在公用 IP 位址上指派 TCP 通訊埠號碼，以轉送至 VM 上的 RDP 連接埠。  

## <a name="using-the-shared-ip"></a>使用共用 IP

- **Linux 使用者**：您可以使用 IP 位址或完整網域名稱，後面緊接冒號，再接著連接埠，以 SSH 至 VM。 例如，在下圖中，連線至 VM 的 RDP 位址是 `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`。

  ![VM 範例](media/devtest-lab-shared-ip/vm-info.png)

- **Windows 使用者**：選取 Azure 入口網站中的 [連線]**** 按鈕，下載預先設定的 RDP 檔案，並且存取 VM。

## <a name="next-steps"></a>後續步驟

* [在 Azure DevTest Labs 中定義實驗室原則](devtest-lab-set-lab-policy.md)
* [在 Azure DevTest Labs 中設定虛擬網路](devtest-lab-configure-vnet.md)





