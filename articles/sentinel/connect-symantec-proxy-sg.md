---
title: 將 Symantec ProxySG 資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Symantec ProxySG 資料連線至 Azure Sentinel。
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
ms.openlocfilehash: 51a5c5de7bce07aa6a54b9ff81e957c38cfffdd2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531205"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>將您的 Symantec ProxySG 連線至 Azure Sentinel

本文說明如何將您的[Symantec ProxySG](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway)應用裝置連線到 Azure Sentinel。 Symantec ProxySG 資料連線器可讓您輕鬆地將 Symantec ProxySG 記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改善調查。 Symantec ProxySG 和 Azure Sentinel 之間的整合會使用 Syslog。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>將 Symantec ProxySG 記錄轉送到 Syslog 代理程式  

設定 Symantec ProxySG，以透過 Syslog 代理程式將 Syslog 訊息轉寄到您的 Azure 工作區。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 [ **Symantec ProxySG**連接器]。

1. 選取 [**開啟連接器] 頁面**。

1. 遵循**Symantec ProxySG**頁面上的指示。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會出現在 Syslog 底下的 Log Analytics 中。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Symantec ProxySG 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。