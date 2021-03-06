---
title: 將資源部署到租用戶
description: 描述如何在 Azure Resource Manager 範本中的租用戶範圍部署資源。
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 45541bcbea5a80e55dbc9f80e1eae8e17189bf6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945438"
---
# <a name="create-resources-at-the-tenant-level"></a>在租用戶層級建立資源

當您的組織成熟時，可能需要定義和指派 Azure AD 租用戶中的[原則](../../governance/policy/overview.md)或[角色型存取控制](../../role-based-access-control/overview.md)。 您可以使用租用戶層級範本，以宣告方式套用原則，並在全域層級指派角色。

## <a name="supported-resources"></a>支援的資源

您可以在租用戶層級部署下列資源類型：

* [部署](/azure/templates/microsoft.resources/deployments) - 適用於部署至管理群組或訂用帳戶的巢狀範本。
* [managementGroups](/azure/templates/microsoft.management/managementgroups)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>結構描述

您用於租用戶部署的結構描述與用於資源群組部署的結構描述不同。

針對範本，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

所有部署範圍的參數檔案結構描述都相同。 針對參數檔案，請使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>必要的存取權

部署範本的主體必須具備在租用戶範圍建立資源的權限。 主體必須擁有執行部署動作 (`Microsoft.Resources/deployments/*`) 並建立在範本中定義之資源的權限。 例如，若要建立管理群組，主體必須具備租用戶範圍的「參與者」權限。 若要建立角色指派，主體必須具備「擁有者」權限。

Azure Active Directory 的全域管理員不會自動具備指派角色的權限。 若要在租用戶範圍啟用範本部署，全域管理員必須執行下列步驟：

1. 提升帳戶存取權，讓全域管理員可以指派角色。 如需詳細資訊，請參閱[提高存取權以管理所有 Azure 訂用帳戶和管理群組](../../role-based-access-control/elevate-access-global-admin.md)。

1. 將「擁有者」或「參與者」指派給需要部署範本的主體。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

主體現在具備部署範本所需的權限。

## <a name="deployment-commands"></a>部署命令

用於租用戶部署的命令與用於資源群組部署的命令不同。

針對 Azure CLI，使用 [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)：

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

針對 Azure PowerShell，使用 [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

針對 REST API，使用[部署 - 在租用戶範圍建立或更新](/rest/api/resources/deployments/createorupdateattenantscope)。

## <a name="deployment-location-and-name"></a>部署位置和名稱

針對租用戶層級部署，您必須提供部署的位置。 部署的位置與您部署的資源位置不同。 部署位置會指定部署資料的儲存位置。

您可以提供部署的名稱，或使用預設的部署名稱。 預設名稱是範本檔案的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

對於每個部署名稱而言，此位置是不可變的。 當某個位置已經有名稱相同的現有部署時，您無法在其他位置建立部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="use-template-functions"></a>使用範本函式

針對租用戶部署，使用範本函式時有一些重要考量：

* **不**支援 [resourceGroup()](template-functions-resource.md#resourcegroup) 函式。
* **不**支援 [subscription()](template-functions-resource.md#subscription) 函式。
* 支援 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函式。
* 使用 [tenantResourceId()](template-functions-resource.md#tenantresourceid) 函式取得在租用戶層級部署之資源的資源識別碼。

  例如，若要取得原則定義的資源識別碼，使用：

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  傳回的資源識別碼具有下列格式：

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>建立管理群組

[下列範本](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg)會建立管理群組。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>指派角色

[下列範本](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment)會在租用戶範圍指派角色。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何指派角色，請參閱[使用 RBAC 和 Azure Resource Manager 範本管理對 Azure 資源的存取](../../role-based-access-control/role-assignments-template.md)。
* 您也可以在[訂用帳戶層級](deploy-to-subscription.md)或[管理群組層級](deploy-to-management-group.md)部署範本。
