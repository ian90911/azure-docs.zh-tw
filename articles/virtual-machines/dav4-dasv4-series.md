---
title: Dav4 和 Dasv4 系列
description: Dav4 和 Dasv4 系列 Vm 的規格。
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 5d8478d97ba2615836f3e121d9dd56d94fcd73fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678387"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 和 Dasv4 系列

Dav4 系列和 Dasv4 系列是在多執行緒設定中使用 AMD 的2.35 版 Ghz EPYC<sup>TM</sup> 7452 處理器的新大小，最多有 256 MB 的 l3 快取，每8個核心可增加客戶選項，以執行其一般用途的工作負載。 Dav4 系列和 Dasv4 系列具有與 D 和 Dsv3 系列相同的記憶體和磁碟設定。

## <a name="dav4-series"></a>Dav4 系列

ACU：230-260

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：支援

記憶體保留更新：支援

Dav4 系列大小以2.35 版 Ghz AMD EPYC<sup>TM</sup> 7452 處理器為基礎，可達到最高的 3.35 ghz 的提升頻率。 Dav4 系列大小提供 vCPU、記憶體和暫存儲存體的組合，可用於大部分的生產環境工作負載。 資料磁碟儲存體與虛擬機器分開計費。 若要使用 premium SSD，請使用 Dasv4 大小。 Dasv4 大小的定價和計費方式與 Dav4 系列相同。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大 NIC/預期的網路頻寬 (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000/1000/500 | 8 / 24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000/1000/500 | 8 / 30000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000/1000/500 | 8 / 30000 |

## <a name="dasv4-series"></a>Dasv4 系列

ACU：230-260

進階儲存體：支援

進階儲存體快取：支援

即時移轉：支援

記憶體保留更新：支援

Dasv4 系列大小以2.35 版 Ghz AMD EPYC<sup>TM</sup> 7452 處理器為基礎，可達成 3.35 ghz 的提升頻率，並使用 premium SSD。 Dasv4 系列大小提供 vCPU、記憶體和暫存儲存體的組合，可用於大部分的生產環境工作負載。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 （50）|3200/48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000/64 （100）|6400/96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000/128 （200）|12800/192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 （400）|25600/384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 （800）|51200/768|8 / 16000 |
| Standard_D48as_v4|48|192|384|32|96000/1020 （1200）|76800/1148|8 / 24000 |
| Standard_D64as_v4|64|256|512|32|128000/1020 （1600）|80000/1200|8 / 30000 | 
| Standard_D96as_v4|96|384|768|32|192000/1020 （2400）|80000/1200|8 / 30000 |

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
