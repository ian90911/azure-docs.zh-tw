---
title: 使用在主機 Azure CLI 受控磁片上的加密來啟用端對端加密
description: 在主機上使用加密，以在您的 Azure 受控磁片上啟用端對端加密。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e0773515809ffdc50167a3cba1f767ac8635bcee
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502566"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-cli"></a>在主機上使用加密來啟用端對端加密-Azure CLI

當您在主機上啟用加密時，儲存在 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 如需在主機上進行加密的概念資訊，以及其他受控磁片加密類型，請參閱[VM 資料的主機端對端](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)加密加密。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支援區域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支援的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

您也可以透過程式設計的方式找到 VM 大小。 若要瞭解如何以程式設計方式取得它們，請參閱[尋找支援的 VM 大小](#finding-supported-vm-sizes)一節。

## <a name="prerequisites"></a>先決條件

為了能夠針對您的 Vm 或虛擬機器擴展集使用主機上的加密，您必須在訂用帳戶上啟用此功能。 使用您的訂用帳戶識別碼傳送電子郵件給， encryptionAtHost@microsoft.com 以取得您的訂閱已啟用的功能。

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>建立 Azure Key Vault 和 DiskEncryptionSet

啟用此功能之後，您必須設定 Azure Key Vault 和 DiskEncryptionSet （如果尚未這麼做）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>為連接至 VM 和虛擬機器擴展集的磁片啟用主機加密

您可以使用 API **2020-06-01**和更新版本，在 vm 或虛擬機器擴展集的 securityProfile 下設定新的屬性 EncryptionAtHost，以在主機上啟用加密。

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>範例指令碼

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>針對使用客戶管理的金鑰連接至 VM 的磁片，在主機上啟用加密

使用稍早建立的 DiskEncryptionSet 資源 URI，建立具有受控磁片的 VM。

取代 `<yourPassword>` 、 `<yourVMName>` 、 `<yourVMSize>` 、、、 `<yourDESName>` `<yoursubscriptionID>` `<yourResourceGroupName>` 和 `<yourRegion>` ，然後執行腳本。

```azurecli
az group deployment create -g <yourResourceGroupName> \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" \
--parameters "virtualMachineName=<yourVMName>" "adminPassword=<yourPassword>" "vmSize=<yourVMSize>" "diskEncryptionSetId=/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" "region=<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>針對使用平臺管理的金鑰連接至 VM 的磁片，在主機上啟用加密

取代 `<yourPassword>` 、 `<yourVMName>` 、 `<yourVMSize>` 、 `<yourResourceGroupName>` 和 `<yourRegion>` ，然後執行腳本。

```azurecli
az group deployment create -g <yourResourceGroupName> \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" \
--parameters "virtualMachineName=<yourVMName>" "adminPassword=<yourPassword>" "vmSize=<yourVMSize>" "region=<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>尋找支援的 VM 大小

不支援舊版 VM 大小。 您可以透過下列其中一種方式來尋找支援的 VM 大小清單：

呼叫[資源 SKU API](/rest/api/compute/resourceskus/list) ，並檢查 `EncryptionAtHostSupported` 功能是否設定為**True**。

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

或者，呼叫[Get-azcomputeresourcesku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) PowerShell Cmdlet。

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>後續步驟

既然您已建立並設定這些資源，您可以使用它們來保護您的受控磁片。 下列連結包含範例腳本，每個都有個別的案例，可讓您用來保護受控磁片。

[Azure Resource Manager 範本範例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
