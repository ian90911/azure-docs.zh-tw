---
title: 使用 Blitzz 將資料從 Oracle 遷移至 Azure Cosmos DB Cassandra API
description: 瞭解如何使用 Blitzz，將資料從 Oracle 資料庫移轉至 Azure Cosmos DB Cassandra API。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 882ba7f0b8f896c51e340fe921e53b27dd07ff8a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262458"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>使用 Blitzz 將 Oracle 的資料移轉至 Azure Cosmos DB Cassandra API 帳戶

Azure Cosmos DB 中的 Cassandra API 對於在 Oracle 上執行的企業工作負載而言，是很棒的選擇，因為有許多原因，例如：

* **更好的擴充性和可用性：** 它可以消除單一失敗點、更佳的擴充性，以及應用程式的可用性。

* **大幅節省成本：** 您可以使用 Azure Cosmos DB 節省成本，包括 VM 的成本、頻寬和任何適用的 Oracle 授權。 此外，您不需要管理資料中心、伺服器、SSD 儲存體、網路和電力成本。

* **管理和監視不會產生額外負荷：** 做為完全受控的雲端服務，Azure Cosmos DB 移除管理及監視許多設定的額外負荷。

有各種方式可將資料庫工作負載從一個平臺遷移至另一個平臺。 [Blitzz](https://www.blitzz.io)是一種工具，可提供安全且可靠的方式，從各種不同的資料庫執行零停機遷移到 Azure Cosmos DB。 本文說明使用 Blitzz 將資料從 Oracle 資料庫移轉至 Azure Cosmos DB Cassandra API 所需的步驟。

## <a name="benefits-using-blitzz-for-migration"></a>使用 Blitzz 進行遷移的優點

Blitzz 的遷移解決方案會遵循逐步執行方法來遷移複雜的作業工作負載。 以下是 Blitzz 的零停機時間遷移計畫的一些重要層面：

* 它提供從 Oracle 資料庫到 Azure Cosmos DB 的商務邏輯（資料表、索引、視圖）的自動遷移。 您不需要手動建立架構。

* Blitzz 提供高容量和平行的資料庫複寫。 它可讓來源和目標平臺在遷移期間，使用稱為「變更-資料-捕獲（CDC）」的技術進行同步處理。 藉由使用 CDC，Blitzz 會持續從源資料庫（Oracle）提取變更的資料流程，並將其套用至目的地資料庫（Azure Cosmos DB）。

* 它具備容錯能力，而且即使在系統中發生硬體或軟體故障時，也能保證剛好一次傳遞資料。

* 它會使用各種安全性方法（例如 TLS/SSL、加密）來保護傳輸期間的資料。

* 它提供的服務可將 PL/SQL 中撰寫的複雜商務邏輯轉換成 Azure Cosmos DB 中的對等商務邏輯。

## <a name="steps-to-migrate-data"></a>遷移資料的步驟

本節說明設定 Blitzz，並將資料從 Oracle 資料庫移轉至 Azure Cosmos DB 所需的步驟。

1. 從您計畫安裝 Blitzz replicant 的電腦上，新增安全性憑證。 Blitzz replicant 需要此憑證，才能建立具有指定 Azure Cosmos DB 帳戶的 TLS 連線。 您可以使用下列步驟來新增憑證：

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou 可以在[Blitzz 網站](https://www.blitzz.io)上要求示範，以取得 Blitzz 安裝和二進位檔案。 或者，您也可以傳送[電子郵件](mailto:success@blitzz.io)給小組。

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz replicant tool 下載":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz replicant 檔":::

1. 從 CLI 終端機，設定源資料庫設定。 使用命令開啟設定檔， **`vi conf/conn/oracle.yml`** 並新增以逗號分隔的 oracle 節點 IP 位址清單、埠號碼、使用者名稱、密碼，以及任何其他必要的詳細資料。 下列程式碼顯示範例設定檔案：

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="開啟 Oracle 連接編輯器":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Oracle 連線設定":::

   填寫設定詳細資料之後，請儲存並關閉檔案。

1. （選擇性）您可以設定源資料庫篩選檔案。 篩選檔案會指定要遷移的架構或資料表。 使用命令開啟設定檔 **`vi filter/oracle_filter.yml`** ，然後輸入下列設定詳細資料：

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   填寫資料庫篩選詳細資料之後，請儲存並關閉檔案。

1. 接下來，您將設定目的地資料庫的設定。 定義設定之前，請先[建立 Azure Cosmos DB Cassandra API 帳戶](create-cassandra-dotnet.md#create-a-database-account)。 從您的資料中[選擇正確的分割](partitioning-overview.md#choose-partitionkey)區索引鍵，然後建立 Keyspace，以及用來儲存已遷移資料的資料表。

1. 在遷移資料之前，請將容器輸送量增加至您的應用程式快速遷移所需的數量。 例如，您可以將輸送量增加到 100000 ru。 在開始進行遷移之前調整輸送量，將可協助您在更短的時間內遷移資料。 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="在整個期間調整 Azure Cosmos 容器":::

   完成遷移之後，您必須減少輸送量。 根據每項作業所需的儲存資料量和 ru，您可以估計資料移轉後所需的輸送量。 若要深入瞭解如何預估所需的 RU，請參閱[在容器和資料庫上](set-throughput.md)布建輸送量和[使用 Azure Cosmos DB 容量規劃工具文章來預估 RU/秒](estimate-ru-with-capacity-planner.md)。

1. 從 [**連接字串**] 窗格取得 Azure Cosmos 帳戶的 [**連絡人點]、[埠]、**[使用者名稱] 和 [**主要密碼**]。 您會在設定檔中使用這些值。

1. 從 CLI 終端機，設定目的地資料庫設定。 使用命令開啟設定檔， **`vi conf/conn/cosmosdb.yml`** 並新增以逗號分隔的主機 URI、埠號碼、使用者名稱、密碼及其他必要參數的清單。 以下是設定檔中的內容範例：

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. 接下來，使用 Blitzz 來遷移資料。 您可以在**完整**或**快照**模式中執行 Blizz replicant：

   * **完整模式**–在此模式中，replicant 會在遷移之後繼續執行，而且它會接聽來源 Oracle 系統上的任何變更。 如果偵測到任何變更，則會即時複寫到目標 Azure Cosmos 帳戶。

   * **快照模式**–在此模式中，您可以執行架構遷移和一次性資料複寫。 使用此選項時，不支援即時複寫。


   藉由使用上述兩種模式，可以在不停機的情況下執行遷移。

1. 若要遷移資料，請從 Blitzz replicant CLI 終端機執行下列命令：

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Replicant UI 會顯示覆寫進度。 完成架構遷移和快照集作業之後，進度會顯示100%。 完成遷移之後，您可以在目標 Azure Cosmos 資料庫上驗證資料。

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Oracle 資料移轉輸出":::

1. 由於您已使用完整模式來進行遷移，因此您可以執行像是在來源 Oracle 資料庫上插入、更新或刪除資料等作業。 之後，您可以驗證它們會在目標 Azure Cosmos 資料庫上即時複寫。 在遷移之後，請務必減少針對您的 Azure Cosmos 容器所設定的輸送量。

1. 您可以隨時停止 replicant，並使用 **--resume**參數加以重新開機。 複寫會從已停止的時間點繼續，而不會影響資料的一致性。 下列命令會顯示如何使用 resume 參數。

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

若要深入瞭解如何將資料移轉至目的地、即時移轉，請參閱[Blitzz replicant 示範](https://www.youtube.com/watch?v=y5ZeRK5A-MI)。

## <a name="next-steps"></a>後續步驟

* [在容器和資料庫中佈建輸送量](set-throughput.md) 
* [資料分割索引鍵的最佳做法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB 容量規劃文章來預估 RU/秒](estimate-ru-with-capacity-planner.md)
