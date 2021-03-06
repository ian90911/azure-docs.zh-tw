---
title: 將 Orca 警示連接到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Orca 安全性警示資料連線到 Azure Sentinel、查看儀表板、建立自訂警示，以及改善調查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 1f2f67281079142f0c4fe4985738dc48332a107d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531300"
---
# <a name="connect-your-orca-alerts-to-azure-sentinel"></a>將您的 Orca 警示連接到 Azure Sentinel 

[Orca 安全性警示] 連接器可讓您輕鬆地將您的[Orca 警示](https://orca.security/)安全性解決方案警示帶入 Azure Sentinel，讓您可以在活頁簿中進行查看、使用它們來建立自訂警示，並將其納入以改善調查。 在 Orca 安全性警示與 Azure Sentinel 之間的整合會使用 REST API。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-orca-security-alerts"></a>設定及連接 Orca 安全性警示

Orca 安全性警示可以將記錄直接整合到 Azure Sentinel，並將其匯出。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 [ **Orca 安全性警示**]，然後**開啟 [連接器] 頁面**。

2. 請參閱 https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration ，以完成來自 Orca 平臺的整合。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會出現在 Log Analytics 的 [ **OrcaAlerts_CL** ] 資料表中的 [ **CustomLogs** ] 底下。
若要在 Log Analytics 中針對 Orca 警示使用相關的架構，請搜尋 `OrcaAlerts_CL` 。

## <a name="validate-connectivity"></a>驗證連線能力
可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Orca 安全性警示連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

