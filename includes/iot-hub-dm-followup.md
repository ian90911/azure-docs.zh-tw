---
title: 包含檔案
description: 包含檔案
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "69558738"
---
## <a name="customize-and-extend-the-device-management-actions"></a>自訂及延伸裝置管理動作

您的 IoT 解決方案可以透過使用裝置對應項和雲端到裝置方法基元，擴充一組已定義的裝置管理模式或啟用自訂模式。 其他裝置管理動作範例還包括恢復出廠預設值、韌體更新、軟體更新、電源管理、網路和連線管理，以及資料加密。

## <a name="device-maintenance-windows"></a>裝置維護期間

一般而言，您會設定讓裝置在產生最短中斷和停機時間的時機執行動作。 裝置維護期間是用來定義裝置組態更新時機的常用模式。 您的後端解決方案可以使用所需的裝置對應項 (twin) 屬性，在您的裝置上定義可啟用維護期間的原則並啟用該原則。 當裝置收到維護期間原則時，它可以使用回報的裝置對應項 (twin) 屬性來回報原則的狀態。 接著，後端 App 便可使用裝置對應項 (twin) 查詢來證明是否符合裝置及每個原則的規定。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用直接方法在裝置上觸發遠端重新開機。 您已使用報告屬性來從裝置回報上次重新開機時間，以及查詢裝置對應項來從雲端探索裝置的上次重新開機時間。

若要繼續開始使用 IoT 中樞和裝置管理模式，例如遠端無線韌體更新，請參閱[如何更新韌體](../articles/iot-hub/tutorial-firmware-update.md)。

若要了解如何擴充 IoT 解決方案，以及在多部裝置上排程方法呼叫，請參閱[排程及廣播作業](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)。