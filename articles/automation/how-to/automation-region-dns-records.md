---
title: Azure 自動化使用的 Azure 資料中心 DNS 記錄 |Microsoft Docs
description: 本文提供在限制對裝載該自動化帳戶之特定 Azure 區域的通訊時，Azure 自動化功能所需的 DNS 記錄。
services: automation
ms.subservice: process-automation
ms.date: 06/22/2020
ms.topic: conceptual
ms.openlocfilehash: 44d70db195850b3f87806c69755095b521078b2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85298305"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Azure 自動化所使用之 Azure 區域的 DNS 記錄

[Azure 自動化](../automation-intro.md)服務會使用一些功能的 DNS 記錄來連接服務。 如果您有針對特定區域定義的自動化帳戶，您可以將通訊限制為該區域資料中心。 您可能需要知道這些記錄，以允許下列自動化功能在防火牆後方主控時正常執行：

* Hybrid Runbook Worker
* State Configuration
* Webhook

>[!NOTE]
>Linux 混合式 Runbook 背景工作角色註冊將會失敗，並出現新的記錄，除非它是1.6.10.2 或更高版本。 您必須升級為適用于[Linux 的 Log Analytics 代理程式](../../azure-monitor/platform/agent-linux.md)的較新版本，電腦才能接收背景工作角色的更新版本，並使用這些新記錄。 現有的機器會繼續運作，而不會發生任何問題。  

## <a name="dns-records-per-region"></a>每個區域的 DNS 記錄

下表提供每個區域的 DNS 記錄。

>[!NOTE]
>雖然此處提供的自動化 DNS 記錄清單已淘汰，但仍可繼續運作，讓您可以將時間遷移至 [[私人連結支援](#support-for-private-link)] 底下所列的新記錄，並防止自動化程式失敗。

| **區域** | **DNS 記錄** |
| --- | --- |
| 澳大利亞中部 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| 澳大利亞東部 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 英國南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 維吉尼亞州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 美國西部 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>私用連結的支援

為了支援 Azure 自動化中的[私用連結](../../private-link/private-link-overview.md)，已更新每個受支援資料中心的 DNS 記錄。 Url 是自動化帳戶特有的，而不是特定區域的 Url。

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中西部 |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| 美國西部 |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| 美國西部 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| 美國中部 |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| 美國中南部 |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| 美國中北部 |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| 美國東部 |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| 美國東部 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| 加拿大中部 |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| 西歐 |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| 北歐 |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| 東南亞 |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| 東亞 |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| 印度中部 |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| 日本東部 |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| 南韓中部 |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| 澳大利亞東南部 |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| 澳大利亞東部 |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| 澳大利亞中部 |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| 英國南部 |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| 法國中部 |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| 南非北部 |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| 巴西南部 |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| 中國北部 |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| 中國北部 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| 中國東部 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| US Gov 維吉尼亞州 |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov 德克薩斯州 |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov 亞利桑那州 |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

建議您使用定義[例外狀況](../automation-runbook-execution.md#exceptions)時所列出的位址。 如需區域 IP 位址的清單，而不是區功能變數名稱稱，請從 Microsoft 下載中心下載下列雲端環境的 JSON 檔案：

* [Azure IP 範圍和服務標記-Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
* [Azure IP 範圍和服務標籤-Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [Azure IP 範圍和服務標籤-Azure 德國](https://www.microsoft.com/download/details.aspx?id=57064)
* [Azure IP 範圍和服務標記-Azure 中國 Vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

IP 位址檔案會列出用於 Microsoft Azure 資料中心的 IP 位址範圍。 其包含計算、SQL 和儲存體範圍，並反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。

建議您每週下載新的 IP 位址檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。

> [!NOTE]
> 如果您是使用 Azure ExpressRoute，請記得在每個月的第一週，該 IP 位址檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

## <a name="next-steps"></a>後續步驟

* 若要了解如何對混合式 Runbook 背景工作角色進行疑難排解，請參閱[對混合式 Runbook 背景工作角色問題進行疑難排解](../troubleshoot/hybrid-runbook-worker.md#general)。

* 若要瞭解如何針對狀態設定的問題進行疑難排解，請參閱針對[狀態設定問題進行疑難排解](../troubleshoot/desired-state-configuration.md)。