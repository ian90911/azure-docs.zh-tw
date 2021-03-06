---
title: 教學課程：Azure Active Directory 與 MVISION Cloud Azure AD SSO 組態整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 MVISION Cloud Azure AD SSO 組態之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 48d6ddd1-4d3e-4019-8234-5e5212684d9c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a519ab6558db95ecf86b7595dbbb13a970460043
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85806800"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>教學課程：整合 MVISION Cloud Azure AD SSO 組態與 Azure Active Directory

在本教學課程中，您將了解如何整合 MVISION Cloud Azure AD SSO 組態與 Azure Active Directory (Azure AD)。 當您將 MVISION Cloud Azure AD SSO 組態與 Azure AD 整合時，您可以：

* 在 Azure AD 中控制可存取 MVISION Cloud Azure AD SSO 組態的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 MVISION Cloud Azure AD SSO 組態。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 MVISION Cloud Azure AD SSO 組態單一登入 (SSO) 的訂用帳戶。


## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* MVISION Cloud Azure AD SSO 組態支援 **SP 和 IDP** 起始的 SSO
* 設定 Dropbox 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>從資源庫新增 MVISION Cloud Azure AD SSO 組態

若要設定 MVISION Cloud Azure AD SSO 組態與 Azure AD 整合，您需要從資源庫將 MVISION Cloud Azure AD SSO 組態新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中，輸入 **MVISION Cloud Azure AD SSO 組態**。
1. 從結果窗格中選取 [MVISION Cloud Azure AD SSO 組態]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **Britta Simon** 的測試使用者，設定及測試與 MVISION Cloud Azure AD SSO 組態搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 MVISION Cloud Azure AD SSO 組態中相關使用者之間的連結關聯性。

若要使用 MVISION Cloud Azure AD SSO 組態來設定及測試 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[設定 MVISION Cloud Azure AD SSO 組態 SSO](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 MVISION Cloud Azure AD SSO 組態測試使用者](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** - 使 MVISION Cloud Azure AD SSO 組態中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Datadog] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)


4. 在 [基本 SAML 組態] 區段上，若您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![MVISION Cloud Azure AD SSO 組態網域和 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「識別碼」、「回覆 URL」及「登入 URL」來更新這些值。 請連絡 [MVISION Cloud Azure AD SSO 組態用戶端支援小組](mailto:support@skyhighnetworks.com)來取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

7. 在 [設定 MVISION Cloud Azure AD SSO 組態] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 MVISION Cloud Azure AD SSO 組態的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [MVISION Cloud Azure AD SSO 組態]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [MVISION Cloud Azure AD SSO 組態]。

    ![應用程式清單中的 MVISION Cloud Azure AD SSO 組態連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。


## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>設定 MVISION Cloud Azure AD SSO 組態 SSO

若要設定 **MVISION Cloud Azure AD SSO 組態**端的單一登入，您必須將從 Azure 入口網站下載的 [憑證 (Base64)] 和複製的適當 URL 傳送給 [MVISION Cloud Azure AD SSO 組態支援小組](mailto:support@skyhighnetworks.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。


### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>建立 MVISION Cloud Azure AD SSO 組態測試使用者

在本節中，您會在 MVISION Cloud Azure AD SSO 組態中建立名為 B.Simon 的使用者。 使用  [MVISION Cloud Azure AD SSO 組態支援小組](mailto:support@skyhighnetworks.com)，在 MVISION Cloud Azure AD SSO 組態平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [MVISION Cloud Azure AD SSO 組態] 圖格時，應該會自動登入您設定 SSO 的 MVISION Cloud Azure AD SSO 組態。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 MVISION Cloud Azure AD SSO 組態](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)