---
title: HB 系列
description: HB 系列 Vm 的規格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: b0ffd9af8df830c1bdacbfe3809d4086346679b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678557"
---
# <a name="hb-series"></a>HB 系列

HB 系列 Vm 已針對記憶體頻寬所驅動的應用程式優化，例如流體 dynamics、明確有限元素分析和氣象模型。 HB Vm 功能 60 AMD EPYC 7551 處理器核心，每個 CPU 核心 4 GB RAM，且不會同時進行多執行緒處理。 HB VM 最多可提供每秒 260 GB 的記憶體頻寬。

ACU：199-216

進階儲存體：支援

進階儲存體快取：支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基本 CPU 頻率（GHz） | 所有核心頻率（GHz、尖峰） | 單核心頻率（GHz、尖峰） | RDMA 效能（Gb/s） | MPI 支援 | 暫存儲存體（GB） | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | 全部 | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。