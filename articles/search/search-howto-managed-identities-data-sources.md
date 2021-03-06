---
title: 使用受控識別 (預覽) 來設定與資料來源的連線
titleSuffix: Azure Cognitive Search
description: 了解如何使用受控識別 (預覽) 設定與資料來源的索引子連線
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 48b94b8cd047f62ea13bf4e062254088ea11840e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663596"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity-preview"></a>使用受控識別 (預覽) 設定與資料來源的索引子連線

> [!IMPORTANT] 
> 使用受控識別來設定資料來源連線的支援目前處於閘道公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 您可填寫[此表單](https://aka.ms/azure-cognitive-search/mi-preview-request)來要求預覽的存取權。

Azure 認知搜尋中的[索引子](search-indexer-overview.md)是一種編目程式，其可供從資料來源將資料提取到 Azure 認知搜尋。 索引子會從所建立的資料來源物件取得資料來源連線。 資料來源物件通常包含目標資料來源的認證。 例如，如果想要編製 blob 儲存體容器資料的索引，則資料來源物件可包含 Azure 儲存體帳戶金鑰。

在許多情況下，直接在資料來源物件中提供認證並不是問題，但有一些挑戰可能會出現：
* 如何在建立資料來源物件的程式碼中保護認證安全？
* 如果帳戶金鑰或密碼遭到入侵且需要加以變更，因此現在我需要使用新的帳戶金鑰或密碼來更新資料來源物件，讓索引子可再次連線到資料來源。

您可使用受控識別來設定連線以解決這些問題。

## <a name="using-managed-identities"></a>使用受控識別

[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)功能會在 Azure Active Directory (Azure AD) 中為 Azure 服務提供自動受控識別。 您可使用 Azure 認知搜尋中的這項功能，以不包含任何認證的連接字串來建立資料來源物件。 將會改為透過角色型存取控制 (RBAC) 來授與搜尋服務對資料來源的存取權。

使用受控識別設定資料來源時，您可變更資料來源認證，而索引子仍然可連線至資料來源。 您也可以在程式碼中建立資料來源物件，而不需要包含帳戶金鑰或使用金鑰保存庫來擷取帳戶金鑰。

## <a name="limitations"></a>限制

下列資料來源支援使用受控識別設定索引子連線。 

* [Azure Blob 儲存體、Azure Data Lake Storage Gen2 (預覽)、Azure 資料表儲存體](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

下列功能目前不支援使用受控識別來設定連線：
* 知識存放區
* 自訂技能
 
## <a name="next-steps"></a>後續步驟

深入了解如何使用受控識別設定索引子連線：

* [Azure Blob 儲存體、Azure Data Lake Storage Gen2 (預覽)、Azure 資料表儲存體](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)