---
title: 使用 Azure 資訊安全中心建議增強安全性 | Microsoft Docs
description: " 了解如何使用「Azure 資訊安全中心」中的安全性原則和建議，來協助降低安全性攻擊的危害。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: 8039be7b69444cc32e763e9a1fb074e7dda4a5ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783226"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>使用 Azure 資訊安全中心建議增強安全性
您可以設定安全性原則，然後實作 Azure 資訊安全中心提供的建議，以降低發生重大安全性事件的機會。 本文說明如何使用資訊安全中心的安全性原則和建議，以協助減少安全性攻擊。 

資訊安全中心會自動執行連續掃描來分析 Azure 資源的安全性狀態。 當資訊安全中心發現潛在的安全性弱點時會建立建議，引導您完成設定所需安全性控制項的程序。 資訊安全中心會在24小時內更新其建議，但有下列例外狀況：

- 作業系統安全性設定建議會在48小時內更新
- Endpoint Protection 問題建議會在8小時內更新

## <a name="scenario"></a>狀況
此案例示範如何使用資訊安全中心，透過監控資訊安全中心建議並採取動作，以協助降低發生安全性事件的機會。 此案例使用虛構公司 Contoso，以及資訊安全中心[規劃與操作指南](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)中出現的角色。 在此案例中，我們著重於下列人物的角色：

![案例角色](./media/security-center-using-recommendations/scenario-roles.png)

Contoso 最近將一些內部部署資源移轉至 Azure。 Contoso 想要保護其資源，並減少雲端中其資源的弱點。

## <a name="use-azure-security-center"></a>使用 Azure 資訊安全中心
來自 Contoso IT 安全性部門的 David，已經選擇將 Contoso 訂用帳戶上的資訊安全中心上架到 Azure 資訊安全中心，以防止和偵測安全性弱點。 

資訊安全中心會自動分析 Contoso Azure 資源的安全性狀態並套用預設安全性原則。 當資訊安全中心識別潛在的安全性弱點時，它會根據安全性原則中設定的控制項來建立**建議**。 

David 會在其所有訂用帳戶中執行 Azure 安全性標準層，以取得完整的建議和安全性功能套件。 Jeff 也會將上線所有尚未遷移至雲端的現有內部部署伺服器，使其能夠利用其[Windows](quick-onboard-windows-computer.md)和[Linux](quick-onboard-linux-computer.md)伺服器上的資訊安全中心混合式支援。

Jeff 是雲端工作負載擁有者。 Jeff 負責根據 Contoso 的安全性原則，套用安全性控制項。 

Jeff 會執行下列工作：

- 監視資訊安全中心提供的安全性建議
- 評估安全性建議，並決定是否應套用或解除建議。
- 套用安全性建議

### <a name="remediate-threats-using-recommendations"></a>利用建議補救威脅
在日常監視活動中，Jeff 會登入 Azure 並開啟資訊安全中心。 

1. Jeff 會選取工作負載的訂閱。

2. Jeff 會檢查**安全分數**，以取得訂用帳戶如何保護的整體圖片，並查看分數為548。

3. Jeff 必須決定要先處理哪些建議。 因此 Jeff 按一下安全分數，並根據其[安全分數](secure-score-security-controls.md)的改善，開始處理建議。

4. Jeff 有大量已連線的 VM 和伺服器，因此決定著重於 [計算和應用程式]****。

5. 當 Jeff 按一下 [**計算和應用程式**] 時，他們會看到建議清單，並根據安全分數影響來處理它們。

6. Jeff 有許多網際網路對應的 Vm，而且因為其埠已公開，所以他們會擔心攻擊者可以取得伺服器的控制權。 因此 Jeff 選擇使用[**即時 VM 存取**](security-center-just-in-time.md)。

Jeff 繼續瀏覽高優先權和中優先順序的建議，然後進行實作決策。 針對每個建議，Jeff 會查看資訊安全中心所提供的詳細資訊，以瞭解哪些資源受到影響、安全分數的影響為何、每個建議的意義，以及如何降低每個問題的補救步驟。

## <a name="conclusion"></a>結論
監控資訊安全中心的建議有助於在發生攻擊之前消除安全性弱點。 當您修復建議時，您的安全分數和工作負載的安全性狀態都會改善。 資訊安全中心會自動探索您所部署的新資源、根據您的安全性原則進行評估，並提供新的建議來保護它們。


## <a name="next-steps"></a>後續步驟
請確定您已備妥監視程序，您會在監視過程中定期檢查資訊安全中心的建議，以確保資源在一段時間內保持安全無虞。

本案例示範如何使用資訊安全中心的安全性原則和建議，以協助減少安全性攻擊。

瞭解如何透過[管理和回應安全性警示](security-center-managing-and-responding-alerts.md)來回應威脅。
