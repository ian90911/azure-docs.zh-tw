---
title: 事件回應教學課程 - Azure 資訊安全中心
description: 在本教學課程中，您將了解如何將安全性警示分級、判斷事件的根本原因和範圍，以及搜尋安全性資料。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: 971b93422e2b3f2053e0c5564c7ba924a631d810
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585777"
---
# <a name="tutorial-respond-to-security-incidents"></a>教學課程：回應安全性事件
資訊安全中心會使用進階的分析和威脅情報，持續分析混合式雲端工作負載，提醒您發生惡意活動。 此外，您可以將警示從其他的安全性產品和服務整合到資訊安全中心，並以您自己的指標或情報來源作為基礎建立自訂警示。 一旦有警示產生，您便需要迅速採取行動來進行調查並加以修復。 在本教學課程中，您將學會如何：

> [!div class="checklist"]
> * 將安全性警示分級
> * 進一步調查，以判斷安全性事件的根本原因及範圍
> * 搜尋安全性資料以協助調查

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites
若要逐步執行本教學課程中涵蓋的功能，您必須是在資訊安全中心的標準定價層。 您可以免費試用資訊安全中心標準層。 若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。 [將 Azure 訂用帳戶上架到資訊安全中心標準定價層](security-center-get-started.md)快速入門會為您逐步解說如何升級至「標準」定價層。

## <a name="scenario"></a>狀況
Contoso 最近將一些內部部署資源移轉至 Azure，包括一些以虛擬機器為基礎的商務營運工作負載和 SQL Database。 Contoso 的核心電腦安全性事件回應小組 (CSIRT) 目前因為尚未整合與其目前事件回應工具的安全性情報，而無法順利調查安全性問題。 缺乏此種整合會在偵測階段帶來問題 (太多誤判)，也會在評估和診斷階段造成問題。 在此移轉過程中，他們決定選擇加入資訊安全中心，以協助他們解決這個問題。

此移轉的第一個階段會在所有資源上架並解決資訊安全中心的所有安全性建議後完成。 Contoso CSIRT 是處理電腦安全性事件的焦點。 該小組是由一群負責處理任何安全性事件的人員所組成。 小組成員已清楚定義職責，以確保涵蓋所有的回應區域。

基於此案例的目的，我們會將重點放在屬於 Contoso CSIRT 的下列人物的角色︰

![事件回應生命週期](./media/tutorial-security-incident/security-center-incident-response.png)

Judy 負責安全性作業。 他們的職責包括︰

* 隨時監視及回應安全性威脅。
* 視需要提升至雲端工作負載擁有者或安全性分析師。

Sam 是安全性分析師，他們的職責包括︰

* 調查攻擊。
* 修復警示。
* 與工作負載擁有者一起來判斷和套用緩和措施。

如您所見，Judy 和 Sam 的責任不同，而他們必須共同合作以分享資訊安全中心的資訊。

## <a name="triage-security-alerts"></a>將安全性警示分級
資訊安全中心可讓您統一檢視所有安全性警示。 安全性警示會依嚴重性來設定順位，並視情況將相關警示合併到一個安全性事件。 在為警示和事件分級時，您應該：

- 將無需任何其他動作的警示關閉，例如，若警示為誤判
- 採取行動來修復已知的攻擊，例如封鎖惡意 IP 位址的網路流量
- 判斷需要進一步調查的警示


1. 在 [資訊安全中心] 主功能表上，選取 [偵測]  下的 [安全性警訊]  ：

   ![安全性警示](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. 在警示清單中對某個安全性事件 (此為警示集合) 按一下，以深入了解此事件。 [偵測到安全性事件]  隨即開啟。

   ![安全性事件](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. 在這個畫面上，您會在上方看到安全性事件描述，以及屬於此事件的警示清單。 按一下您要進一步調查的警示，以取得詳細資訊。

   ![安全性事件](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   警示類型可能會有所不同，如需警示類型的詳細資訊及可能的補救步驟，請閱讀[了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 對於可安全關閉的警示，您可以滑鼠右鍵按一下警示，並選取 [關閉]  選項：

   ![警示](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. 如果惡意活動的根本原因和範圍不明時，請繼續進行下一個步驟以進一步調查。

## <a name="investigate-an-alert-or-incident"></a>調查警示或事件
1. 在 [安全性警示]  頁面上，按一下 [開始調查]  按鈕 (如果您已啟動，名稱會變更為 [繼續調查]  )。

   ![調查](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   調查地圖會以圖形來表示連線到此安全性警示或事件的實體。 按一下地圖中的實體，該實體的相關資訊就會顯示新的實體，並展開地圖。 地圖中選取的實體會在頁面的右側窗格中將其屬性反白顯示。 每個索引標籤上的可用資訊會根據選取的實體而有所不同。 在調查流程中，檢閱所有相關資訊以深入了解攻擊者的移動路線。

2. 如果您需要更多證據，或必須對調查期間找到的實體進一步調查時，請繼續進行下一個步驟。

## <a name="search-data-for-investigation"></a>搜尋資料以進行調查

您可以使用資訊安全中心內的搜尋功能，來尋找更多關於遭入侵系統的證據，以及尋找屬於調查一部分之實體的詳細資料。

若要執行搜尋，請開啟 [資訊安全中心]  儀表板，按一下左側瀏覽窗格中的 [搜尋]  ，選取您要搜尋之實體所在的工作區，輸入搜尋查詢，然後按一下 [搜尋] 按鈕。

## <a name="clean-up-resources"></a>清除資源
此集合中的其他快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續處理後續的快速入門和教學課程，請繼續執行標準層，並保持將自動佈建維持為啟用狀態。 如果您不打算繼續，或是要返回免費層：

1. 返回 [資訊安全中心] 主功能表，並選取 [安全性原則]  。
2. 選取您需要返回免費層的訂用帳戶或原則。 [安全性原則]  隨即開啟。
3. 在 [原則元件]  下，選取 [定價層]  。
4. 選取 [免費]  以將訂用帳戶從標準層變更為免費層。
5. 選取 [儲存]  。

如果您需要停用自動佈建：

1. 返回 [資訊安全中心] 主功能表，並選取 [安全性原則]  。
2. 選取您想要停用自動佈建的訂用帳戶。
3. 在 [安全性原則 - 資料收集]  下，選取 [上架]  底下的 [關閉]  以停用自動佈建。
4. 選取 [儲存]  。

>[!NOTE]
> 停用自動佈建不會從已佈建代理程式的 Azure VM 移除 Log Analytics 代理程式。 停用自動佈建會限制對資源的安全性監視。
>

## <a name="next-steps"></a>後續步驟
在此教學課程中，您已了解在回應安全性事件時要使用的資訊安全中心功能，例如：

> [!div class="checklist"]
> * 安全性事件，此為資源相關警示的彙總
> * 調查地圖，此地圖會以圖形來表示連線到安全性警示或事件的實體
> * 搜尋功能，用以尋找更多關於遭入侵系統的證據