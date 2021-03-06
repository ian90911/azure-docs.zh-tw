---
title: 影片播放-Azure
description: 預留位置
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 1fc65a00b2aa1e82c5585583ee9e0ccb97e5168f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260171"
---
# <a name="video-playback"></a>影片播放 

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項 

* [IoT Edge 上的 Live Video Analytics 概觀](overview.md)
* [IoT Edge 上的 Live Video Analytics 術語](terminology.md)
* [媒體圖表概念](media-graph-concept.md)

## <a name="overview"></a>總覽  

您可以使用[媒體圖形](media-graph-concept.md)，將影片錄製到 Azure 媒體服務[資產](terminology.md#asset)中。 在本檔中，您可以瞭解使用 Azure 媒體服務的現有串流功能來播放資產所需採取的步驟。

## <a name="streaming-endpoint"></a>串流端點 

您可以使用 Azure 媒體服務，使用業界標準的 HTTP 型媒體串流通訊協定（例如 HTTP 即時串流（HLS）和 MPEG-破折號）將資產[串流](terminology.md#streaming)至影片播放機。 從錄製的內容轉換成串流格式的媒體會由[串流端點](../latest/streaming-endpoint-concept.md)來處理，這是您需要在 Azure 媒體服務帳戶中布建的資源。

## <a name="streaming-policy"></a>串流原則 

Azure 媒體服務提供您不同的方法來保護您的影片串流，如[使用媒體服務動態加密來保護您的內容](../latest/content-protection-overview.md)一文所述。 概括而言，內容保護的選項如下：

* **清晰的串流**–在串流處理期間不會套用加密。
* **使用進階加密標準（AES-128）** –並執行方法來傳遞金鑰，只將影片解密到已驗證的檢視器。
* **使用數位 Rights Management （DRM）系統**–控制對強制執行這些原則的裝置使用、修改及傳遞影片。

若要達成內容保護，您可以在媒體服務帳戶中定義和建立[串流原則](../latest/streaming-policy-concept.md)，並用它來串流處理所有資產（假設所有資料流程都具有相同的安全性需求）。 您也可以使用任何預先定義的原則（例如 Predefined_ClearStreamingOnly）。

## <a name="streaming-locator"></a>串流定位器  

當您在媒體服務帳戶中啟動串流端點，並定義串流原則之後，您可以從資產透過 HLS 或虛線通訊協定繼續串流錄製的媒體。 Web 播放機和行動應用程式需要指向該 HLS 或破折號串流的 URL。 您可以使用[串流定位器](../latest/streaming-locators-concept.md)來建立此 URL。 如本文中所討論，以及[建立串流定位器和組建 url](../latest/create-streaming-locator-build-url.md)範例中所述，串流 URL 是由串流端點、串流原則和串流定位器所組成。

## <a name="content-recorded-using-file-sink"></a>使用 file sink 錄製的內容  

如[media graph 檔案接收](media-graph-concept.md#file-sink)中所述，您可以使用媒體圖形，將影片記錄到 edge 裝置的本機檔案系統中，並使用 media graph 中的 file 接收。 File 接收[會產生有](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4)案的檔案，您可以使用 HTML5 [ &lt; video &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video)元素來播放這類內容。 

## <a name="next-steps"></a>後續步驟

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
