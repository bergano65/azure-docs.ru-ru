---
title: Тестирование безопасности Azure v2 — резервное копирование и восстановление
description: Резервное копирование и восстановление для системы безопасности Azure версии 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b2e54545fb79120a3f9d66067da267df3b151b3f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322127"
---
# <a name="security-control-v2-backup-and-recovery"></a>Управление безопасностью версии 2: резервное копирование и восстановление

Резервное копирование и восстановление охватывает элементы управления, чтобы обеспечить выполнение, проверку и защиту резервных копий данных и конфигурации на разных уровнях служб.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: обеспечение регулярного автоматического резервного копирования

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Убедитесь, что вы создаете резервные копии систем и данных для поддержания непрерывности бизнес-процессов после непредвиденного события. Это должно быть определено любыми целями для цели точки восстановления (RPO) и целевого времени восстановления (RTO).

Включите Azure Backup и настройте источник резервного копирования (например, виртуальные машины Azure, SQL Server, базы данных HANA или файловые ресурсы), а также нужную частоту и срок хранения.  

Для более высокого уровня защиты можно включить геоизбыточное хранилище для репликации данных резервных копий в дополнительный регион и восстановить их с помощью операции восстановления между регионами.

- [Непрерывность бизнес-процессов и аварийное восстановление в масштабах предприятия](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Включение Azure Backup](/azure/backup/)

- [Включение восстановления между регионами](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Политика и стандарты](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Подготовка инцидентов](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: шифрование данных резервных копий

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Убедитесь, что резервные копии защищены от атак. Это должно быть шифрование резервных копий для защиты от потери конфиденциальности.   

Для локальных резервных копий, использующих Azure Backup, шифрование неактивных данных предоставляется с помощью предоставленной парольной фразы. Для обычных резервных копий служб Azure данные резервного копирования автоматически шифруются с помощью ключей, управляемых платформой Azure. Вы можете выбрать шифрование резервных копий с помощью управляемого клиентом ключа. В этом случае убедитесь, что этот ключ, управляемый клиентом, в хранилище ключей также находится в области резервного копирования. 

Используйте управление доступом на основе ролей в Azure Backup, Azure Key Vault или других ресурсах для защиты резервных копий и управляемых клиентом ключей. Кроме того, можно включить дополнительные функции безопасности, чтобы требовать MFA, прежде чем можно будет изменить или удалить резервные копии.

- [Общие сведения о средствах безопасности в Azure Backup](/azure/backup/security-overview)

- [Шифрование данных резервных копий с помощью управляемых клиентом ключей](/azure/backup/encryption-at-rest-with-cmk) 

- [Резервное копирование ключей Key Vault в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [Функции безопасности, помогающие защитить гибридные резервные копии от атак](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Подготовка инцидентов](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3. Проверка всех резервных копий, включая ключи, управляемые клиентом

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| BR-3 | 10.3 | CP-4, CP-9 |

Периодически осуществляйте восстановление данных из резервной копии. Убедитесь, что вы можете восстановить резервные копии ключей, управляемых клиентом.

- [Как восстановить файлы из резервной копии виртуальной машины Azure](/azure/backup/backup-azure-restore-files-from-vm)

- [Восстановление ключей Key Vault в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Подготовка инцидентов](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: снижение риска потери ключей

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

Убедитесь, что у вас есть меры по предотвращению и восстановлению после потери ключей. Включите обратимое удаление и очистку защиты в Azure Key Vault, чтобы защитить ключи от случайного или вредоносного удаления.  

- [Включение обратимого удаления и очистки защиты в Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Подготовка инцидентов](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Безопасность данных](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

