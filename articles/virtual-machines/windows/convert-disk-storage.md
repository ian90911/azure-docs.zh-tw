---
title: 轉換 standard 和 premium SSD 之間的受控磁片儲存體
description: 如何使用 Azure PowerShell，將 Azure 受控磁片從 Standard 轉換為 Premium 或 Premium 至 Standard。
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4d43311ece7cb72e9f4abb3f8a18f3550fe48f71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660686"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新受控磁碟的儲存體類型

Azure 受控磁片有四種磁片類型： Azure ultra Ssd （預覽）、premium SSD、標準 SSD 和標準 HDD。 根據您的效能需求，您可以在三種 GA 磁片類型（premium SSD、標準 SSD 和標準 HDD）之間切換。 您還無法從或到 ultra SSD 進行切換，您必須部署一個新的。

非受控磁片不支援此功能。 但是，您可以輕鬆地[將非受控磁片轉換為受控磁片](convert-unmanaged-to-managed-disks.md)，以便在磁片類型之間切換。

 

## <a name="prerequisites"></a>必要條件

* 因為轉換需要重新開機虛擬機器（VM），所以您應該在預先存在的維護期間排程磁片儲存體的遷移。
* 如果您的磁片未受管理，請先[將它轉換為受控磁片](convert-unmanaged-to-managed-disks.md)，以便在儲存體選項之間切換。

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>在 Premium 與 Standard 之間切換 VM 的所有受控磁片

此範例示範如何將 VM 的所有磁片從標準轉換至高階儲存體，或從 Premium 轉換至標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換成可支援進階儲存體的大小：

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在 Standard 和 Premium 之間切換個別受控磁片

針對您的開發/測試工作負載，您可能會想要混合使用標準和 Premium 磁片，以降低成本。 您可以選擇只升級需要較佳效能的磁片。 此範例示範如何將單一 VM 磁片從標準轉換成 Premium 儲存體，或從 Premium 轉換成標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會示範如何切換至支援高階儲存體的大小：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>在 Azure 入口網站中將受控磁片從標準轉換成 Premium

請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站中的**虛擬機器**清單中選取 VM。
3. 如果 VM 未停止，請選取 [VM**總覽**] 窗格頂端的 [**停止**]，並等候 vm 停止。
3. 在 VM 的窗格中，從功能表中選取 [**磁片**]。
4. 選取您要轉換的磁片。
5. 從功能表**中選取 [** 設定]。
6. 將**帳戶類型**從**標準 HDD**變更為**進階 SSD**。
7. 按一下 [**儲存**]，然後關閉 [磁片] 窗格。

磁片類型轉換為瞬間。 您可以在轉換後啟動 VM。

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>在標準 HDD 和標準 SSD 之間切換受控磁片 

此範例示範如何將單一 VM 磁片從標準 HDD 轉換成標準 SSD 或從標準 SSD 到標準 HDD：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>後續步驟

使用[快照集](snapshot-copy-managed-disk.md)來製作 VM 的唯讀複本。
