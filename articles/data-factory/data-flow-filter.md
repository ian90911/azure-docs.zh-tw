---
title: 對應資料流程中的篩選轉換
description: 使用 Azure Data Factory 對應資料流程中的篩選準則轉換來篩選出資料列
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84112801"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>對應資料流程中的篩選轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

篩選轉換可讓您根據條件來進行資料列篩選。 輸出資料流程包含符合篩選準則的所有資料列。 篩選器轉換類似于 SQL 中的 WHERE 子句。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>組態

使用資料流程運算式產生器，為篩選準則輸入運算式。 若要開啟 [運算式產生器]，請按一下藍色方塊。 篩選準則的類型必須是布林值。 如需如何建立運算式的詳細資訊，請參閱[expression builder](concepts-data-flow-expression-builder.md)檔。

![篩選轉換](media/data-flow/filter1.png "篩選轉換")

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>範例

下列範例是一個名為的篩選器轉換 `FilterBefore1960` ，它會接受傳入的資料流程 `CleanData` 。 篩選準則是運算式 `year <= 1960` 。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![篩選轉換](media/data-flow/filter1.png "篩選轉換")

此轉換的資料流指令碼位於下列程式碼片段中：

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>後續步驟

使用[select 轉換](data-flow-select.md)來篩選出資料行
