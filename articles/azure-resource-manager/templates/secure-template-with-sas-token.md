---
title: 使用 SAS 權杖安全地部署範本
description: 使用受到 SAS 權杖保護的 Azure Resource Manager 範本，將資源部署至 Azure。 顯示 Azure PowerShell 和 Azure CLI。
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80156390"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>使用 SAS 權杖部署私人 ARM 範本

當您的 Azure Resource Manager （ARM）範本位於儲存體帳戶時，您可以限制對範本的存取，以避免公開公開。 您可以藉由建立範本的共用存取簽章（SAS）權杖，並在部署期間提供該權杖，來存取受保護的範本。 本文說明如何使用 Azure PowerShell 或 Azure CLI 來部署具有 SAS 權杖的範本。

## <a name="create-storage-account-with-secured-container"></a>建立具有安全容器的儲存體帳戶

下列腳本會建立儲存體帳戶和已關閉公用存取的容器。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>將範本上傳至儲存體帳戶

現在，您已經準備好將範本上傳至儲存體帳戶。 提供您想要使用之範本的路徑。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>在部署期間提供 SAS Token

若要在儲存體帳戶中部署私人範本，請產生 SAS Token 並將它包含在範本的 URI 中。 設定到期時間，以允許足夠的時間來完成部署。

> [!IMPORTANT]
> 僅有帳戶擁有者可以存取包含範本的 Blob。 不過，當您建立 Blob 的 SAS Token 時，具備該 URI 的任何人都可以存取該 Blob。 如果另一位使用者攔截了 URI，該使用者也能存取範本。 SAS Token 是限制存取範本的好方法，但您不應該將機密資料 (如密碼) 直接包含在範本中。
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

如需使用包含已連結範本的 SAS Token 範例，請參閱 [透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。


## <a name="next-steps"></a>後續步驟
* 如需部署範本的簡介，請參閱[使用 ARM 範本部署資源和 Azure PowerShell](deploy-powershell.md)。
* 若要在範本中定義參數，請參閱 [編寫範本](template-syntax.md#parameters)。
