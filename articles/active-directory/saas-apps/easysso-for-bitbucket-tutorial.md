---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 EasySSO for BitBucket 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 EasySSO for BitBucket 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ed96e97-b590-4dca-8d00-36288444c641
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946785f02461706d1d3675d089c19d68816fb8ca
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077077"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 EasySSO for BitBucket 整合

在本教學課程中，您將了解如何整合 EasySSO for BitBucket 與 Azure Active Directory (Azure AD)。 在整合 EasySSO for BitBucket 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 EasySSO for BitBucket 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 EasySSO for BitBucket。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用進行單一登入 (SSO) 之 EasySSO for BitBucket 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* EasySSO for BitBucket 支援由 SP 和 Idp 起始的 SSO。
* EasySSO for BitBucket 支援 "Just-In-Time" 使用者佈建。
* 在設定 EasySSO for BitBucket 之後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>從資源庫新增 EasySSO for BitBucket

若要進行將 EasySSO for BitBucket 整合到 Azure AD 中的設定，您必須從資源庫將 EasySSO for BitBucket 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 移至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增新的應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **EasySSO for BitBucket**。
1. 從結果中選取 [EasySSO for BitBucket]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>設定及測試 EasySSO for BitBucket 的 Azure AD 單一登入

使用名為 **B.Simon** 的測試使用者，搭配 EasySSO for BitBucket 設定及測試 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 EasySSO for BitBucket 中相關使用者之間的連結關聯性。

