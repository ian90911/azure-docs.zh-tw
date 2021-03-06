---
title: 雙層類神經網路：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用兩個類別的類神經網路模組來建立類神經網路模型，以用來預測只有兩個值的目標。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 4270e4c71cca28d43b1ecbaca1ec0f262251252b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82137598"
---
# <a name="two-class-neural-network-module"></a>雙類別類神經網路模組

本文說明 Azure Machine Learning 設計工具 (預覽) 中的模組。

使用此模組建立類神經網路模型，以用來預測只有兩個值的目標。

使用類神經網路的分類是一種受監督的學習方法，因此需要包含標籤資料行的已*加上標記的資料集*。 例如，您可以使用此類神經網路模型來預測二進位結果，例如患者是否有特定疾病，或電腦是否可能在指定的時間範圍內失敗。  

定義模型之後，請提供已加上標籤的資料集和模型做為[定型模型](./train-model.md)的輸入，以將它定型。 定型的模型接著可用來預測新輸入的值。

### <a name="more-about-neural-networks"></a>深入瞭解類神經網路

類神經網路是一組互連的層。 輸入是第一層，並由加權邊緣和節點所組成的非循環圖表形連接到輸出層。

在輸入和輸出層之間，您可以插入多個隱藏層。 大部分的預測工作可以與一個或幾個隱藏層輕鬆完成。 不過，最近的研究顯示，具有許多層級的深度類神經網路（DNN）可在複雜的工作（例如影像或語音辨識）中生效。 後續層用來建立更多的語義深度層級模型。

輸入和輸出之間的關聯性是從訓練類神經網路的輸入資料開始學習。 圖形的方向會透過隱藏層和輸出層來繼續輸入。 圖層中的所有節點都是由加權邊緣連接到下一層中的節點。

若要計算特定輸入的網路輸出，則會在隱藏層和輸出層中的每個節點上計算值。 此值是藉由計算上一層中節點值的加權總和來設定。 接著，會將啟用函式套用至該加權總和。
  
## <a name="how-to-configure"></a>如何設定

1.  將**兩個類別的類神經網路**模組新增至您的管線。 您可以在 [**分類**] 分類的 [ **Machine Learning**]、[**初始化**] 下找到此模組。  
  
2.  藉由設定 [**建立定型模式]** 選項，指定您要如何訓練模型。  
  
    -   **單一參數**：如果您已經知道要如何設定模型，請選擇此選項。

    -   **參數範圍**：如果您不確定最佳參數，您可以使用[微調模型超參數](tune-model-hyperparameters.md)模組來尋找最佳參數。 您會提供某個範圍的值，而講師會逐一查看設定的多個組合，以判斷產生最佳結果的值組合。  

3.  針對 [**隱藏層規格**]，選取要建立的網路架構類型。  
  
    -   **完全連線的案例**：使用預設的類神經網路架構（定義于兩個類別的神經網路），如下所示：
  
        -   有一個隱藏層。
  
        -   輸出層完全連接到隱藏層，而隱藏層完全連接到輸入層。
  
        -   輸入層中的節點數目等於定型資料中的特徵數目。
  
        -   隱藏層中的節點數目是由使用者設定。 預設值是 100。
  
        -   節點數目等於類別數目。 針對兩個類別的類神經網路，這表示所有輸入都必須對應到輸出層中的兩個節點之一。

5.  如需**學習速率**，請在更正之前定義每個反復專案所採取的步驟大小。 較大的學習速率值可能會導致模型更快收斂，但可能衝過局部最小值。

6.  針對 [學習反復專案**數**]，指定演算法處理定型案例的最大次數。

7.  針對**初始學習權數直徑**，請在學習程式開始時指定節點權數。

8.  針對**動量**，指定從先前的反復專案中學習到節點時要套用的權數  

10. 選取 [**隨機播放範例**] 選項，在反復專案之間隨機播放案例。 如果您取消選取此選項，則每次執行管線時，會以完全相同的順序來處理案例。
  
11. 針對 [**亂數字種子**]，輸入要做為種子的值。
  
     當您想要確保跨相同管線的執行可重複性時，指定種子值會很有用。  否則會使用系統時鐘值做為種子，這會在每次執行管線時，產生稍微不同的結果。
  
13. 將加上標籤的資料集新增至管線，並將模型定型：

    + 如果您將 [**建立定型模式**] 設定為 [**單一參數**]，請連接已標記的資料集和 [[訓練模型](train-model.md)] 模組。  
  
    + 如果您將 [**建立定型模式**] 設定為 [**參數範圍**]，請連接已加上標籤的資料集，並使用[微調模型超參數](tune-model-hyperparameters.md)來定型模型。  
  
    > [!NOTE]
    > 
    > 如果您將參數範圍傳遞至[定型模型](train-model.md)，它只會使用單一參數清單中的預設值。  
    > 
    > 如果您將一組參數值傳遞至[微調模型超參數](tune-model-hyperparameters.md)模組，當它預期每個參數的設定範圍時，就會忽略這些值，並使用學習模組的預設值。  
    > 
    > 如果您選取 [**參數範圍**] 選項，並輸入任何參數的單一值，則會在整個清除中使用您指定的單一值，即使其他參數在某個範圍的值之間變更也是如此。  
  
14. 提交管線。

## <a name="results"></a>結果

定型完成後：

+ 若要儲存定型模型的快照集，請選取 [**訓練模型**] 模組右面板中的 [**輸出**] 索引標籤。 選取 [**註冊資料集**] 圖示，將模型儲存為可重複使用的模組。

+ 若要使用模型進行評分，請將 [**評分模型**] 模組新增至管線。


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
