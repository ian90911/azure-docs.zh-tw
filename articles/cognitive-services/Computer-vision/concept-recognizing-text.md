---
title: " (OCR) 的光學字元辨識-電腦視覺"
titleSuffix: Azure Cognitive Services
description: 與光學字元辨識相關的概念 (OCR) 從影像和檔，以及使用電腦視覺 API 的列印和手寫文字。
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 6bc118145bec30085c2d9fbf726c40a20b312430
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207062"
---
# <a name="optical-character-recognition-ocr"></a>光學字元辨識 (OCR)

Microsoft 的電腦視覺 API 包含光學字元辨識 (OCR) 功能，可從影像和 PDF 檔中解壓縮列印或手寫文字。 OCR Api 會將類比檔中的文字從 (影像、掃描的檔) 和數位化的檔中解壓縮。 您可以從真實影像中取出文字，例如授權盤子的相片或具有序號的容器，以及檔-發票、帳單、財務報告、文章等等。 新的讀取 OCR API 可作為雲端或內部部署 (容器) 的受控服務之一部分。 此外，它也支援虛擬網路和私人端點，以符合您的企業級合規性和隱私權需求。

## <a name="read-api"></a>讀取 API 

電腦視覺的[讀取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)是 Microsoft 的最新 OCR 技術，可將印刷文字 (七種語言) 、手寫文字僅 (英文) 、數位和貨幣符號，從影像和多頁 PDF 檔中取得。 它已優化，可從具有混合語言的內部文字大量影像和多頁 PDF 檔中解壓縮文字。 它支援在相同的影像或檔中偵測列印和手寫文字 (英文) 。 請參閱[OCR 支援語言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)的完整清單頁面。

### <a name="how-ocr-works"></a>OCR 的運作方式

[讀取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)支援高達2000頁的大量文字檔，因此會以非同步方式執行。 第一個步驟是呼叫讀取作業。 讀取作業會採用影像或 PDF 檔做為輸入，並傳回作業識別碼。 

第二個步驟是呼叫[取得結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750)作業。 這項作業會採用讀取作業所建立的作業識別碼。 然後，它會從您的影像或檔以 JSON 格式傳回已解壓縮的文字內容。 JSON 回應會維護已辨識單字的原始行分組。 其中包含已解壓縮的文字行及其周框方塊座標。 每一行都包含所有已解壓縮的文字及其座標和信賴分數。

如有必要，請參閱以水準影像軸的角度傳迴旋轉位移，以更正已辨識頁面的旋轉，如下圖所示。

![OCR 如何使用已解壓縮的文字將影像和檔轉換成結構化輸出](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>OCR 輸出範例

成功的回應會以 JSON 格式傳回，如下列範例所示：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

請遵循[解壓縮印刷和手寫文字](./QuickStarts/CSharp-hand-text.md)快速入門，使用 c # 和 REST API 來執行 OCR。

### <a name="input-requirements"></a>輸入需求

讀取 API 會接受下列輸入：
* 支援的檔案格式： JPEG、PNG、BMP、PDF 和 TIFF
* 若是 PDF 和 TIFF，則會處理最多2000個頁面。 對於免費層訂閱者，只會處理前兩個頁面。
* 檔案大小必須小於 50 MB，以及至少 50 x 50 圖元和最多 10000 x 10000 圖元的維度。
* PDF 尺寸最多必須是 17 x 17 英寸，對應于合法或 A3 紙張大小和較小。

### <a name="text-from-images"></a>影像中的文字

下列讀取 API 輸出顯示影像中的已解壓縮文字行和單字，以及不同角度、色彩和字型的文字

![要旋轉的影像及其要讀取和描繪的文字](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>檔中的文字

除了影像，讀取 API 也會採用 PDF 檔做為輸入。

![要旋轉的影像及其要讀取和描繪的文字](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>英文的手寫文字

目前，讀取作業支援英文以獨佔方式解壓縮手寫文字。

![要旋轉的影像及其要讀取和描繪的文字](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>支援的語言中的印刷文字

讀取 API 支援以英文、西班牙文、德文、法文、義大利文、葡萄牙文和荷蘭文語言來解壓縮印刷文字。 如果您的案例需要支援更多語言，請參閱本檔中的 OCR API 總覽。 請參閱所有[OCR 支援語言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)的清單

![要旋轉的影像及其要讀取和描繪的文字](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>混合語言支援

讀取 API 支援在其中使用多種語言的影像和檔，通常稱為混合語言檔。 其方式是將檔中的每個文字行分類成偵測到的語言，再將文字內容解壓縮。

![要旋轉的影像及其要讀取和描繪的文字](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>資料隱私權和安全性

就像所有認知服務一樣，使用「讀取」服務的開發人員應該要瞭解 Microsoft 對於客戶資料的原則。 若要深入瞭解，請參閱[Microsoft 信任中心](https://www.microsoft.com/en-us/trust-center/product-overview)上的認知服務頁面。

### <a name="deploy-on-premises"></a>部署內部部署

[讀取] 也可作為 Docker 容器 (預覽) ，讓您在自己的環境中部署新的 OCR 功能。 容器非常適合用於特定的安全性和資料控管需求。 請參閱[如何安裝和執行讀取容器。](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)會使用較舊的辨識模型、僅支援影像，並以同步方式執行，並立即傳回偵測到的文字。 請參閱[OCR 支援的語言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)，而不是讀取 API。

## <a name="next-steps"></a>後續步驟

- 瞭解[讀取 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)。
- 遵循[解壓縮文字](./QuickStarts/CSharp-hand-text.md)快速入門，使用 c #、JAVA、JavaScript 或 Python 搭配 REST API 來執行 OCR。
