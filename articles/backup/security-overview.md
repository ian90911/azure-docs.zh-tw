---
title: 安全性功能概觀
description: 了解 Azure 備份中的安全性功能，協助保護您的備份資料，並符合您企業的安全性需求。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: ce6d8a43b48be5189f0459c9f82c69354f40689f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513196"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure 備份中的安全性功能概觀

保護您的資料最重要的步驟之一，就是擁有可靠的備份基礎結構。 但請務必確保以安全的方式備份資料，而且您的備份會隨時受到保護。 無論是傳輸中的資料還是待用資料，Azure 備份皆可為您的備份環境提供安全的保障。 本文列出 Azure 備份中的安全性功能，協助保護您的備份資料，並符合您企業的安全性需求。

## <a name="management-and-control-of-identity-and-user-access"></a>管理與控制身分識別和使用者存取

復原服務保存庫所使用的儲存體帳戶是隔離的，而且使用者不得出於任何惡意用途而進行存取。 僅允許透過 Azure 備份管理作業 (例如，還原) 進行存取。 Azure 備份可讓您使用 [Azure 角色型存取控制 (RBAC)](./backup-rbac-rs-vault.md)，透過細部存取來控制受控作業。 RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。

Azure 備份提供三種用來控制備份管理作業的[內建角色](../role-based-access-control/built-in-roles.md)：

* 備份參與者 - 可建立和管理備份，但刪除復原服務保存庫和賦予他人存取權除外
* 備份操作員 - 可執行參與者有權執行的所有動作，但移除備份和管理備份原則除外
* 備份讀取者 - 有權檢視所有備份管理作業

深入了解[使用角色型存取控制來管理 Azure 備份](./backup-rbac-rs-vault.md)。

Azure 備份有數個服務內建的安全性控制項，可防止、偵測及回應安全性弱點。 深入了解 [Azure 備份的安全性控制項](./backup-security-controls.md)。

## <a name="separation-between-guest-and-azure-storage"></a>來賓與 Azure 儲存體之間的分隔

Azure 備份包括虛擬機器備份和虛擬機器備份中的 SQL 和 SAP HANA，備份資料存放在 Azure 儲存體中，來賓無法直接存取備份儲存體或其內容。  使用虛擬機器備份，備份快照的建立和儲存是由 Azure 網狀架構完成，在此流程中，來賓除了停止應用程式一致備份的工作負載以外，未參與任何動作。  使用 SQL 和 SAP HANA，備份擴充功能可暫時存取以寫入特定的 Blob。  如此一來，即使是在受影響的環境中，來賓也無法竄改或刪除現有備份。

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM 備份不需要網際網路連線

備份 Azure VM 需要將資料從虛擬機器磁碟移至復原服務保存庫。 不過，所有必要的通訊和資料傳輸只會發生在 Azure 骨幹網路上，而不需要存取您的虛擬網路。 因此，放置在安全網路中的 Azure VM 備份不會要求您允許存取任何 IP 或 FQDN。

## <a name="private-endpoints-for-azure-backup"></a>Azure 備份的私人端點

您現在可以使用[私人端點](../private-link/private-endpoint-overview.md)，從虛擬網路內部的伺服器將資料安全地備份到復原服務保存庫。 私人端點會針對您的保存庫使用 VNET 位址空間中的 IP，因此，您不需要向任何公用 IP 公開虛擬網路。 私人端點可用於備份和還原在您的 Azure VM 中執行的 SQL 和 SAP Hana 資料庫， 也可以用於使用 MARS 代理程式的內部部署伺服器。

請在[這裡](./private-endpoints.md)詳讀 Azure 備份的私人端點詳細資料。

## <a name="encryption-of-data-in-transit-and-at-rest"></a>加密傳輸中資料和待用資料

加密可保護您的資料安全，並協助您符合組織安全性和合規性承諾。 在 Azure 中，會透過 HTTPS 保護 Azure 儲存體與保存庫之間的傳輸中資料。 此資料會保持在 Azure 中樞網路上。

