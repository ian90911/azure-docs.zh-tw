---
title: Azure Front 門板-應用層安全性 |Microsoft Docs
description: 本文可協助您瞭解 Azure Front 門板如何讓您保護應用程式的安全後端
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e458926930c1b95d48886559551878fc6c9d0673
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471790"
---
# <a name="application-layer-security-with-front-door"></a>Front Door 的應用程式層安全性
Azure Front-提供 web 應用程式保護功能來保護您的 web 應用程式免于遭受網路攻擊，以及常見的 web 弱點惡意探索，例如 SQL 插入式攻擊或跨網站腳本（XSS）。 針對 http(s) 前端啟用，Front Door 的應用程式層安全性散布全球各地且永遠開啟，可在遠離您後端的 Azure 網路邊緣遏止惡意攻擊。 Front Door 可提高安全性並將效能最佳化，向您的終端使用者提供快速又安全的 Web 體驗。

## <a name="application-protection"></a>應用程式保護
Front Door 的應用程式保護是在全球各地的每個邊緣環境配合應用程式設定的，而且會自動封鎖非 http(s) 流量觸達您的 Web 應用程式。 我們的多租用戶分散式架構可提供大規模全球保護，卻不必犧牲效能。 針對 http(s) 工作負載，Front Door 的 Web 應用程式保護服務可為自訂規則、對常見攻擊預先設定的規則集提供豐富的規則引擎，並詳細記錄符合規則的所有要求。 可彈性使用的支援動作包括允許、封鎖或僅記錄。

## <a name="custom-access-control-rules"></a>自訂存取控制規則
- **IP 允許清單和封鎖清單：** 您可以根據用戶端 IP 位址清單來設定自訂規則，以控制對 web 應用程式的存取。 支援 IP v4 與 IP v6 兩者
- **地理型存取控制：** 您可以設定自訂規則，根據用戶端 IP 所來自的國家/地區代碼控制對您 Web 應用程式的存取權
- **HTTP 參數篩選：** 您可以根據相符的 http(s) 要求參數 (包括標頭、URL 與查詢字串) 設定自訂存取規則

## <a name="azure-managed-rules"></a>Azure 受控規則
- 預設會啟用針對最常見 OWASP 弱點預先設定的規則集。 預覽期間的規則集包含 sqli 與 xss 要求檢查。 未來將會新增其他規則。 您可以從選擇只記錄動作開始，以驗證為您的應用程式預先設定的規則如預期般運作 

## <a name="rate-limiting"></a>速率限制
- 速率控制規則是要限制來自任何用戶端 IP 的異常高流量。  您可以設定用戶端 IP 在一分鐘的時間範圍內允許的 Web 要求數目閾值。

## <a name="centralized-protection-policy"></a>集中式保護原則
- 您可以定義數個保護規則，並依優先順序將其增至「原則」。 自訂規則的優先順序高於受控規則集，以允許例外。 單一原則會與您的 Web 應用程式相關聯。  相同的 Web 應用程式保護原則會複寫到所有位置的所有 Edge Server ，確保所有區域都有一致的安全性原則

## <a name="configuration"></a>組態
- 在預覽期間，您可以使用 REST API、PowerShell 或 CLI 來建立及部署 Front Door 的應用程式保護規則與原則。 在服務公開推出之前，支援以入口網站存取。 


## <a name="monitoring"></a>監視
Front Door 可讓您使用與 Azure 監視器整合以追蹤警示並輕鬆監視趨勢的即時計量，來監視對 Web 應用程式的攻擊。

## <a name="pricing"></a>定價
預覽期間免費提供 Front Door 的應用程式層安全性。


## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
