---
title: 媒體服務中的內容金鑰原則 - Azure | Microsoft Docs
description: 本文解釋內容金鑰原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 85a9cad80156dc6ac40e78610c91805d485ff3df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80585999"
---
# <a name="content-key-policies"></a>內容金鑰原則

使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 

若要在您的串流上指定加密選項，您需要建立[串流原則](streaming-policy-concept.md)，並將它與您的[串流定位器](streaming-locators-concept.md)建立關聯。 您會建立[內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies)，以設定內容金鑰（提供[資產](assets-concept.md)的安全存取）如何傳遞給終端用戶端。 您需要設定必須符合的內容金鑰原則需求（限制），才能將具有指定設定的金鑰傳遞給用戶端。 清除串流或下載時不需要內容金鑰原則。 

通常，您會將內容金鑰原則與您的[串流定位器](streaming-locators-concept.md)建立關聯。 或者，您也可以在[串流原則](streaming-policy-concept.md)內指定內容金鑰原則（為 advanced 案例建立自訂串流原則時）。 

## <a name="best-practices-and-considerations"></a>最佳做法和考慮

> [!IMPORTANT]
> 請參閱下列建議。

* 您應該為媒體服務帳戶設計一組有限的原則，並且在需要相同的選項時，針對您的串流定位器重複使用它們。 如需詳細資訊，請參閱[配額和限制](limits-quotas-constraints.md)。
* 內容金鑰原則是可更新的。 金鑰傳遞快取最多可能需要15分鐘的時間來更新並收取已更新的原則。 

   藉由更新原則，您會覆寫現有的 CDN 快取，這可能會對使用快取內容的客戶造成播放問題。  
* 我們建議您不要為每個資產建立新的內容金鑰原則。 在需要相同原則選項的資產之間共用相同內容金鑰原則的主要優點如下：
   
   * 很容易就能管理少量的原則。
   * 如果您需要更新內容金鑰原則，這些變更幾乎會立即對所有新的授權要求生效。
* 如果您需要建立新的原則，則必須為資產建立新的串流定位器。
* 建議讓媒體服務自動產生內容金鑰。 

   一般來說，您會使用長時間執行的金鑰，並使用[Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get)檢查內容金鑰原則是否存在。 若要取得金鑰，您必須呼叫個別的動作方法，以取得秘密或認證，請參閱下列範例。

## <a name="example"></a>範例

若要取得金鑰，請使用 `GetPolicyPropertiesWithSecretsAsync` ，如[從現有原則取得簽署金鑰](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)範例中所示。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="additional-notes"></a>其他注意事項

* 類型的內容金鑰原則屬性 `Datetime` 一律為 UTC 格式。
* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="next-steps"></a>後續步驟

* [使用 AES-128 動態加密和金鑰傳遞服務](protect-with-aes128.md)
* [使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
