---
title: 有效錨點體驗的指導方針
description: 使用 Azure 空間錨點有效率地建立和尋找錨點的指導方針和考慮。
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 1877934cb604d140d7700c2e537d6dc187b63cc4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83005517"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>使用 Azure 空間錨點建立有效的錨點體驗

本文提供指導方針和考慮，以協助您使用 Azure 空間錨點，有效地建立和尋找錨點。

## <a name="anchor-improvement-over-time"></a>一段時間的錨定改善

使用 Azure 空間錨點，每次您找到錨點時，我們會嘗試改善未來尋找作業的品質。 我們會使用所收集的環境資料，在我們要尋找的錨點上增加視覺資訊來完成這項工作。 此程式會在幕後執行，而且是由 Azure 空間錨點服務執行的離線優化，可針對您的環境進行優化。 在每個作業期間所收集的額外資料，會更深入瞭解環境。 這會改善品質，並可讓您透過環境變更、時間傳遞，以及從不同角度和觀點尋找錨點的使用者，更清楚地找出錨點。

## <a name="good-anchors"></a>良好錨點

雖然 Azure 空間錨點會在一段時間後嘗試改善錨點的品質，但在使用者體驗（UX）中進行教育或引導使用者以建立良好錨點也很重要。 藉由投資預先建立良好的錨定，您可以協助使用者可靠地找出錨點：

- 跨不同裝置。
- 在不同的時間。
- 在不同光源狀況中。
- 從空間內所需的角度來。

## <a name="static-and-dynamic-locations"></a>靜態和動態位置

設計錨點體驗的部分是選擇位置。 這些位置是靜態的，而且是由空間系統管理員所定義嗎？ 或者，它們會是動態的，而且是由使用者定義的嗎？

零售商店管理員可能會想要有靜態的存放區內部體驗，以吸引使用者造訪。 但是，混合現實委員會遊戲的開發人員可能會想要讓使用者選擇要玩的地方。

針對靜態位置，您可以讓系統管理員花時間策劃具有良好錨點的空間。

針對動態位置，您應該考慮如何教授或引導 UX 中的使用者建立良好錨點。

## <a name="stable-visual-features"></a>穩定的視覺效果功能

在混合現實和增強現實裝置上使用的視覺化追蹤系統依賴環境的視覺功能。 若要取得最可靠的體驗：

- *請*在具有穩定視覺功能的位置（也就是不常變更的功能）中建立錨點。

- *請勿*在沒有任何區別特性的大型空白表面上建立錨點。

- *請勿*在高反射材質上建立錨點。

- *請勿*在模式重複的表面上建立錨點，例如地毯或壁紙。

