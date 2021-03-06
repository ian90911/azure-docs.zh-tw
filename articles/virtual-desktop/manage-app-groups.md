---
title: 管理 Windows 虛擬桌面的應用程式群組 (入口網站) - Azure
description: 如何使用 Azure 入口網站管理 Windows 虛擬桌面應用程式群組。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f072ed8a758173645c886cabf0b20f9e123cbbab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612110"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>教學課程：使用 Azure 入口網站管理應用程式群組

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/manage-app-groups-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

為新的 Windows 虛擬桌面主機集區建立的預設應用程式群組，也會發佈完整桌面。 此外，您可以為主機集區建立一或多個 RemoteApp 應用程式群組。 遵循本教學課程來建立 RemoteApp 應用程式群組及發佈個別 [開始] 功能表應用程式。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 RemoteApp 群組。
> * 授與 RemoteApp 程式的存取權。

## <a name="create-a-remoteapp-group"></a>建立 RemoteApp 群組

如果您已使用 Azure 入口網站或 PowerShell 建立主機集區和工作階段主機 VM，您可以透過下列程序，從 Azure 入口網站新增應用程式群組：

1.  登入 [Azure 入口網站](https://portal.azure.com/)。

2.  搜尋並選取 **Windows 虛擬桌面**。

3.  在頁面左側的功能表中選取 [應用程式群組]  ，然後選取 [+ 新增]  。

4. 在 [基本資料]  索引標籤上，選取您想要為其建立應用程式群組的訂用帳戶群組和資源群組。 您也可以選擇建立新的資源群組，而不是選取現有的資源群組。

5. 從 [主機集區]  旁的下拉式功能表中，選取將與應用程式群組相關聯的主機集區。

    >[!NOTE]
    >您必須選取與應用程式群組相關聯的主機集區。 應用程式群組具有從工作階段主機提供服務的應用程式或桌面，而工作階段主機則是主機集區的一部分。 應用程式群組必須在建立期間與主機集區相關聯。

    > [!div class="mx-imgBorder"]
    > ![Azure 入口網站中 [基本資料] 索引標籤的螢幕擷取畫面。](media/basics-tab.png)

6. 如果您想要將應用程式群組新增至主機集區，請在畫面左側的功能表中選取 [主機集區]  。
   
    接下來，選取您想要對其新增應用程式群組的主機集區名稱。
   
    然後，從畫面左側的功能表中選取 [應用程式群組]  ，然後選取 [+ 新增]  。

    最後，選取您想要在其中建立應用程式群組的訂用帳戶群組和資源群組。 您可以從下拉式功能表中選取現有的資源群組，或選取 [新建]  來建立新的資源群組。

      >[!NOTE]
      >當您將應用程式群組新增至主機集區時，表示您已選取與該應用程式群組相關的主機集區，因為您已從其中進行瀏覽。
      > 
      > [!div class="mx-imgBorder"]
      >![[基本資料] 索引標籤的螢幕擷取畫面，其中已預先選取主機集區。](media/host-pool-selected.png)

7. 在 [應用程式群組類型] 底下選取 [RemoteApp]  ，然後輸入 RemoteApp 的名稱。

      > [!div class="mx-imgBorder"]
      > ![應用程式群組類型欄位的螢幕擷取畫面。 [RemoteApp] 已醒目提示。](media/remoteapp-button.png)

8.  選取 [指派]  索引標籤。

9.  若要將個別使用者或使用者群組發佈到應用程式群組，請選取 [+ 新增 Azure AD 使用者或使用者群組]  。

10.  選取您想要對多少使用者新增應用程式。 您可以選取一個或多個使用者和使用者群組。

     > [!div class="mx-imgBorder"]
     > ![使用者選取功能表的螢幕擷取畫面。](media/select-users.png)

11.  選取 [選取]  。

12.  選取 [應用程式]  索引標籤，然後選取 [+ 新增應用程式]  。

13.  若要從 [開始] 功能表新增應用程式： 

      - 移至 [應用程式來源]  ，然後從下拉式功能表中選取 [開始功能表]  。 接下來，移至 [應用程式]  ，然後從下拉式功能表中選擇應用程式。

     > [!div class="mx-imgBorder"]
     > ![新增應用程式畫面的螢幕擷取畫面，其中已選取 [開始] 功能表。](media/add-app-start.png)

      - 在 [顯示名稱]  中，輸入使用者將在用戶端上看見的應用程式名稱。

      - 將其他選項保留為原狀，然後選取 [儲存]  。

14. 若要從特定檔案路徑新增應用程式：

      - 移至 [應用程式來源]  ，然後從下拉式功能表中選取 [檔案路徑]  。

      - 輸入工作階段主機 (已向相關聯主機集區註冊) 上的應用程式路徑。

      - 在 [應用程式名稱]  、[顯示名稱]  、[圖示路徑]  和 [圖示索引]  欄位中輸入應用程式的詳細資料。

      - 選取 [儲存]  。

     > [!div class="mx-imgBorder"]
     > ![新增應用程式頁面的螢幕擷取畫面，其中已選取檔案路徑。](media/add-app-file.png)

     針對您要新增到應用程式群組的每個應用程式，重複執行此流程。

15.  接下來，選取 [工作區]  索引標籤。

16.  如果您想要將應用程式群組註冊到工作區，請移至 [註冊應用程式群組]  ，然後選取 [是]  。 如果您想要在稍後註冊應用程式群組，請選取 [否]  。

17.  如果您選取 [是]  ，則可以選取現有的工作區來註冊應用程式群組。
       
       >[!NOTE]
       >您只能對建立在主機集區所在位置的工作區註冊應用程式群組。 此外， 如果您先前已從新應用程式群組所在的主機集區中，將其他應用程式群組註冊到工作區，則會選取相同工作區，而且您無法進行編輯。 主機集區中的所有應用程式群組都必須註冊到相同的工作區。

     > [!div class="mx-imgBorder"]
     > ![對已存在工作區註冊應用程式群組的頁面螢幕擷取畫面。 已預先選取主機集區。](media/register-existing.png)

18. 或者，如果您想要建立標籤以輕鬆地組織您的工作區，請選取 [標籤]  索引標籤，然後輸入您的標籤名稱。

19. 當您完成時，選取 [檢閱 + 建立]  索引標籤。

20. 等待驗證程序完成。 完成時，請選取 [建立]  以部署您的應用程式群組。

部署程序會為您執行下列作業：

- 建立 RemoteApp 應用程式群組。
- 將選取的應用程式新增至應用程式群組。
- 將應用程式群組發佈給您選取的使用者和使用者群組。
- 註冊應用程式群組 (如果您選擇這麼做)。
- 根據您的設定建立 Azure Resource Manager 範本的連結，以便您稍後下載並儲存。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立應用程式群組、以 RemoteApps 程式填入群組，以及將使用者指派給應用程式群組。 若要深入了解如何建立驗證主機集區，請參閱下列教學課程。 您可以使用驗證主機集區來監視服務更新，再將其推展到生產環境。

> [!div class="nextstepaction"]
> [建立主機集區以驗證服務更新](./create-validation-host-pool.md)
