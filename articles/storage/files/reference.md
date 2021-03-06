---
title: Azure 檔案儲存體參考
description: 尋找 Azure 儲存體 API 參考、讀我檔案和用戶端程式庫套件。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/14/2020
ms.service: storage
ms.topic: conceptual
ms.reviewer: ripohane
ms.openlocfilehash: c5799b7ec9f6ecce1b9203345446b2d3240beb80
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527841"
---
# <a name="azure-files-reference"></a>Azure 檔案儲存體參考

尋找 Azure 檔案儲存體的 API 參考、程式庫套件、讀我檔案和快速入門文章。

## <a name="net-client-libraries"></a>.NET 用戶端程式庫

下表列出 Azure 檔案儲存體 .NET Api 的參考和範例檔。

|  版本  | 參考文件 | 套件 | 快速入門 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [適用于 .NET 的 Azure 檔案儲存體用戶端程式庫 v12](/dotnet/api/overview/azure/storage.files.shares-readme) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files/) | &nbsp; |
| 11. x | [Microsoft. Azure. File 命名空間](/dotnet/api/microsoft.azure.storage.file) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/) | [使用 .NET 開發 Azure 檔案服務](/azure/storage/files/storage-dotnet-how-to-use-files) |

### <a name="storage-management"></a>存放管理

下表列出 Azure 儲存體 management .NET Api 的參考檔。

|  版本  | 參考文件 | 套件 |
| :-------: | ----------------------- | ------- |
| 16. x | [Microsoft.Azure.Management.Storage](/dotnet/api/microsoft.azure.management.storage) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) |

### <a name="data-movement"></a>資料移動

下表列出 Azure 儲存體資料移動 .NET Api 的參考檔。

|  版本  | 參考文件 | 套件 |
| :-------: | ----------------------- | ------- |
| 1.x | [資料移動](/dotnet/api/microsoft.azure.storage.datamovement) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) |

## <a name="java-client-libraries"></a>JAVA 用戶端程式庫

下表列出 Azure 檔案儲存體 JAVA Api 的參考和範例檔。

|  版本  | 參考文件 | 套件 | 快速入門 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [適用于 JAVA 的 Azure 檔案儲存體用戶端程式庫](/java/api/overview/azure/storage-file-share-readme) | [封裝（Maven）](https://mvnrepository.com/artifact/com.azure/azure-storage-file-share) | &nbsp; |
| 推進 | [.com. azure. 儲存檔案](/java/api/com.microsoft.azure.storage.file) | [封裝（Maven）](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) | [使用 Java 開發 Azure 檔案服務](/azure/storage/files/storage-java-how-to-use-file-storage) |

### <a name="storage-management"></a>存放管理

下表列出 Azure 儲存體管理 JAVA Api 的參考檔。

|  版本  | 參考文件 | 套件 |
| :-------: | ----------------------- | ------- |
| 0.9. x | [.com. azure 管理儲存體](/java/api/overview/azure/storage/management) | [封裝（Maven）](https://mvnrepository.com/artifact/com.microsoft.azure/azure-svc-mgmt-storage) |

## <a name="python-client-libraries"></a>Python 用戶端程式庫

下表列出 Azure 檔案儲存體 Python Api 的參考和範例檔。

|  版本  | 參考文件 | 套件 | 快速入門 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [適用于 Python 的 Azure 儲存體用戶端程式庫 v12](/azure/developer/python/sdk/storage/overview?view=storage-py-v12) | [封裝（PyPI）](https://pypi.org/project/azure-storage-file/12.0.0b4/) | [範例](/python/api/overview/azure/storage-file-share-readme#examples) |
| 2.x | [適用于 Python 的 Azure 儲存體用戶端程式庫 v2](/azure/developer/python/sdk/storage/overview?view=storage-py-v2) | [封裝（PyPI）](https://pypi.org/project/azure-storage-file/2.1.0/) | [使用 Python 開發 Azure 檔案服務](/azure/storage/files/storage-python-how-to-use-file-storage) |

## <a name="javascript-client-libraries"></a>JavaScript 用戶端程式庫

下表列出 Azure 檔案儲存體 JavaScript Api 的參考和範例檔。

|  版本  | 參考文件 | 套件 | 快速入門 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [適用于 JavaScript 的 Azure 檔案儲存體用戶端程式庫](/javascript/api/overview/azure/storage-file-share-readme) | [封裝（npm）](https://www.npmjs.com/package/@azure/storage-file-share) | [範例](/javascript/api/overview/azure/storage-file-share-readme#examples) |
| 10.x | [@azure/storage-file](/javascript/api/@azure/storage-file) | [封裝（npm）](https://www.npmjs.com/package/@azure/storage-file) | &nbsp; |

## <a name="rest-apis"></a>REST API

下表列出 Azure 檔案儲存體 REST Api 的參考和範例檔。

| 參考文件 | 概觀 |
| ----------------------- | -------- |
| [檔案服務 REST API](/rest/api/storageservices/file-service-rest-api) | [檔案服務概念](/rest/api/storageservices/file-service-concepts) |

### <a name="other-rest-reference"></a>其他 REST 參考

- [Azure 儲存體匯入匯出 REST API](/rest/api/storageimportexport/)可協助您管理匯入/匯出作業，以便在 Blob 儲存體之間傳輸資料。

## <a name="other-languages-and-platforms"></a>其他語言和平臺

下列清單包含其他程式設計語言和平臺程式庫的連結。

- [C++](https://azure.github.io/azure-storage-cpp)
- [Ruby](https://azure.github.io/azure-storage-ruby)
- [PHP](https://azure.github.io/azure-storage-php/)
- [iOS](https://azure.github.io/azure-storage-ios/)
- [Android](https://azure.github.io/azure-storage-android)

## <a name="powershell"></a>PowerShell

下表包含最新版參考內容的連結。

| 版本 | 平台 |
| ------- | -------- |
|  3.x  | [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/?view=azps-3.8.0) |
|  2.x  | [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/?view=azps-2.8.0) |

## <a name="azure-cli"></a>Azure CLI

- [Azure CLI](/cli/azure/storage)
