---
title: 關於自我服務註冊流程中的 API 連接器-Azure AD
description: 使用 Azure Active Directory （Azure AD） API 連接器，以使用 web Api 來自訂和擴充您的自助式註冊使用者流程。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c5e546c6eac77c4952a0d32d360f49d4251d49d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905172"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>使用 API 連接器來自訂和擴充自助式註冊 

## <a name="overview"></a>總覽 
身為開發人員或 IT 系統管理員，您可以利用 web Api 來使用 API 連接器，將您的[自助註冊使用者流程](self-service-sign-up-overview.md)與外部系統整合。 例如，您可以使用 API 連接器來執行下列動作：

- [**與自訂核准工作流程整合**](self-service-sign-up-add-approvals.md)。 連接到自訂核准系統來管理帳戶建立。
- [**執行身分識別驗證**](code-samples-self-service-sign-up.md#identity-verification)。 使用身分識別驗證服務，為帳戶建立決策增加額外的安全性層級。
- **驗證使用者輸入資料**。 針對格式不正確或不正確使用者資料進行驗證。 例如，您可以針對外部資料存放區中的現有資料或允許的值清單，驗證使用者提供的資料。 如果無效，您可以要求使用者提供有效的資料，或封鎖使用者繼續註冊流程。
- **覆寫使用者屬性**。 重新格式化或指派值給從使用者收集的屬性。 例如，如果使用者輸入全部小寫或全部大寫的名字，您可以設定名稱格式為只有第一個字母大寫。 
<!-- - **Enrich user data**. Integrate with your external cloud systems that store user information to integrate them with the sign-up flow. For example, your API can receive the user's email address, query a CRM system, and return the user's loyalty number. Returned claims can be used to pre-fill form fields or return additional data in the application token.  -->
- **執行自訂商務邏輯**。 您可以在雲端系統中觸發下游事件以傳送推播通知、更新公司資料庫、管理許可權、審核資料庫，以及執行其他自訂動作。

API 連接器會藉由定義 HTTP 端點、驗證、要求和預期的回應，來代表 Azure AD 與 API 端點之間的合約。 設定 API 連接器之後，您可以在使用者流程的特定步驟中啟用它。 當使用者在註冊流程中達到該步驟時，會叫用 API 連接器並具體化為 HTTP POST 要求，並將選取的宣告當做 JSON 主體中的機碼值組來傳送。 API 回應可能會影響使用者流程的執行。 例如，API 回應可能會封鎖使用者的註冊、要求使用者重新輸入資訊，或覆寫和附加使用者屬性。

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>您可以在其中啟用使用者流程中的 API 連接器

使用者流程中有兩個位置可讓您啟用 API 連接器：

- 使用身分識別提供者登入之後
- 建立使用者之前

在這兩種情況下，API 連接器會在註冊期間叫用，而不是登入。

### <a name="after-signing-in-with-an-identity-provider"></a>使用身分識別提供者登入之後

在使用者使用身分識別提供者（Google、Facebook、Azure AD）進行驗證之後，就會立即叫用註冊程式中此步驟的 API 連接器。 此步驟會在 [***屬性集合] 頁面***之前，這是向使用者呈現以收集使用者屬性的表單。 以下是您可能會在此步驟啟用的 API 連接器案例範例：

- 使用使用者所提供的電子郵件或同盟身分識別，來查閱現有系統中的宣告。 從現有系統傳回這些宣告，預先填入 [屬性集合] 頁面，然後讓它們可在權杖中傳回。
- 驗證使用者是否包含在允許或拒絕清單中，並控制他們是否可以繼續註冊流程。

### <a name="before-creating-the-user"></a>建立使用者之前

在註冊程式的這個步驟中，API 連接器會在屬性集合頁面（如果有包含的話）之後叫用。 在 Azure AD 中建立使用者帳戶之前，一律會叫用此步驟。 以下是您在註冊期間可能會啟用的案例範例：

- 驗證使用者輸入資料，並要求使用者重新提交資料。
- 根據使用者輸入的資料封鎖使用者註冊。
- 執行身分識別驗證。
- 查詢外部系統中有關使用者的現有資料，以在應用程式權杖中傳回，或將其儲存在 Azure AD 中。

<!-- > [!IMPORTANT]
> If an invalid response is returned or another error occurs (for example, a network error), the user will be redirected to the app with the error re -->

## <a name="next-steps"></a>後續步驟
- 瞭解如何[將 API 連接器新增至使用者流程](self-service-sign-up-add-api-connector.md)
- 瞭解如何[將自訂核准系統新增至自助式註冊](self-service-sign-up-add-approvals.md)