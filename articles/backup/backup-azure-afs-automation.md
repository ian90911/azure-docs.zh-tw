---
title: 使用 PowerShell 來備份 Azure 檔案共用
description: 在本文中，您將瞭解如何使用 Azure 備份服務和 PowerShell 來備份 Azure 檔案儲存體檔案共用。
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 18c03eda9d9daca3a0fa536843e32f7fc3158287
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85971023"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>使用 PowerShell 來備份 Azure 檔案共用

本文說明如何使用 Azure PowerShell，透過[Azure 備份](backup-overview.md)復原服務保存庫來備份 Azure 檔案儲存體的檔案共用。

本文說明如何：

> [!div class="checklist"]
>
> * 設定 PowerShell 並註冊復原服務提供者。
> * 建立復原服務保存庫。
> * 設定 Azure 檔案共用的備份。
> * 執行備份作業。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

* [深入瞭解](backup-azure-recovery-services-vault-overview.md)復原服務保存庫。
* 請參閱 Azure 程式庫中的 Azurerm.recoveryservices [Cmdlet 參考](/powershell/module/az.recoveryservices)參考。
* 請參閱下列適用于復原服務的 PowerShell 物件階層：

  ![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>設定 PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

設定 PowerShell，如下所示：

1. [下載最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)。

    > [!NOTE]
    > 備份 Azure 檔案共用所需的最低 PowerShell 版本是 Az. Azurerm.recoveryservices 2.6.0。 使用最新版本或至少最低版本，可協助您避免現有腳本的問題。 使用下列 PowerShell 命令安裝最小版本：
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. 使用下列命令，尋找 Azure 備份的 PowerShell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 備份、Azure Site Recovery 和復原服務保存庫的別名和 Cmdlet。 以下是您可能會看到的範例。 這不是 Cmdlet 的完整清單。

    ![復原服務 Cmdlet 清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用 **Connect-AzAccount** 登入您的 Azure 帳戶。
5. 在出現的網頁上，系統會提示您輸入您的帳號憑證。

    或者，您可以使用 **-Credential**，將您的帳號憑證納入**disconnect-azaccount** Cmdlet 中作為參數。

    如果您是代表租使用者工作的 CSP 合作夥伴，請將客戶指定為租使用者。 使用其租使用者識別碼或租使用者主功能變數名稱稱。 例如**Connect-disconnect-azaccount-Tenant "fabrikam.com"**。

6. 將您想要使用的訂用帳戶與帳戶建立關聯，因為一個帳戶可以有數個訂閱：

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 如果您是第一次使用 Azure 備份，請使用**register-azresourceprovider** Cmdlet 向您的訂用帳戶註冊 Azure 復原服務提供者：

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 確認已成功註冊提供者：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令輸出中，確認 [ **RegistrationState** ] 變更為 [**已註冊**]。 如果不是，請再次執行**register-azresourceprovider** Cmdlet。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是 Resource Manager 資源，因此您必須將它放在資源群組中。 您可以使用現有的資源群組，也可以使用**remove-azresourcegroup** Cmdlet 來建立資源群組。 當您建立資源群組時，請指定其名稱和位置。

請遵循下列步驟來建立復原服務保存庫：

1. 如果您沒有現有的資源群組，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) Cmdlet 建立一個新的。 在此範例中，我們會在美國西部區域建立資源群組：

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. 使用[new-azrecoveryservicesvault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) Cmdlet 來建立保存庫。 為您用於資源群組的保存庫指定相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要查看訂用帳戶中的所有保存庫，請使用[new-azrecoveryservicesvault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)：

```powershell
Get-AzRecoveryServicesVault
```

輸出如下所示。 請注意，輸出會提供相關聯的資源群組和位置。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>設定保存庫內容

將保存庫物件儲存在變數中，並設定保存庫內容。

許多 Azure 備份 Cmdlet 都需要復原服務保存庫物件做為輸入，因此將保存庫物件儲存在變數中是很方便的。

保存庫內容是保存庫中受保護的資料類型。 使用[set-azrecoveryservicesvaultcoNtext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)來設定它。 設定內容之後，它會套用至所有後續的 Cmdlet。

下列範例會設定**testvault**的保存庫內容：

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保存庫識別碼

我們計畫根據 Azure PowerShell 方針來取代保存庫內容設定。 相反地，您可以儲存或提取保存庫識別碼，並將它傳遞至相關的命令。 如果您尚未設定保存庫內容，或想要指定要針對特定保存庫執行的命令，請將保存庫識別碼傳遞 `-vaultID` 給所有相關的命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>設定備份原則

備份原則會指定備份的排程，以及保留備份復原點的時間長度。

備份原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。 您可以設定每日、每週、每月或每年保留的備份。

以下是一些適用于備份原則的 Cmdlet：

* 使用[AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)來查看預設的備份原則保留期。
* 使用[get-azrecoveryservicesbackupschedulepolicyobject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)來查看預設的備份原則排程。
* 使用[使用 get-azrecoveryservicesbackupprotectionpolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)建立新的備份原則。 您可以輸入排程和保留原則物件做為輸入。

根據預設，開始時間是在排程原則物件中定義。 使用下列範例，將開始時間變更為所需的開始時間。 所需的開始時間應為全球協調時間（UTC）。 此範例假設每日備份所需的開始時間為 01:00 AM UTC。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 您只需要以30分鐘的倍數提供開始時間。 在上述範例中，它只能是 "01:00:00" 或 "02:30:00"。 開始時間不得為 "01:15:00"。

下列範例會將排程原則和保留原則儲存在變數中。 然後，它會使用這些變數做為新原則的參數（**NewAFSPolicy**）。 **NewAFSPolicy** 會每日進行備份，並保留 30 天。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出大致如下：

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>啟用備份

定義備份原則之後，您可以使用原則來啟用 Azure 檔案共用的保護。

### <a name="retrieve-a-backup-policy"></a>取出備份原則

您可以使用[使用 get-azrecoveryservicesbackupprotectionpolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)來提取相關的原則物件。 使用此 Cmdlet 來查看與工作負載類型相關聯的原則，或取得特定的原則。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>取得工作負載類型的原則

下列範例會抓取工作負載類型**AzureFiles**的原則：

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

輸出大致如下：

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell 中**BackupTime**欄位的時區是 UTC 格式。 當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。

#### <a name="retrieve-a-specific-policy"></a>取出特定原則

下列原則會抓取名為**dailyafs**的備份原則：

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>啟用保護並套用原則

使用[enable-enable-azrecoveryservicesbackupprotection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)啟用保護。 當原則與保存庫建立關聯之後，就會根據原則排程觸發備份。

下列範例會在 [儲存體帳戶] **testStorageAcct**中，使用原則**Dailyafs**來啟用 Azure 檔案共用**testAzureFileShare**的保護：

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令會等到設定保護作業完成，並提供類似于下列範例的輸出：

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

如需有關如何取得儲存體帳戶的檔案共用清單的詳細資訊，請參閱[這篇文章](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageshare?view=azps-4.3.0)。

## <a name="important-notice-backup-item-identification"></a>重要通知：備份專案識別

本節概述 Azure 檔案共用備份中的重要變更，以準備正式推出。

啟用 Azure 檔案共用的備份時，使用者會為客戶提供一個檔案共用名稱作為機構名稱，並建立一個備份專案。 備份專案的名稱是 Azure 備份服務所建立的唯一識別碼。 識別碼通常是使用者易記的名稱。 但是，若要處理虛刪除的案例，其中可刪除檔案共用，而且可以使用相同的名稱建立另一個檔案共用，Azure 檔案共用的唯一識別現在是識別碼。

若要知道每個專案的唯一識別碼，請執行**backup-azrecoveryservicesbackupitem**命令與**backupManagementType**和**WorkloadType**相關的篩選器，以取得所有相關的專案。 然後觀察傳回的 PowerShell 物件/回應中的 [名稱] 欄位。

我們建議您列出專案，然後從回應中的 [名稱] 欄位取得其唯一名稱。 使用此值來篩選具有*Name*參數的專案。 否則，請使用*FriendlyName*參數來抓取具有其識別碼的專案。

> [!IMPORTANT]
> 請確定 PowerShell 已升級為 Azure 檔案共用備份的最小版本（Az. Azurerm.recoveryservices 2.6.0）。 在此版本中， *FriendlyName*篩選器適用于**backup-azrecoveryservicesbackupitem**命令。
>
> 將 Azure 檔案共用的名稱傳遞給*FriendlyName*參數。 如果您將檔案共用的名稱傳遞給*name*參數，這個版本會擲回警告，將名稱傳遞給*FriendlyName*參數。
>
> 未安裝最低版本可能會導致現有的腳本失敗。 使用下列命令來安裝 PowerShell 的最低版本：
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>觸發隨選備份

使用[備份 backup-azrecoveryservicesbackupitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0)來執行受保護 Azure 檔案共用的隨選備份：

1. 使用[AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)在保存備份資料的保存庫中，從容器中取出儲存體帳戶。
2. 若要開始備份作業，請使用[backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)取得 Azure 檔案共用的相關資訊。
3. 使用[備份 backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)來執行隨選備份。

執行隨選備份，如下所示：

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

此命令會傳回具有要追蹤之識別碼的作業，如下列範例所示：

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

執行備份時，會使用 Azure 檔案共用快照集。 通常作業會在命令傳回此輸出時完成。

## <a name="next-steps"></a>後續步驟

* 瞭解[Azure 入口網站中的備份 Azure 檔案儲存體](backup-afs.md)。
* 請參閱[GitHub 上的範例腳本](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup)，以使用 Azure 自動化 runbook 來排程備份。