* 備份資料會使用 Microsoft 受控金鑰自動加密。 您也可以使用存放在 Azure Key Vault 中的[客戶受控金鑰](backup-encryption.md#encryption-of-backup-data-using-customer-managed-keys)，在復原服務保存庫中加密您的備份受控磁碟 VM。 您不需要採取任何明確的動作，即可啟用此加密。 其適用於備份到復原服務保存庫的所有工作負載。

* Azure 備份支援備份和還原已使用 Azure 磁碟加密 (ADE) 加密其作業系統/資料磁碟的 Azure VM。 [深入了解加密的 Azure VM 和 Azure 備份](./backup-azure-vms-encryption.md)。

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>保護不小心刪除的備份資料

Azure 備份提供安全性功能，即使在刪除之後，也能協助保護備份資料。 使用虛刪除，如果使用者刪除了虛擬機器的備份，備份資料會額外保留 14 天，以便在沒有遺失資料的情況下復原該備份項目。 在「虛刪除」狀態中，備份資料額外保留 14 天不會對客戶產生任何費用。 [深入了解虛刪除](backup-azure-security-feature-cloud.md)。

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>可疑活動的監視與警示

Azure 備份提供[內建的監視和警示功能](./backup-azure-monitoring-built-in-monitor.md)，以檢視和設定與 Azure 備份相關的事件動作。 [備份報告](./configure-reports.md)提供一站式目的地服務，以供追蹤使用量、稽核備份與還原，以及找出不同細微性層級的主要趨勢。 使用 Azure 備份的監視和報告工具，可在發生任何未經授權、可疑或惡意的活動時，盡快向您發出警示。

## <a name="security-features-to-help-protect-hybrid-backups"></a>安全性功能有助於保護混合式備份

Azure 備份服務使用 Microsoft Azure 復原服務 (MARS) 代理程式，將檔案、資料夾，以及磁碟區或系統狀態，從內部部署電腦備份與還原至 Azure。 MARS 現在提供安全性功能，有助於保護混合式備份。 這些功能包括：

* 每當執行重要作業 (如變更複雜密碼) 時，就會多一道驗證。 這項驗證用來確保只有具備有效 Azure 認證的使用者，才能執行這類作業。 [深入了解防止攻擊的功能](./backup-azure-security-feature.md#prevent-attacks)。

* 已刪除的備份資料會額外保留 14 天 (從刪除日期算起)。 這可確保指定時間週期內的資料復原能力，所以即使發生攻擊，也不會遺失任何資料。 而且會維護更多的復原點，以防範資料損毀。 [深入了解復原已刪除的備份資料](./backup-azure-security-feature.md#recover-deleted-backup-data)。

* 對於使用 Microsoft Azure 復原服務 (MARS) 代理程式備份的資料，在上傳至 Azure 備份之前，會使用複雜密碼來確保已加密資料，而且只有在從 Azure 備份下載之後才會解密。 複雜密碼詳細資料僅適用於建立複雜密碼的使用者，以及使用複雜密碼設定的客服專員。 不會使用服務傳輸或共用任何內容。 這可確保資料的完整安全性，因為不慎公開的任何資料 (例如網路上的中間人攻擊) 如果沒有複雜密碼，即無法使用，而且不會在網路上傳送複雜密碼。

## <a name="compliance-with-standardized-security-requirements"></a>符合標準化的安全性需求

為協助組織符合控管個人資料收集和使用的國家、區域和業界特定需求的標準，Microsoft Azure 與 Azure 備份提供一套完整的認證和證明。 [請參閱合規性認證的清單](compliance-offerings.md)

## <a name="next-steps"></a>後續步驟

* [利用安全性功能協助保護使用 Azure 備份的雲端工作負載](backup-azure-security-feature-cloud.md)
* [利用安全性功能協助保護使用 Azure 備份的混合式備份](backup-azure-security-feature.md)