若要設定及測試與 EasySSO for BitBucket 搭配運作的 Azure AD SSO，請完成下列步驟：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓您的使用者能夠使用此功能。
    1. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 B.Simon 測試 Azure AD 單一登入。
    1. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. [設定 EasySSO for BitBucket SSO](#configure-easysso-for-bitbucket-sso)，在應用程式端設定單一登入設定。
    1. [建立 EasySSO for BitBucket 測試使用者](#create-an-easysso-for-bitbucket-test-user)，使 EasySSO for BitBucket 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. [測試 SSO](#test-sso)，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [EasySSO for BitBucket] 應用程式整合頁面上，尋找 [管理] 區段。 選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，選取 [基本 SAML 組態] 的鉛筆圖示，以編輯設定。

   ![以 SAML 設定單一登入頁面的螢幕擷取畫面，其中的鉛筆圖示已醒目提示](common/edit-urls.png)

1. 在 [基本 SAML 設定] 區段中，如果您想要以 **Idp** 起始模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL︰`https://<server-base-url>/plugins/servlet/easysso/saml`

1. 如果您想要以 **SP** 起始模式設定應用程式，請選取 [設定其他 URL]，然後執行下列步驟：

    - 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<server-base-url>/login.jsp`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 如有任何疑問，請連絡 [EasySSO 支援小組](mailto:support@techtime.co.nz)取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. EasySSO for BitBucket 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![預設屬性的螢幕擷取畫面](common/default-attributes.png)

1. EasySSO for BitBucket 應用程式還需要在 SAML 回應中傳回更多屬性。 下表顯示這些屬性。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。
    
    | 名稱 | 來源屬性|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    如果您的 Azure AD 使用者設定了 **sAMAccountName**，您必須將 **urn:oid:0.9.2342.19200300.100.1.1** 對應到 **sAMAccountName** 屬性。
    
1. 在 [使用 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，選取 [憑證 (Base64)] 或 [同盟中繼資料 XML] 選項的下載連結。 將任一項或兩者儲存到您的電腦。 您之後將會使用該資料設定 BitBucket EasySSO。

    ![[SAML 簽署憑證] 區段的螢幕擷取畫面，其中已醒目提示下載連結](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    如果打算使用憑證手動執行 EasySSO for BitBucket，則您也需要複製**登入 URL** 與 **Azure AD 識別碼**，並將其儲存在您的電腦上。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，選取 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 針對 [名稱] 輸入 `B.Simon`。  
   1. 針對 [使用者名稱]，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後寫下密碼。
   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 EasySSO for BitBucket 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式] > [所有應用程式]。
1. 在應用程式清單中，選取 [EasySSO for BitBucket]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

   ![管理區段的螢幕擷取畫面，醒目提示使用者和群組](common/users-groups-blade.png)

1. 選取 [新增使用者]。 在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![使用者和群組使用者及群組頁面的螢幕擷取畫面，其中已醒目提示 [新增使用者]](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊中，從 [使用者] 清單中選取 [B.Simon]，然後選擇畫面底部的 [選取]。
1. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當的使用者角色。 然後，選擇畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，選取 [指派]。

## <a name="configure-easysso-for-bitbucket-sso"></a>設定 EasySSO for BitBucket SSO

1. 使用管理員權限登入您的 Atlassia BitBucket 執行個體，然後移至 [管理] 區段。 

    ![BitBucket 執行個體的螢幕擷取畫面，其中已醒目顯示齒輪圖示](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. 尋找並選取 **EasySSO**。

    ![EasySSO 選項的螢幕擷取畫面](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. 選取 [SAML]。 這會帶您前往 SAML 組態區段。

    ![[EasySSO 管理] 頁面的螢幕擷取畫面，其中已醒目顯示 SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. 選取 [憑證] 索引標籤，下列畫面會隨即顯示：

    ![[憑證] 索引標籤的螢幕擷取畫面，其中已醒目提示各種選項](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. 找出您在本教學課程上一節中儲存的 [憑證 (Base64)] 或 [中繼資料檔案]。 您可以使用下列其中一個方法繼續進行：

    - 使用您下載到電腦上本機檔案的應用程式同盟 [中繼資料檔案]。 選取 [上傳] 選項按鈕，然後遵循您作業系統專用的路徑。

    - 開啟應用程式同盟 [中繼資料檔案]，以任何純文字編輯器查看檔案的內容。 將其複製到剪貼簿。 選取 [輸入]，並將剪貼簿內容貼到文字欄位中。
 
    - 執行完全手動設定。 開啟應用程式同盟 [憑證 (Base64)]，以任何純文字編輯器查看檔案的內容。 將其複製到剪貼簿，並貼到 [IdP 權杖簽署憑證] 文字欄位。 然後移至 [一般] 索引標籤，並分別在 [POST 繫結 URL] 和 [實體識別碼] 欄位中填入先前所儲存的 [登入 URL ] 和 [Azure AD 識別碼] 值。
 
1. 選取頁面底部的 [儲存 ]。 您便會看到中繼資料或憑證檔案的內容剖析到組態欄位中。 EasySSO for BitBucket 設定完成。

1. 若要測試組態，請移至 [外觀及風格] 索引標籤，然後選取 [SAML 登入] 按鈕。 這會在 BitBucket 登入畫面上啟用個別的按鈕，以專門用來端對端測試您的 Azure AD SAML 整合。 您可以讓此按鈕保持開啟狀態，並且也將其位置、色彩和轉譯設定為生產模式。

    ![SAML 頁面 [外觀及風格] 索引標籤的螢幕擷取畫面，其中已醒目顯示 [SAML 登入] 按鈕](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >如果您有任何問題，請連絡 [EasySSO 支援小組](mailto:support@techtime.co.nz)。

### <a name="create-an-easysso-for-bitbucket-test-user"></a>建立 EasySSO for BitBucket 測試使用者

在本節中，您會在 BitBucket 中建立名為 Britta Simon 的使用者。 EasySSO for BitBucket 支援依預設停用的 Just-In-Time 使用者佈建。 若要加以啟用，您必須在 EasySSO 外掛程式組態的 [一般] 區段中，明確地核取 [在登入成功時建立使用者]。 如果 BitBucket 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

不過，如果您不想要在使用者第一次登入時啟用自動使用者布建，則使用者必須存在於 BitBucket 執行個體所使用的使用者目錄中。 例如，此目錄可能是 LDAP 或 Atlassian Crowd。

![EasySSO 外掛程式組態的 [一般] 區段的螢幕擷取畫面，其中已醒目顯示 [在登入成功時建立使用者]](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>測試 SSO 

### <a name="idp-initiated-workflow"></a>IdP 起始的工作流程

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您選取 EasySSO for BitBucket 圖格時，應該會自動登入您已設定 SSO 的 BitBucket 執行個體。 如需詳細資訊，請參閱[登入我的應用程式入口網站並啟動應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

### <a name="sp-initiated-workflow"></a>SP 起始的工作流程

在本節中，您會使用 BitBucket 的 [SAML 登入] 按鈕，測試您的 Azure AD 單一登入組態。

![[登入] 畫面的螢幕擷取畫面，其中已醒目顯示 [SAML 登入]](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

此案例假設您已在 BitBucket EasySSO 組態頁面的 [外觀及風格] 索引標籤中，啟用了 [SAML 登入] 按鈕。 在瀏覽器的 incognito 模式中，開啟您的 BitBucket 登入 URL，以免干擾您現有的工作階段。 選取 [SAML 登入]，接著您將會被重新導向至 Azure AD 使用者驗證流程。 在成功完成了此項作業之後，您將會以通過 SAML 驗證的使用者身分，再被重新導向回您的 BitBucket 執行個體。

從 Azure AD 重新導向回來之後，您可能會遇到下列畫面：

![EasySSO 失敗畫面的螢幕擷取畫面，其中已醒目顯示參考編號](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

若是如此，請遵循[此頁面上的指示](https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS)，取得 **atlassian-bitbucket.log** 檔案的存取權。 您可以使用錯誤的參考識別碼，在 EasySSO 錯誤頁面上找到該錯誤的詳細資料。

如果您有任何問題，請連絡 [EasySSO 支援小組](mailto:support@techtime.co.nz)。

## <a name="additional-resources"></a>其他資源

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 EasySSO for BitBucket](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [使用進階可見性和控制項保護 EasySSO for BitBucket](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
