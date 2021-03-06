---
title: 從相同訂用帳戶的儲存體帳戶的 VHD 檔案建立受控磁碟 - CLI 範例
description: Azure CLI 指令碼範例 - 從相同訂用帳戶的儲存體帳戶的 VHD 檔案建立受控磁碟
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: b0ce935e03a6202ac444987cbecf853ee6717d3b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459513"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>使用 CLI 從相同訂用帳戶的儲存體帳戶的 VHD 檔案建立受控磁碟

此指令碼會從相同訂用帳戶的儲存體帳戶的 VHD 檔案建立受控磁碟。 使用此指令碼可將專用 (非一般化/已執行過 Sysprep) 的 VHD 匯入至受控 OS 磁碟，以建立虛擬機器。 或者，用它將資料 VHD 匯入到受控資料磁碟。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令從 VHD 建立受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | 在相同的訂用帳戶中使用儲存體帳戶的 VHD URI 受控磁碟 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Windows VM 文件](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器和受控磁碟 CLI 指令碼範例。
