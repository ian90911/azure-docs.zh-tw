---
title: Azure Cosmos DB 查詢語言中的 StartsWith
description: 深入了解 Azure Cosmos DB 中的 SQL 系統函數 STARTSWITH。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c64efb92de00291e6381e30af24e76df2b38aee0
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847109"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)

 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。  
  
## <a name="syntax"></a>語法
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   是字串運算式。
  
*str_expr2*  
   是要與 *str_expr1* 的開頭進行比較的字串運算式。

*bool_expr* 忽略大小寫的選擇性值。 設定為 True 時，STARTSWITH 將執行不區分大小寫的搜尋。 若未指定，則此值為 False。

## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
下列範例會檢查字串 "abc" 是否以 "b" 和 "A" 開頭。  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 以下為結果集。  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>備註

此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
