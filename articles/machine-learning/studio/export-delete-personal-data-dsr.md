---
title: 匯出及刪除您的資料
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio （傳統）所儲存的產品內資料可透過 Azure 入口網站和通過驗證的 REST Api 來進行匯出和刪除。 透過 Azure 隱私權入口網站可以存取遙測資料。 本文將說明如何做到。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 9f623357dfbc8fab8a2967df65cc53abc4bc9d57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84696128"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>從 Azure Machine Learning Studio 匯出和刪除產品中的使用者資料（傳統）

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

您可以使用 Azure 入口網站、Studio （傳統）介面、PowerShell 和已驗證的 REST Api，來刪除或匯出 Azure Machine Learning Studio （傳統）儲存的產品中資料。 本文會說明做法。 

透過 Azure 隱私權入口網站可以存取遙測資料。 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Studio （傳統）會收集哪些類型的使用者資料？

在這項服務中，使用者資料包含有權存取工作區的使用者相關資訊，以及使用者與服務互動的遙測記錄。

Machine Learning Studio （傳統）中有兩種使用者資料：
- **個人帳戶資料：** 與帳戶相關聯的帳戶識別碼和電子郵件地址。
- **客戶資料：** 您上傳以供分析的資料。

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio （傳統）帳戶類型以及資料的儲存方式

Machine Learning Studio （傳統）中有三種帳戶。 您擁有的帳戶種類可決定您資料的儲存方式，以及其刪除或匯出方式。

- **來賓工作區**是免費的匿名帳戶。 您不需提供認證 (例如電子郵件地址或密碼) 即可註冊。
    -  資料會在來賓工作區過期後清除。
    - 來賓使用者可以透過 UI、REST API 或 PowerShell 套件匯出客戶資料。
- **免費工作區**是您可使用 Microsoft 帳戶認證 (電子郵件地址和密碼) 登入的免費帳戶。
    - 您可以匯出及刪除個人和客戶資料，而這些資料受資料主體權限 (DSR) 要求所約束。
    - 您可以透過 UI、REST API 或 PowerShell 套件匯出客戶資料。
    - 若為未使用 Azure AD 帳戶的免費工作區，可以使用隱私權入口網站匯出遙測。
    - 當您刪除工作區時，您會刪除所有個人和客戶資料。
- **標準工作區**是您可使用登入認證存取的付費帳戶。
    - 您可以匯出及刪除個人和客戶資料，而這些資料受 DSR 要求所約束。
    - 您可以透過 Azure 隱私權入口網站存取資料
    - 您可以透過 UI、REST API 或 PowerShell 套件匯出個人和客戶資料。
    - 您可以在 Azure 入口網站中刪除您的資料。

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>在 Studio 中刪除工作區資料（傳統） 

### <a name="delete-individual-assets"></a>刪除個別資產

使用者可藉由選取工作區中的資產，然後選取 [刪除] 按鈕加以刪除。

![刪除 Machine Learning Studio 中的資產（傳統）](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>刪除整個工作區

使用者也可以刪除整個工作區：
- 付費工作區：透過 Azure 入口網站刪除。
- 免費工作區：使用 [設定]**** 窗格中的 [刪除] 按鈕。

![刪除 Machine Learning Studio 中的免費工作區（傳統）](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>使用 PowerShell 匯出 Studio （傳統）資料
使用 PowerShell，使用命令從 Azure Machine Learning Studio （傳統）將您所有的資訊匯出為可攜的格式。 如需相關資訊，請參閱[Azure Machine Learning Studio （傳統）的 PowerShell 模組](powershell-module.md)文章。

## <a name="next-steps"></a>後續步驟

如需涵蓋 web 服務和承諾用量方案計費的檔，請參閱[Azure Machine Learning Studio （傳統） REST API 參考](https://docs.microsoft.com/rest/api/machinelearning/)。 
