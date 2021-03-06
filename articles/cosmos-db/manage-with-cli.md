---
title: 使用 Azure CLI 管理 Azure Cosmos DB 資源
description: 使用 Azure CLI 來管理您的 Azure Cosmos DB 帳戶、資料庫和容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: fe348c2bbd901934c6365be6efefafb44ef8d875
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262390"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>使用 Azure CLI 管理 Azure Cosmos 資源

下列指南說明使用 Azure CLI 自動管理 Azure Cosmos DB 帳戶、資料庫及容器的常見命令。 您可以在 [Azure CLI 參考](https://docs.microsoft.com/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。 您也可以在[適用於 Azure Cosmos DB 的 Azure CLI 範例](cli-samples.md)中找到更多範例，包括如何針對 MongoDB、Gremlin、Cassandra 及資料表 API建立和管理 Cosmos DB 帳戶、資料庫和容器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.6.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 帳戶

下列各節會示範如何管理 Azure Cosmos 帳戶，包括：

* [建立 Azure Cosmos 帳戶](#create-an-azure-cosmos-db-account)
* [新增或移除區域](#add-or-remove-regions)
* [啟用多重區域寫入](#enable-multiple-write-regions)
* [設定區域容錯移轉優先順序](#set-failover-priority)
* [啟用自動容錯移轉](#enable-automatic-failover)
* [觸發手動容錯移轉](#trigger-manual-failover)
* [列出帳戶金鑰](#list-account-keys)
* [列出唯讀帳戶金鑰](#list-read-only-account-keys)
* [列出連接字串](#list-connection-strings)
* [重新產生帳戶金鑰](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

建立具有 SQL API 的 Azure Cosmos DB 帳戶、美國西部2和美國東部2區域中的會話一致性：

> [!IMPORTANT]
> Azure Cosmos 帳戶名稱必須是小寫且小於44個字元。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>新增或移除區域

建立具有兩個區域的 Azure Cosmos 帳戶、新增區域，以及移除區域。

> [!NOTE]
> 您不能同時新增或移除 `locations` 區域，以及變更 Azure Cosmos 帳戶的其他屬性。 修改區域必須與帳戶資源的任何其他變更分開作業。
> [!NOTE]
> 此命令可讓您新增及移除區域，但不允許您修改容錯移轉優先順序或觸發手動容錯移轉。 請參閱[設定容錯移轉優先順序](#set-failover-priority)和[觸發手動容錯移轉](#trigger-manual-failover)。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>啟用多個寫入區域

啟用 Cosmos 帳戶的多宿主

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>設定容錯移轉優先順序

針對設定為自動容錯移轉的 Azure Cosmos 帳戶，設定容錯移轉優先順序

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a> 啟用自動容錯移轉

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>觸發手動容錯移轉

> [!CAUTION]
> 變更優先順序 = 0 的區域將會觸發 Azure Cosmos 帳戶的手動容錯移轉。 變更其他優先順序則不會觸發容錯移轉。

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a>列出所有帳戶金鑰

取得 Cosmos 帳戶的所有金鑰。

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>列出唯讀帳戶金鑰

取得 Cosmos 帳戶的唯讀金鑰。

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>列出連接字串

取得 Cosmos 帳戶的連接字串。

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>重新產生帳戶金鑰

為 Cosmos 帳戶重新產生新的金鑰。

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB 資料庫

下列各節會示範如何管理 Azure Cosmos DB 資料庫，包括：

* [建立資料庫](#create-a-database)
* [建立具有共用輸送量的資料庫](#create-a-database-with-shared-throughput)
* [變更資料庫輸送量](#change-database-throughput)
* [管理資料庫上的鎖定](#manage-lock-on-a-database)

### <a name="create-a-database"></a>建立資料庫

建立 Cosmos 資料庫。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>建立具有共用輸送量的資料庫

建立具有共用輸送量的 Cosmos 資料庫。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="change-database-throughput"></a>變更資料庫輸送量

增加 Cosmos 資料庫的輸送量（以 1000 RU/秒為單位）。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="manage-lock-on-a-database"></a>管理資料庫上的鎖定

將刪除鎖定放在資料庫上。 若要深入瞭解如何啟用，請參閱[防止 sdk 的變更](role-based-access-control.md#preventing-changes-from-cosmos-sdk)。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='myDatabase'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB 容器

下列各節會示範如何管理 Azure Cosmos DB 容器，包括：

* [建立容器](#create-a-container)
* [建立已啟用 TTL 的容器](#create-a-container-with-ttl)
* [建立含有自訂索引原則的容器](#create-a-container-with-a-custom-index-policy)
* [變更容器輸送量](#change-container-throughput)
* [管理容器上的鎖定](#manage-lock-on-a-container)

### <a name="create-a-container"></a>建立容器

使用預設索引原則、分割區索引鍵和 RU/秒400建立 Cosmos 容器。

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-ttl"></a>建立具有 TTL 的容器

建立已啟用 TTL 的 Cosmos 容器。

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>建立具有自訂索引原則的容器

建立 Cosmos 容器，其中包含自訂索引原則、空間索引、複合索引、分割區索引鍵，以及400的 RU/秒。

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>變更容器輸送量

增加 Cosmos 容器的輸送量（以 1000 RU/秒為單位）。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="manage-lock-on-a-container"></a>管理容器上的鎖定

在容器上放置刪除鎖定。 若要深入瞭解如何啟用，請參閱[防止 sdk 的變更](role-based-access-control.md#preventing-changes-from-cosmos-sdk)。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='myDatabase'
containerName='myContainer'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱：

- [安裝 Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI 參考](https://docs.microsoft.com/cli/azure/cosmosdb)
- [適用於 Azure Cosmos DB 的其他 Azure CLI 範例](cli-samples.md)
