---
title: Azure Application Insights 概觀儀表板 | Microsoft Docs
description: 使用 Azure Application Insights 和概觀儀表板的功能監控應用程式。
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e5188972d9058b85a9765c7d33f6209b37245d7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77669891"
---
# <a name="application-insights-overview-dashboard"></a>Azure Application Insights 概觀儀表板

Application Insights 一直以來都在提供摘要概覽窗格，以便一眼便能快速評量應用程式的健康情況和效能。 全新的概觀儀表板可提供更快、更有彈性的體驗。

## <a name="how-do-i-test-out-the-new-experience"></a>如何嘗試新的體驗？

新的概觀儀表板目前會依預設啟動：

![概觀預覽窗格](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>更好的效能

已將時間範圍選取功能簡化為輕鬆的單鍵介面。

![時間範圍](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

整體效能已大幅提升。 只要按一下，就能存取常用功能，例如**搜尋**和**分析**。 預設的每個動態更新 KPI 圖格均可讓您了解對應的 Application Insights 功能。 若要深入了解失敗的要求，請選取 [調查]**** 標題下的 [失敗]****：

![失敗](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>應用程式儀表板

應用程式儀表板會使用 Azure 中現有的儀表板技術，提供可完全自訂的單一窗格，讓您檢視應用程式的健康情況和效能。

若要使用預設儀表板，請選取左上角的 [應用程式儀表板]__。

![儀表板檢視](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

如果這是您第一次使用儀表板，便會啟動預設檢視：

![儀表板檢視](./media/overview-dashboard/0001-dashboard.png)

如果您喜歡預設檢視，即可保持原狀繼續使用。 您也可以在儀表板上新增和刪除項目，盡可能滿足小組的需求。

> [!NOTE]
> 具有 Application Insights 資源存取權的所有使用者，都會共用相同的應用程式儀表板體驗。 一位使用者所做的變更將會修改所有使用者的檢視。

若要回到概觀的畫面，只需選取：

![[概觀] 按鈕](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>疑難排解

如果您選取 [設定**磚設定**]，並將自訂的時間範圍設為超過31天，則您的儀表板不會顯示超過31天的資料，即使預設資料保留期為90天。 這種行為目前沒有任何因應措施。

## <a name="next-steps"></a>後續步驟

- [漏斗圖](../../azure-monitor/app/usage-funnels.md)
- [保留](../../azure-monitor/app/usage-retention.md)
- [使用者流程](../../azure-monitor/app/usage-flows.md)
