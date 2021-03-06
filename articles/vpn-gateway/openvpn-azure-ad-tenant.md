---
title: VPN 閘道：用於 P2S VPN 連線的 Azure AD 租使用者： Azure AD 驗證
description: 您可以使用 P2S VPN，使用 Azure AD authentication 來連線到您的 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/17/2020
ms.author: alzam
ms.openlocfilehash: 2dda6cb84fc881b4ca628ff1cecdec7c00555e8b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414297"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>建立 Azure Active Directory 租用戶以進行 P2S OpenVPN 通訊協定連線

連線到您的 VNet 時，您可以使用以憑證為基礎的驗證或 RADIUS 驗證。 不過，當您使用 Open VPN 通訊協定時，您也可以使用 Azure Active Directory 驗證。 本文可協助您設定 P2S Open VPN 驗證的 Azure AD 租使用者。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. 確認 Azure AD 租使用者

請確認您有 Azure AD 的租使用者。 如果您沒有 Azure AD 租使用者，您可以使用[建立新的租](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)使用者一文中的步驟來建立一個：

* 組織名稱
* 初始網域名稱

範例：

   ![新增 Azure AD 租用戶](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. 建立 Azure AD 租使用者使用者

Azure AD 租使用者需要下列帳戶：全域管理員帳戶和主要使用者帳戶。 主要使用者帳戶會當做您的主要內嵌帳戶 (服務帳戶) 使用。 當您建立 Azure AD 租用戶使用者帳戶時，您會針對您想要建立的使用者類型調整目錄角色。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟，為您的 Azure AD 租用戶建立至少兩個使用者。 請務必變更**目錄角色**以建立帳戶類型：

* 全域管理員
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. 在 VPN 閘道上啟用 Azure AD 驗證

1. 找出目錄 (您要用於驗證) 的目錄識別碼。 會列在 Active Directory 頁面的屬性區段中。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. 複製目錄識別碼。

3. 以獲指派**全域管理員**角色的使用者身分登入 Azure 入口網站。

4. 接下來，給予管理員同意。 在瀏覽器的網址列中，複製並貼上您部署位置的相關 URL：

    公開

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 若出現提示，請選取**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 出現提示時選取 [接受]。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. 在您的 Azure AD 下的 [**企業應用程式**] 中，您會看到列出**Azure VPN** 。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. 如果您還沒有正常運作的點對站台環境，請依照指示來建立一個。 請參閱[建立點對站 vpn](vpn-gateway-howto-point-to-site-resource-manager-portal.md) ，以建立及設定點對站 vpn 閘道。 

    > [!IMPORTANT]
    > OpenVPN 不支援基本 SKU。

9. 流覽至 [**點對站**設定]，然後選取 [ **OpenVPN （SSL）** ] 作為 [通道**類型**]，以在 VPN 閘道上啟用 Azure AD 驗證。 選取 [ **Azure Active Directory** ] 作為 [**驗證類型**]，然後填入 [ **Azure Active Directory** ] 區段下的資訊。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png)


   > [!NOTE]
   > 請確定您在值的結尾包含尾端斜線 `AadIssuerUri` 。 否則，連接可能會失敗。

10. 按一下 [**下載 VPN 用戶端**] 連結，以建立並下載設定檔。

11. 將下載的 zip 檔案解壓縮。

12. 流覽至解壓縮的 "AzureVPN" 資料夾。

13. 記下「azurevpnconfig.xml」檔案的位置。 azurevpnconfig.xml 包含 VPN 連線的設定，而且可以直接匯入至 Azure VPN 用戶端應用程式。 您也可以將此檔案散發給所有需要透過電子郵件或其他方式連接的使用者。 使用者需要有效的 Azure AD 認證，才能成功連接。

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[設定 vpn 用戶端以進行 P2S vpn](openvpn-azure-ad-client.md)連線。
