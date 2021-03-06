---
title: Azure Cosmos DB 中的要求單位和輸送量
description: 了解如何在 Azure Cosmos DB 中指定和預估要求單位需求
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 3378b442c80758c4a5dca02b33f92ba3823f33ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85113646"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的要求單位

使用 Azure Cosmos DB，您需要按小時支付所佈建的輸送量及所取用的儲存體。 您必須佈建輸送量，確保有足夠的系統資源隨時都可供您的 Azure Cosmos 資料庫使用。 您需要足夠的資源來滿足或超過[Azure Cosmos DB sla](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)。

Azure Cosmos DB 支援許多 API，例如 SQL、MongoDB、Cassandra、Gremlin 和資料表。 每個 API 都有它自己的一組資料庫作業。 這些作業的範圍可從簡單的點讀取和寫入到複雜查詢。 每個資料庫作業都會根據作業的複雜度來取用系統資源。 

所有資料庫作業的成本會由 Azure Cosmos DB 正規化，並以*要求單位*（簡稱 ru）來表示。 您可以將每秒 RU 想像為輸送量的貨幣。 每秒 RU 是以速率為基礎的貨幣。 它會擷取執行 Azure Cosmos DB 所支援之資料庫作業所需的系統資源 (例如 CPU、IOPS 和記憶體)。 

讀取 1 KB 項目的成本是 1 個要求單位 (即 1 RU)。 至少需要 10 RU/秒，才能儲存每 1 GB 的資料。 所有其他資料庫作業同樣會使用 RU 來指派成本。 無論您使用哪種 API 與 Azure Cosmos 容器互動，成本始終由 RU 測量。 無論資料庫作業是寫入、讀取或查詢，成本一律以 RU 為單位進行測量。

下圖顯示 RU 的高階概念：

:::image type="content" source="./media/request-units/request-units.png" alt-text="資料庫作業會取用要求單位" border="false":::

為了管理和規劃容量，Azure Cosmos DB 會確保指定資料集上指定資料庫作業的 RU 數目具有決定性。 您可以檢查回應標頭來追蹤任何資料庫作業所取用的 RU 數目。 當您瞭解[影響 RU 費用的因素](request-units.md#request-unit-considerations)，以及應用程式的輸送量需求時，您可以將應用程式的成本有效率地執行。

您還是會以每秒為單位，以遞增 100 個 RU/秒的方式來佈建應用程式的 RU 數目。 若要調整應用程式的佈建輸送量，您可以隨時增加或減少 RU 數目。 您可以用遞增或遞減 100 RU 的方式來調整數目。 您可以程式設計方式或使用 Azure 入口網站進行變更。 您要按小時付費。

您可以在兩個不同的資料粒度上佈建輸送量： 

* **容器**：如需詳細資訊，請參閱[在 Azure Cosmos 容器上](how-to-provision-container-throughput.md)布建輸送量。
* **資料庫**：如需詳細資訊，請參閱[在 Azure Cosmos 資料庫上](how-to-provision-database-throughput.md)布建輸送量。

## <a name="request-unit-considerations"></a>要求單位的考量

雖然您預估每秒佈建的的 RU 數目，但請考慮下列因素：

* **專案大小**：當專案的大小增加時，取用來讀取或寫入專案的 ru 數目也會增加。

* **專案索引**：根據預設，每個專案都會自動編制索引。 如果您選擇不要在容器中編製某些項目的索引，即會取用較少的 RU。

* **專案屬性計數**：假設預設的索引是在所有屬性上，則取用來寫入專案的 ru 數目會隨著專案屬性計數增加而增加。

* 已**編制索引的屬性**：每個容器上的索引原則會決定預設要編制索引的屬性。 若要減少寫入作業的要求單位取用量，請限制已編製索引的屬性數目。

* **資料一致性**：相較于其他寬鬆一致性層級，強式和限定過期一致性層級在執行讀取作業時，會耗用大約兩倍以上的 ru。

* **查詢模式**：查詢的複雜度會影響針對作業所耗用的 ru 數目。 影響查詢作業成本的因素包括： 
    
    - 查詢結果數目
    - 述詞數目
    - 述詞性質
    - 使用者定義函式數目
    - 來源資料的大小
    - 結果集的大小
    - 投影

  Azure Cosmos DB 保證相同資料上相同查詢的成本，一律會與重複執行上的 RU 數目相同。

* **腳本使用**方式：如同查詢，預存程式和觸發程式會根據所執行作業的複雜度來取用 ru。 當您開發應用程式時，請檢查[要求費用標頭](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query)，進一步瞭解每個作業會耗用多少 RU 容量。

## <a name="next-steps"></a>後續步驟

* 深入了解如何[在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)。
* 深入了解[邏輯分割區](partition-data.md)。
* 深入了解如何[全域調整佈建的輸送量](scaling-throughput.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
* 瞭解如何[尋找作業的要求單位費用](find-request-unit-charge.md)。
* 瞭解如何[在 Azure Cosmos DB 中將布建的輸送量成本優化](optimize-cost-throughput.md)。
* 瞭解如何將[Azure Cosmos DB 中的讀取和寫入成本優化](optimize-cost-reads-writes.md)。
* 瞭解如何[在 Azure Cosmos DB 中將查詢成本優化](optimize-cost-queries.md)。
* 瞭解如何[使用計量來監視輸送量](use-metrics.md)。
