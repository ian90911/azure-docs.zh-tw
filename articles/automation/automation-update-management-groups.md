---
title: 使用動態群組搭配 Azure 自動化更新管理
description: 本文說明如何使用動態群組搭配 Azure 自動化更新管理。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 25a306b3281079e8476a67ee70c2ca2cfffdd30c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185733"
---
# <a name="use-dynamic-groups-with-update-management"></a>搭配更新管理使用動態群組

「更新管理」可讓您以 Azure 或非 Azure VM 的動態群組作為更新部署目標。 使用動態群組讓您不必編輯部署即可更新機器。

> [!NOTE]
> 動態群組無法搭配傳統 VM 使用。

您可以從 Azure 入口網站中的 [更新管理]，定義 Azure 或非 Azure 機器的動態群組。 請參閱[管理多部 Azure 虛擬機器的更新](manage-update-multi.md)。

動態群組是由 Azure 自動化在部署階段評估的查詢所定義。 即使動態群組查詢擷取大量的機器，Azure 自動化一次最多只能處理 1000 部機器。 請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../azure-resource-manager/management/azure-subscription-service-limits.md#update-management)。 

> [!NOTE]
> 如果您預計更新 1000 部以上的機器，建議您將更新分散在多個更新排程中。 

## <a name="define-dynamic-groups-for-azure-machines"></a>定義 Azure 機器的動態群組

為 Azure 機器定義動態群組查詢時，您可使用下列項目來填入動態群組：

* 訂用帳戶
* 資源群組
* 位置
* Tags

![選取群組](./media/automation-update-management/select-groups.png)

若要預覽動態群組查詢的結果，請按一下 [預覽]。 預覽會顯示當時的群組成員資格。 在此範例中，我們所搜尋的機器是 **BackendServer** 群組中具有 `Role` 標記的機器。 如果有更多機器新增此標記，系統就會將其新增至針對該群組進行的所有未來部署。

![預覽群組](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>定義非 Azure 機器的動態群組

非 Azure 機器的動態群組會使用已儲存的搜尋，也稱為「電腦群組」。 若要了解如何建立已儲存的搜尋，請參閱[建立電腦群組](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 建立已儲存的搜尋之後，您可從 Azure 入口網站中 [更新管理] 的已儲存搜尋清單中加以選取。 按一下 [預覽] 以預覽已儲存搜尋中的電腦。

![選取群組](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>後續步驟

若要進行 VM 管理，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
