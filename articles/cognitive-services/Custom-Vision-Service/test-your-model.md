---
title: 測試和重塑模型 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 本文將說明如何測試影像，然後使用它來重新定型您在自訂視覺服務中的模型。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 5a3aacd1d07ff068fe50312b2c1d47ac080e5c2c
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391718"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>使用自訂視覺服務測試和重塑模型

當您為模型定型之後，便可以使用本機儲存的影像或線上影像，快速地進行測試。 測試會使用最近定型的反覆項目。

## <a name="test-your-model"></a>測試您的模型

1. 從[自訂視覺網頁](https://customvision.ai) \(英文\) 選取您的專案。 在頂端功能表列右方選取 [快速測試]****。 這個動作會開啟標示為 [快速測試]**** 的視窗。

    ![[快速測試] 按鈕會顯示於視窗右上角。](./media/test-your-model/quick-test-button.png)

2. 在 [快速測試]**** 視窗中，按一下 [送出影像]**** 欄位，然後輸入您要用來測試的影像 URL。 如果您想要改用本機儲存的影像，按一下 [瀏覽本機檔案]**** 按鈕，然後選取本機影像檔。

    ![[送出影像] 頁面的影像](./media/test-your-model/submit-image.png)

您選取的影像會出現在頁面中間。 然後，結果會以含有兩欄 (分別標示為**標記**和**信賴度**) 的表格形式出現在影像下方。 當您檢視結果之後，即可關閉 [快速測試]**** 視窗。

您現在可以將這個測試影像新增到模型，然後重塑模型。

## <a name="use-the-predicted-image-for-training"></a>使用預測的映射進行定型

若要使用先前送出的影像來定型，請使用下列步驟：

1. 若要檢視送出到分類器的影像，開啟[自訂視覺網頁](https://customvision.ai) \(英文\)，然後選取 [預測]____ 索引標籤。

    ![[預測] 索引標籤的影像](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > 預設檢視會顯示來自目前反覆項目的影像。 您可以使用 [反覆項目]____ 下拉式清單欄位，來檢視在先前反覆項目期間送出的影像。

2. 將滑鼠停留在影像上方，以查看由分類器所預測的標記。

    > [!TIP]
    > 影像會設定順位，如此一來，可為分類器帶來最大獲益的影像就會位於最上方。 若要選取不同的排序，使用 [排序]____ 區段。

    若要將影像新增到定型資料，請選取影像、選取標記，然後選取 [儲存並關閉]____。 從 [預測]____ 移除影像並新增到定型影像。 您可以選取 [定型影像]____ 索引標籤加以檢視。

    ![標記頁面的影像](./media/test-your-model/tag-image.png)

3. 使用 [定型]____ 按鈕來重塑分類器。

## <a name="next-steps"></a>接下來的步驟

[改善您的分類器](getting-started-improving-your-classifier.md)
