---
title: Azure VM 大小-計算優化 |Microsoft Docs
description: 列出 Azure 中適用于虛擬機器的不同計算優化大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: b945348f6fabfa698e45c111241fbfcef088d45e
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058073"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>計算最佳化的虛擬機器大小

計算優化 VM 大小具有高 CPU 對記憶體比例。 這些大小適用于中流量 web 伺服器、網路設備、批次處理，以及應用程式伺服器。 本文提供個 vcpu 數目、資料磁片和 Nic 的相關資訊。 其中也包含此群組中每個大小的儲存體輸送量和網路頻寬的相關資訊。

[Fsv2 系列](fsv2-series.md)會在第2代 Intel®，®白金8272CL （Cascade Lake）處理器和 Intel®的®白金8168（Skylake）處理器上執行。 它的特色是將所有核心 Turbo 主頻的速度維持在 3.4 GHz，而最大單一核心 turbo 頻率為 3.7 g h z。 Intel® AVX-512 指示是 Intel 可擴充的處理器上的新技術。 這些指示可為單一和雙精確度浮點運算上的向量處理工作負載，提供最多2倍的效能提升。 換句話說，對於任何計算工作負載來說，這些都是非常快速的。

Fsv2 系列的每小時訂價較低，在 Azure 產品組合中，就每一 vCPU 的「Azure 計算單位」(ACU) 而言，具有最佳的價格/性能比表現。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
