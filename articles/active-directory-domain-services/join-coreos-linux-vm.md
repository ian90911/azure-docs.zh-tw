---
title: 將 CoreOS VM 加入 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何設定 CoreOS 虛擬機器並將其加入 Azure AD Domain Services 受控網域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 845b48d84040343f829648f9c7fda2372e3413dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84734736"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>將 CoreOS 虛擬機器加入 Azure Active Directory Domain Services 受控網域

若要讓使用者能夠使用一組認證來登入 Azure 中的虛擬機器（Vm），您可以將 Vm 加入 Azure Active Directory Domain Services （Azure AD DS）受控網域。 當您將 VM 加入 Azure AD DS 受控網域時，可以使用網域中的使用者帳戶和認證來登入和管理伺服器。 也會套用來自受控網域的群組成員資格，讓您控制對 VM 上檔案或服務的存取。

本文說明如何將 CoreOS VM 加入受控網域。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 屬於受控網域一部分的使用者帳戶。

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>建立並聯機至 CoreOS Linux VM

如果您在 Azure 中有現有的 CoreOS Linux VM，請使用 SSH 連接到它，然後繼續進行下一個步驟以[開始設定 VM](#configure-the-hosts-file)。

如果您需要建立 CoreOS Linux VM，或想要建立要與本文搭配使用的測試 VM，您可以使用下列其中一種方法：

* [Azure 入口網站](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

當您建立 VM 時，請注意虛擬網路設定，以確保 VM 可以與受控網域進行通訊：

* 將 VM 部署到已啟用 Azure AD Domain Services 的相同或對等互連虛擬網路中。
* 將 VM 部署到與您的 Azure AD Domain Services 受控網域不同的子網。

部署 VM 之後，請遵循使用 SSH 連接到 VM 的步驟。

## <a name="configure-the-hosts-file"></a>設定 hosts 檔案

為確保受控網域已正確設定 VM 主機名稱，請編輯 */etc/hosts*檔案，並設定主機名稱：

```console
sudo vi /etc/hosts
```

在*hosts*檔案中，更新*localhost*位址。 在下例中︰

* *aaddscontoso.com*是受控網域的 DNS 功能變數名稱。
* *coreos*是您要加入至受控網域之 coreos VM 的主機名稱。

使用您自己的值來更新這些名稱：

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

完成時，請使用編輯器的命令來儲存並結束*hosts*檔案 `:wq` 。

## <a name="configure-the-sssd-service"></a>設定 SSSD 服務

更新 */Etc/sssd/sssd.conf* sssd 設定。

```console
sudo vi /etc/sssd/sssd.conf
```

為下列參數指定您自己的受控功能變數名稱：

* 全大寫的*網域*
* *[domain/AADDS]* ，其中 AADDS 為全大寫
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* 全大寫的*krb5_realm*

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>將 VM 加入受控網域

更新 SSSD 設定檔之後，現在請將虛擬機器加入受控網域。

1. 首先，使用 `adcli info` 命令來確認您可以看到受控網域的相關資訊。 下列範例會取得網域*AADDSCONTOSO.COM*的資訊。 以大寫指定您自己的受控功能變數名稱：

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   如果 `adcli info` 命令找不到您的受控網域，請參閱下列疑難排解步驟：

    * 請確定可從 VM 連線到該網域。 請嘗試 `ping aaddscontoso.com` 查看是否傳回正面回復。
    * 檢查 VM 是否部署到可使用受控網域的相同或對等互連虛擬網路。
    * 確認虛擬網路的 DNS 伺服器設定已更新，以指向受控網域的網域控制站。

1. 現在使用命令將 VM 加入受控網域 `adcli join` 。 指定屬於受控網域的使用者。 如有需要，請[將使用者帳戶新增至 Azure AD 中的群組](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同樣地，必須以全部大寫輸入受控功能變數名稱。 在下列範例中，會使用名為的帳戶 `contosoadmin@aaddscontoso.com` 來初始化 Kerberos。 輸入屬於受控網域的使用者帳戶。

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    `adcli join`當 VM 已成功加入受控網域時，此命令不會傳回任何資訊。

1. 若要套用加入網域設定，請啟動 SSSD 服務：
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用網域帳戶登入 VM

若要確認 VM 已成功加入受控網域，請使用網域使用者帳戶啟動新的 SSH 連線。 確認已建立主目錄，並已套用網域的群組成員資格。

1. 從您的主控台建立新的 SSH 連線。 使用屬於受控網域的網域帳戶（ `ssh -l` 例如）， `contosoadmin@aaddscontoso.com` 然後輸入您 VM 的位址，例如*coreos.aaddscontoso.com*。 如果您使用 Azure Cloud Shell，請使用 VM 的公用 IP 位址，而不是內部 DNS 名稱。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. 現在檢查是否已正確解析群組成員資格：

    ```console
    id
    ```

    您應該會看到來自受控網域的群組成員資格。

## <a name="next-steps"></a>後續步驟

如果您在將 VM 連線到受控網域或使用網域帳戶登入時發生問題，請參閱針對[網域加入問題進行疑難排解](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