![錨點的良好環境範例，以及錨點的錯誤環境](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>各種不同的觀看角度

建立錨點時，請考慮稍後將嘗試尋找錨點的人員。

例如，請考慮在房間中間有兩門的錨點。 您可能想要讓使用者輸入任一門的房間。 建立錨點時，您必須從兩個門口掃描其位置。 您可以變更觀點來捕捉錨點周圍的環境資料，讓使用者可以從任一個門尋找錨點。

一般來說，在建立錨點時，請從將嘗試尋找它的人員觀點來掃描它。 因此，如果您要將虛擬內容放在戶外 sculpture 上，當您建立錨點時，就可以在進行掃描時進行 sculpture，這是很合理的做法。 如果您的錨點位於房間角落，則其中只有一個方向可以進行處理。 建立此錨點時，您可以從這種觀點來掃描它。

## <a name="multiple-anchors"></a>多個錨點

光源可以讓應用程式偵測到的視覺功能有所差異。 以強式自然光源建立的錨點可能難以在人工智慧中找到，反之亦然。

如果您遇到此問題，它可以協助建立兩個錨點。 在相同的位置上，在日光中建立一個錨點，然後在 [人工光線] 中建立另一個。 然後，您的應用程式就可以查詢這兩個錨點。 當找到任一錨點時，應用程式會有錨點的姿勢。

同樣地，在視覺功能因為大部分物件移動而變更的環境中，多個錨點可能會有説明。 當錨點因為環境中的重大變更而變得太難尋找時，您可以使用新的錨點來取代它。 例如，您可以在零售商店中執行此動作，其中版面配置會每隔幾個月重新整理一次。

## <a name="targets-and-rooms"></a>目標和聊天室

在許多情況下，錨點是您應用程式體驗的進入點。 您會想要快速且可靠地完成這個步驟，讓使用者能夠進入您的經驗。 花費時間來瞭解使用者如何尋找您的錨點是一個重要的設計步驟。 根據兩個廣泛的案例（*目標*和*房間*）來尋找錨點是很有説明的。

### <a name="targets"></a>目標

在目標案例中，錨點的位置知名。 例如，在虛構的混合事實繪製應用程式中，有一位使用者在牆上放置虛擬畫布。 她會指示房間中的其他使用者將其裝置指向牆上的相同位置，以找出錨點並開始體驗。

目標案例的另一個範例可能是「咖啡廳」的登入，它會閱讀「掃描交易」。 咖啡廳已將錨點放在這裡。 當使用者掃描正負號時，他們會找出錨點，並輸入增強的現實體驗，以找出咖啡的交易。

在目標案例中，相片可以提供協助。 如果您在裝置上向使用者顯示預定目標的相片，他們可以快速地識別要在真實世界中掃描的內容。 例如，您可以使用 GPS，協助使用者抵達預定目標的一般區域中。 當使用者抵達時，您的應用程式會顯示目標的相片。 使用者會尋找空間、尋找目標，以及掃描錨點。

![錨點的圖例，顯示使用者行動裝置上的目標相片](./media/start-here-edit.png)

### <a name="rooms"></a>聊天室

在聊天室案例中，使用者只要知道這裡有錨點就能輸入一個空格。 使用者會掃描其裝置的空間，並快速找出錨點。

此體驗通常會要求您建立策劃的錨點，如各種不同的觀看角度中所述。 如果您在建立錨點時從許多觀點掃描了房間，使用者在嘗試尋找它時幾乎可以進行掃描。

![說明使用者如何掃描房間以尋找錨點](./media/scan-room.png)

基本上，當您建立錨點時，會花費更多時間來掃描空間，讓之後的使用者可以快速地掃描和尋找錨點。 當您建立體驗時，您必須考慮這項重要的取捨。

我們稍早討論的混合現實繪製應用程式範例，無法做為會議室案例。 在這裡，放置錨點的使用者想要讓其他人快速加入體驗。 使用者不想等待開始體驗，直到房間掃描順利為止。 因為所有使用者都知道要指向其裝置以尋找錨點的確切位置，所以這個範例的運作方式會比目標案例更好。

## <a name="anchor-location"></a>錨點位置

視覺效果追蹤系統依賴環境中的視覺功能。 掃描所包含的視覺功能愈多，尋找錨點的可能性就越高。

請遵循本節中的一般指導方針，以建立鼓勵對環境進行實用掃描的 UX。

首先，如果使用者在幾秒內找不到錨點，應用程式應該鼓勵使用者移動裝置以取得更多的觀點。 應用程式也可以鼓勵使用者在環境中移動自己，以從更多觀點來掃描錨點。 裝置所看到的功能觀點越多越好，因為它會增加錨點的位置，而且也會收集更多用來改善錨點品質的環境資料。

針對目標案例，請要求使用者移動目標，以從不同的觀點來觀看。 換句話說，請要求使用者從新的觀點來捕捉目標，直到錨點找到為止。

針對房間案例，請要求使用者慢慢地掃描房間。 例如，要求使用者輪流抓住房間的180度或甚至360度。 或要求使用者從新的觀點來觀看房間。

最有意義的方法是在房間內掃描。 例如，跨房間掃描會捕捉比附近牆掃描更多的環境視覺功能。 附近的牆掃描並不會捕捉到環境中的許多有用視覺功能。

在尋找錨點時，將裝置從側邊重複移動並不會有説明。 這只會從相同的觀點來捕捉相同的點。

## <a name="experience-tests"></a>體驗測試

在本文中，我們已討論過一般方針。 有了空間錨點，您就可以撰寫與真實世界互動的應用程式。 因此，您應該投入時間來測試應用程式在實際環境中的錨點案例。 對於代表您希望使用者使用應用程式之位置的環境而言，尤其如此。
