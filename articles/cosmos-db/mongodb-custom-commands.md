---
title: MongoDB 擴充命令，用來管理 Azure Cosmos DB 的 MongoDB API 中的資料
description: 本文說明如何使用 MongoDB 延伸模組命令來管理 Azure Cosmos DB 的 MongoDB API 中儲存的資料。
author: LuisBosquez
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2020
ms.author: lbosq
ms.openlocfilehash: 7879793cbbb029468e84b14f8e8eee793cf4c530
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260724"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 延伸模組命令來管理 Azure Cosmos DB 的 MongoDB API 中儲存的資料 

下列檔包含 Azure Cosmos DB 的適用于 MongoDB 的 API 所特有的自訂動作命令。 這些命令可以用來建立和取得[Azure Cosmos DB 容量模型](databases-containers-items.md)特有的資料庫資源。

藉由使用適用于 MongoDB 的 Azure Cosmos DB API，您可以享有 Cosmos DB 的優點，例如全域散發、自動分區化、高可用性、延遲保證、自動、待用加密、備份等，同時保留您在 MongoDB 應用程式中的投資。 您可以使用任何開放原始碼[MongoDB 用戶端驅動程式](https://docs.mongodb.org/ecosystem/drivers)，與適用于 MongoDB 的 Azure Cosmos DB API 進行通訊。 Azure Cosmos DB 適用于 MongoDB 的 API 可讓您藉由遵循[mongodb 有線通訊協定](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)，來使用現有的用戶端驅動程式。

## <a name="mongodb-protocol-support"></a>MongoDB 通訊協定支援

Azure Cosmos DB 適用于 MongoDB 的 API 與 MongoDB 伺服器版本3.2 和3.6 相容。 如需詳細資訊，請參閱[支援的功能和語法](mongodb-feature-support.md)。 

下列擴充功能命令可讓您透過資料庫要求來建立和修改 Azure Cosmos DB 特定的資源：

* [建立資料庫](#create-database)
* [更新資料庫](#update-database)
* [取得資料庫](#get-database)
* [建立集合](#create-collection)
* [更新集合](#update-collection)
* [取得集合](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>建立資料庫

建立資料庫延伸模組命令會建立新的 MongoDB 資料庫。 資料庫名稱可以從命令所設定的資料庫內容中使用 `use database` 。 下表描述命令中的參數：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
| `customAction`   |  `string`  |   自訂命令的名稱，必須是 "CreateDatabase"。      |
| `offerThroughput` | `int`  | 您在資料庫上設定的布建輸送量。 這是選擇性參數。 |
| `autoScaleSettings` | `Object` | 自動調整[模式](provision-throughput-autoscale.md)的必要。 此物件包含與自動調整容量模式相關聯的設定。 您可以設定 `maxThroughput` 值，其中描述集合將以動態方式增加的最高要求單位數量。 |

### <a name="output"></a>輸出

如果命令成功，它會傳回下列回應：

```javascript
{ "ok" : 1 }
```

請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

#### <a name="create-a-database"></a>建立資料庫

若要使用所有預設值來建立名為 `"test"` 的資料庫，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

此命令會建立資料庫，而不會有資料庫層級的輸送量。 這表示此資料庫內的集合必須指定您需要使用的輸送量。

#### <a name="create-a-database-with-throughput"></a>建立具有輸送量的資料庫

若要建立名為的資料庫， `"test"` 並指定[資料庫層級](set-throughput.md#set-throughput-on-a-database)的已布建輸送量 1000 ru，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

這會建立資料庫，並為其設定輸送量。 除非使用[特定的輸送量層級](set-throughput.md#set-throughput-on-a-database-and-a-container)來建立集合，否則此資料庫內的所有集合都會共用設定的輸送量。

#### <a name="create-a-database-with-autoscale-throughput"></a>建立具有自動調整輸送量的資料庫

若要建立名為的資料庫， `"test"` 並在[資料庫層級](set-throughput.md#set-throughput-on-a-database)指定 20000 RU/秒的自動調整最大輸送量，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a>更新資料庫

更新資料庫延伸模組命令會更新與指定資料庫相關聯的屬性。 下表描述命令中的參數：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
| `customAction`    |    `string`     |   自訂命令的名稱。 必須是 "Updatedatabase.vbs"。      |
|  `offerThroughput`   |  `int`       |     當資料庫使用[資料庫層級輸送量](set-throughput.md#set-throughput-on-a-database)時，您想要在資料庫上設定的新布建輸送量  |
| `autoScaleSettings` | `Object` | 自動調整[模式](provision-throughput-autoscale.md)的必要。 此物件包含與自動調整容量模式相關聯的設定。 您可以設定 `maxThroughput` 值，其中描述資料庫將以動態方式增加的最高要求單位數量。 |

此命令會使用會話內容中指定的資料庫。 這是您在命令中使用的資料庫 `use <database>` 。 目前無法使用此命令來變更資料庫名稱。

### <a name="output"></a>輸出

如果命令成功，它會傳回下列回應：

```javascript
{ "ok" : 1 }
```

請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>更新與資料庫相關聯的布建輸送量

若要將名稱為 1200 ru 的資料庫布建輸送量更新 `"test"` ，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>更新與資料庫相關聯的自動調整輸送量

若要將名稱為 20000 ru 的資料庫布建輸送量更新為 `"test"` ，或將其轉換為[自動調整輸送量層級](provision-throughput-autoscale.md)，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a>取得資料庫

取得資料庫延伸模組命令會傳回資料庫物件。 資料庫名稱會從執行命令的資料庫內容中使用。

```javascript
{
  customAction: "GetDatabase"
}
```

下表描述命令中的參數：


|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   自訂命令的名稱。 必須是 "GetDatabase"|
        
### <a name="output"></a>輸出

如果命令成功，回應會包含具有下欄欄位的檔：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `database`    |    `string`        |   資料庫的名稱。      |
|   `provisionedThroughput`  |    `int`      |    資料庫使用[手動資料庫層級輸送量](set-throughput.md#set-throughput-on-a-database)時，在資料庫上設定的布建輸送量     |
| `autoScaleSettings` | `Object` | 如果使用[自動調整模式](provision-throughput-autoscale.md)，此物件會包含與資料庫相關聯的容量參數。 此 `maxThroughput` 值描述資料庫將以動態方式增加的最高要求單位數量。 |

如果命令失敗，則會傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

#### <a name="get-the-database"></a>取得資料庫

若要取得名為之資料庫的資料庫物件 `"test"` ，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

如果資料庫沒有相關聯的輸送量，輸出將會是：

```javascript
{ "database" : "test", "ok" : 1 }
```

如果資料庫具有相關聯的[資料庫層級手動輸送量](set-throughput.md#set-throughput-on-a-database)，則輸出會顯示 `provisionedThroughput` 值：

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

如果資料庫具有相關聯的[資料庫層級自動調整輸送量](provision-throughput-autoscale.md)，輸出會顯示 `provisionedThroughput` ，其中描述資料庫的最小 ru/秒，以及 `autoScaleSettings` 包含的物件 `maxThroughput` ，其中描述資料庫的最大 ru/秒。

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a>建立集合

建立集合擴充功能命令會建立新的 MongoDB 集合。 資料庫名稱會從命令所設定的資料庫內容中使用 `use database` 。 CreateCollection 命令的格式如下所示：

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

下表描述命令中的參數：

| **欄位** | **型別** | **必要** | **描述** |
|---------|---------|---------|---------|
| `customAction` | `string` | 必要 | 自訂命令的名稱。 必須是 "CreateCollection"。|
| `collection` | `string` | 必要 | 集合的名稱。 不允許使用任何特殊字元或空格。|
| `offerThroughput` | `int` | 選擇性 | 要在資料庫上設定的布建輸送量。 如果未提供此參數，則會預設為最小值 400 RU/秒。 * 若要指定超過 10000 RU/秒的輸送量，則 `shardKey` 需要參數。|
| `shardKey` | `string` | 具有大量輸送量之集合的必要 | 分區化集合的分區索引鍵路徑。 如果您在中設定了超過 10000 RU/秒，則此為必要參數 `offerThroughput` 。  若已指定，則所有插入的檔都需要此金鑰和值。 |
| `autoScaleSettings` | `Object` | 自動調整[模式](provision-throughput-autoscale.md)的必要 | 此物件包含與自動調整容量模式相關聯的設定。 您可以設定 `maxThroughput` 值，其中描述集合將以動態方式增加的最高要求單位數量。 |

### <a name="output"></a>輸出

傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

#### <a name="create-a-collection-with-the-minimum-configuration"></a>建立具有最低設定的集合

若要建立具有名稱 `"testCollection"` 和預設值的新集合，請使用下列命令： 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

這會導致新的固定分區、具有 400RU/s 的集合，以及 `_id` 自動建立之欄位的索引。 這種類型的設定也適用于透過函式建立新的集合時 `insert()` 。 例如： 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>建立分區集合

若要建立分區集合，其名稱 `"testCollection"` 和布建的輸送量為 1000 ru，請使用下列命令： 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

您可以建立最多 10000 RU/秒的集合， `offerThroughput` 而不需要指定分區索引鍵。 針對具有較大輸送量的集合，請參閱下一節。

#### <a name="create-a-sharded-collection"></a>建立分區化集合

若要建立分區化集合，其名稱 `"testCollection"` 和布建的輸送量為 11000 ru，而 `shardkey` 屬性為 "a. b"，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

此命令現在需要 `shardKey` 參數，因為中指定了超過 10000 RU/秒 `offerThroughput` 。

#### <a name="create-an-unsharded-autoscale-collection"></a>建立分區自動調整規模集合

若要建立名為的分區集合 `'testCollection'` ，其使用的[自動調整輸送量容量](provision-throughput-autoscale.md)設定為 4000 RU/秒，請使用下列命令：

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

針對此 `autoScaleSettings.maxThroughput` 值，您可以指定從 4000 ru/秒到 10000 RU/s 的範圍，而不需要分區金鑰。 如需更高的自動調整輸送量，您必須指定 `shardKey` 參數。

#### <a name="create-a-sharded-autoscale-collection"></a>建立分區化自動調整集合

若要使用名為的分區金鑰建立名為的分區化集合 `'testCollection'` `'a.b'` ，並將[自動調整輸送量容量](provision-throughput-autoscale.md)設定為 20000 RU/秒，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a>更新集合

更新集合延伸模組命令會更新與指定的集合相關聯的屬性。

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

下表描述命令中的參數：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   自訂命令的名稱。 必須是 "UpdateCollection"。      |
|  `collection`   |   `string`      |   集合的名稱。       |
| `offerThroughput` | `int` |   要在集合上設定的布建輸送量。|
| `autoScaleSettings` | `Object` | 自動調整[模式](provision-throughput-autoscale.md)的必要。 此物件包含與自動調整容量模式相關聯的設定。 `maxThroughput`值描述集合將以動態方式增加的最大要求單位數量。 |

## <a name="output"></a>輸出

傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>更新與集合相關聯的已布建輸送量

若要將名稱為的集合的已布建輸送量更新 `"testCollection"` 為 1200 ru，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>取得集合

取得集合自訂命令會傳回集合物件。

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

下表描述命令中的參數：


|**欄位**|**型別** |**說明** |
|---------|---------|---------|
| `customAction`    |   `string`      |   自訂命令的名稱。 必須是 "GetCollection"。      |
| `collection`    |    `string`     |    集合的名稱。     |

### <a name="output"></a>輸出

如果命令成功，回應會包含具有下欄欄位的檔


|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `database`    |    `string`     |   資料庫的名稱。      |
| `collection`    |    `string`     |    集合的名稱。     |
|  `shardKeyDefinition`   |   `document`      |  當做分區金鑰使用的索引規格檔。 這是選擇性的回應參數。       |
|  `provisionedThroughput`   |   `int`      |    要在集合上設定的布建輸送量。 這是選擇性的回應參數。     |
| `autoScaleSettings` | `Object` | 如果使用[自動調整模式](provision-throughput-autoscale.md)，此物件會包含與資料庫相關聯的容量參數。 `maxThroughput`值描述集合將以動態方式增加的最大要求單位數量。 |

如果命令失敗，則會傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

#### <a name="get-the-collection"></a>取得集合

若要取得名為之集合的集合物件 `"testCollection"` ，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

如果集合具有相關聯的輸送量容量，它會包含 `provisionedThroughput` 值，而輸出會是：

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

如果集合具有相關聯的自動調整輸送量，它會包含 `autoScaleSettings` 具有參數的物件 `maxThroughput` ，以定義集合會以動態方式增加的最大輸送量。 此外，它也會包含 `provisionedThroughput` 值，這會定義當集合中沒有任何要求時，此集合會減少到的最小輸送量： 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

如果集合是在自動調整模式或手動共用[資料庫層級的輸送量](set-throughput.md#set-throughput-on-a-database)，則輸出會是：

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>自訂命令的預設輸出

如果未指定，自訂回應會包含具有下欄欄位的檔：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `code`    |   `int`      |   只有在命令失敗時傳回（亦即 ok = = 0）。 包含 MongoDB 錯誤碼。 這是選擇性的回應參數。      |
|  `errMsg`   |  `string`      |    只有在命令失敗時傳回（亦即 ok = = 0）。 包含使用者易記的錯誤訊息。 這是選擇性的回應參數。      |

例如：

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>後續步驟

接下來，您可以繼續瞭解下列 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
