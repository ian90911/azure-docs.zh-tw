---
title: 教學課程：使用 Azure Active Directory 設定4me 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至4me。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 423ba8c7aea9659a4c91f68a01392954c2ba6db2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77059155"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>教學課程：設定4me 來自動布建使用者

本教學課程的目的是要示範要在4me 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至4me。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [4me 租使用者](https://www.4me.com/trial/)
* 4me 中具有系統管理員許可權的使用者帳戶。

## <a name="add-4me-from-the-gallery"></a>從資源庫新增4me

將4me 設定為使用 Azure AD 自動布建使用者之前，您需要從 Azure AD 應用程式庫將4me 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增4me，請執行下列步驟：**

1. 在**[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**4me**，在結果面板中選取 [ **4me** ]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 4me](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>將使用者指派給4me

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取4me。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給4me：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>將使用者指派給4me 的重要秘訣

* 建議將單一 Azure AD 使用者指派給4me，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給4me 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-4me"></a>設定自動使用者布建至4me 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在4me 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇啟用4me 的 SAML 型單一登入，請遵循[4me 單一登入教學](4me-tutorial.md)課程中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>若要在 Azure AD 中設定4me 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [4me]****。

    ![應用程式清單中的 4me 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建] 索引標籤](common/provisioning-automatic.png)

5. 若要取出4me 帳戶的**租使用者 URL**和**秘密權杖**，請遵循步驟6中所述的逐步解說。

6. 登入您的4me 管理主控台。 流覽至 [**設定**]。

    ![4me 設定](media/4me-provisioning-tutorial/4me01.png)

    在搜尋列中輸入**應用程式**。

    ![4me 應用程式](media/4me-provisioning-tutorial/4me02.png)

    開啟 [ **SCIM** ] 下拉式清單，以取出秘密權杖和 SCIM 端點。

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. 填入步驟5所示的欄位之後，按一下 [**測試連接**] 以確保 Azure AD 可以連接到4me。 如果連線失敗，請確定您的4me 帳戶具有系統管理員許可權，然後再試一次。

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [檔案] 。

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 4me**]。

    ![4me 使用者對應](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到4me 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對4me 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![4me 使用者對應](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 4me**]。

    ![4me 使用者對應](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至4me 的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對4me 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![4me 群組對應](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

15. 若要啟用4me 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

16. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到4me 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

17. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述4me 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* 針對測試和生產環境，4me 具有不同的 SCIM 端點 Url。 前者以 **. qa**結尾，而後者則以 **.com**結尾
* 4me 產生的秘密權杖的到期日為「世代」的一個月。
* 4me 不支援**刪除**作業

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)