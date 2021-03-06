---
title: 適用于檔案 & Acl 的 Azure Data Lake Storage Gen2 PowerShell
description: 使用 PowerShell Cmdlet 來管理已啟用階層命名空間 (HNS) 之儲存體帳戶中 (ACL) 的目錄和檔案和目錄存取控制清單。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: d22b83e1f3464f6d87d2bc3821682b25e05d947b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142536"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 PowerShell 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl

本文說明如何使用 PowerShell 來建立和管理已啟用階層命名空間 (HNS) 的儲存體帳戶中的目錄、檔案和許可權。 

[Gen1 至 Gen2 對應](#gen1-gen2-map) | [提供意見反應](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](data-lake-storage-quickstart-create-account.md)指示以建立帳戶。
> * .NET Framework 已安裝4.7.2 或更新版本。 請參閱[下載 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
> * PowerShell 版本 `5.1` 或更高版本。

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

1. 使用下列命令，確認已安裝的 PowerShell 版本為 `5.1` 或更高。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升級您的 PowerShell 版本，請參閱[升級現有的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. 安裝**Az. Storage**模組。

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   如需有關如何安裝 PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="connect-to-the-account"></a>連線到帳戶

開啟 Windows PowerShell 命令視窗，然後使用命令登入您的 Azure 訂用帳戶 `Connect-AzAccount` ，並遵循畫面上的指示。

```powershell
Connect-AzAccount
```

如果您的身分識別與多個訂用帳戶相關聯，則請將您的使用中訂用帳戶設定為您要在其中建立和管理目錄之儲存體帳戶的訂用帳戶。 在此範例中，將 `<subscription-id>` 預留位置值取代為您的訂用帳戶識別碼。

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

接下來，選擇您希望命令取得儲存體帳戶授權的方式。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>選項1：使用 Azure Active Directory (AD 來取得授權) 

使用此方法時，系統可確保您的使用者帳戶具有適當的角色型存取控制， (RBAC) 指派和 ACL 許可權。 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>選項2：使用儲存體帳戶金鑰取得授權

使用此方法時，系統不會檢查 RBAC 或 ACL 許可權。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-container"></a>建立容器

容器會作為檔案的檔案系統。 您可以使用 Cmdlet 來建立一個 `New-AzStorageContainer` 。 

這個範例會建立名為的容器 `my-file-system` 。

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>建立目錄

使用 Cmdlet 建立目錄參考 `New-AzDataLakeGen2Item` 。 

這個範例會將名為的目錄新增 `my-directory` 至容器。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

這個範例會新增相同的目錄，但也會設定許可權、umask、屬性值和中繼資料值。 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>顯示目錄屬性

這個範例會使用 Cmdlet 取得目錄 `Get-AzDataLakeGen2Item` ，然後將屬性值列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

## <a name="rename-or-move-a-directory"></a>重新命名或移動目錄

使用 Cmdlet 重新命名或移動目錄 `Move-AzDataLakeGen2Item` 。

這個範例會將名稱的目錄重新命名 `my-directory` 為名稱 `my-new-directory` 。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> `-Force`如果您想要覆寫而不提示，請使用參數。

這個範例會將名為 `my-directory` 的目錄移到名為的子目錄 `my-directory-2` `my-subdirectory` 。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>刪除目錄

使用 Cmdlet 刪除目錄 `Remove-AzDataLakeGen2Item` 。

此範例刪除名為 `my-directory` 的目錄。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

您可以使用 `-Force` 參數來移除檔案，而不會出現提示。

## <a name="download-from-a-directory"></a>從目錄下載

使用 Cmdlet 從目錄下載檔案 `Get-AzDataLakeGen2ItemContent` 。

此範例從名為 `my-directory` 的目錄下載名為 `upload.txt` 的檔案。 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>列出目錄內容

使用 Cmdlet 來列出目錄的內容 `Get-AzDataLakeGen2ChildItem` 。 您可以使用選擇性參數 `-OutputUserPrincipalName` 來取得 (的名稱，而不是使用者的物件識別碼) 。

這個範例會列出名為之目錄的內容 `my-directory` 。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

下列範例會列出 `ACL` `Permissions` `Group` `Owner` 目錄中每個專案的、、和屬性。 `-FetchProperty`需要參數才能取得屬性的值 `ACL` 。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

若要列出容器的內容，請省略 `-Path` 命令中的參數。

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳至目錄

使用 Cmdlet 將檔案上傳至目錄 `New-AzDataLakeGen2Item` 。

此範例將名為 `upload.txt` 的檔案上傳至名為 `my-directory` 的目錄。 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

這個範例會上傳相同的檔案，但是會設定目的地檔案的許可權、umask、屬性值和中繼資料值。 這個範例也會將這些值列印到主控台。

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>顯示檔案屬性

這個範例會使用 Cmdlet 來取得檔案 `Get-AzDataLakeGen2Item` ，然後將屬性值列印到主控台。

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>刪除檔案

使用 Cmdlet 來刪除檔案 `Remove-AzDataLakeGen2Item` 。

這個範例會刪除名為的檔案 `upload.txt` 。 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

您可以使用 `-Force` 參數來移除檔案，而不會出現提示。

## <a name="manage-access-permissions"></a>管理存取權限

您可以取得、設定及更新目錄和檔案的存取權限。 這些許可權會在 (Acl) 的存取控制清單中加以捕捉。

> [!NOTE]
> 如果您使用 Azure Active Directory (Azure AD) 來授權命令，請確定已將[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)指派給您的安全性主體。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-an-acl"></a>取得 ACL

使用 Cmdlet 取得目錄或檔案的 ACL `Get-AzDataLakeGen2Item` 。

這個範例會取得**容器**根目錄的 acl，然後將 acl 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

這個範例會取得**目錄**的 acl，然後將 acl 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

這個範例會取得**檔案的 acl，然後**將 acl 列印到主控台。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下圖顯示取得目錄的 ACL 之後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

在此範例中，擁有使用者具有讀取、寫入和執行權限。 擁有群組只有讀取和執行權限。 如需存取控制清單的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="set-an-acl"></a>設定 ACL

使用 `set-AzDataLakeGen2ItemAclObject` Cmdlet 來建立擁有使用者、擁有群組或其他使用者的 ACL。 然後，使用 `Update-AzDataLakeGen2Item` Cmdlet 來認可 ACL。

這個範例會針對擁有使用者、擁有群組或其他使用者，在**容器**的根目錄上設定 acl，然後將 acl 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

這個範例會在擁有使用者、擁有群組或其他使用者的**目錄**上設定 acl，然後將 acl 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
這個範例會針對擁有使用者、擁有群組或其他使用者 **，在檔案**上設定 acl，然後將 acl 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下圖顯示設定檔案的 ACL 之後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

在此範例中，擁有使用者和擁有群組只有讀取和寫入權限。 所有其他使用者具備寫入和執行權限。 如需存取控制清單的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。


### <a name="set-acls-on-all-items-in-a-container"></a>在容器中的所有專案上設定 Acl

您可以使用 `Get-AzDataLakeGen2Item` 和 `-Recurse` 參數搭配 Cmdlet，以 `Update-AzDataLakeGen2Item` 遞迴方式設定容器中目錄和檔案的 ACL。 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl

$Token = $Null
do
{
     $items = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -ContinuationToken $Token    
     if($items.Count -le 0) { Break;}
     $items | Update-AzDataLakeGen2Item -Acl $acl
     $Token = $items[$items.Count -1].ContinuationToken;
}
While ($Token -ne $Null) 
```

### <a name="add-or-update-an-acl-entry"></a>新增或更新 ACL 專案

首先，取得 ACL。 然後，使用 `set-AzDataLakeGen2ItemAclObject` Cmdlet 來新增或更新 ACL 專案。 使用 `Update-AzDataLakeGen2Item` Cmdlet 來認可 ACL。

這個範例會在使用者的**目錄**上建立或更新 ACL。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>移除 ACL 專案

這個範例會從現有的 ACL 移除專案。

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Gen1 至 Gen2 對應

下表顯示用於 Data Lake Storage Gen1 的 Cmdlet 對應至 Data Lake Storage Gen2 之 Cmdlet 的方式。

|Data Lake Storage Gen1 Cmdlet| Data Lake Storage Gen2 Cmdlet| 附註 |
|--------|---------|-----|
|AzDataLakeStoreChildItem|AzDataLakeGen2ChildItem|根據預設，AzDataLakeGen2ChildItem Cmdlet 只會列出第一個層級的子專案。 -遞迴參數會以遞迴方式列出子專案。 |
|AzDataLakeStoreItem<br>AzDataLakeStoreItemAclEntry<br>AzDataLakeStoreItemOwner<br>AzDataLakeStoreItemPermission|AzDataLakeGen2Item|AzDataLakeGen2Item 指令程式的輸出專案具有下列屬性： Acl、擁有者、群組、許可權。|
|AzDataLakeStoreItemContent|AzDataLakeGen2FileContent|AzDataLakeGen2FileContent Cmdlet 會將檔內容下載到本機檔案。|
|移動-AzDataLakeStoreItem|移動-AzDataLakeGen2Item||
|新增-AzDataLakeStoreItem|新增-AzDataLakeGen2Item|此 Cmdlet 會從本機檔案上傳新的檔案內容。|
|移除-AzDataLakeStoreItem|移除-AzDataLakeGen2Item||
|設定-AzDataLakeStoreItemOwner<br>設定-AzDataLakeStoreItemPermission<br>設定-AzDataLakeStoreItemAcl|更新-AzDataLakeGen2Item|AzDataLakeGen2Item 指令程式只會更新單一專案，而不會以遞迴方式進行。 如果想要以遞迴方式更新，請使用 AzDataLakeStoreChildItem Cmdlet 來列出專案，然後將管線加入至 AzDataLakeGen2Item Cmdlet。|
|測試-AzDataLakeStoreItem|AzDataLakeGen2Item|如果專案不存在，AzDataLakeGen2Item 指令程式將會報告錯誤。|

## <a name="see-also"></a>另請參閱

* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [儲存體 PowerShell Cmdlet](/powershell/module/az.storage)
