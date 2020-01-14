---
title: Управление безопасностью Azure — восстановление данных
description: Восстановление данных управления безопасностью
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934491"
---
# <a name="security-control-data-recovery"></a>Управление безопасностью: восстановление данных

Убедитесь, что все системные данные, конфигурации и секреты автоматически архивируются на регулярной основе.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярную автоматическую архивацию

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 9.1 | 10.1 | Customer |

Включите Azure Backup и настройте источник резервного копирования (виртуальные машины Azure, SQL Server или файловые ресурсы), а также нужную частоту и срок хранения.

Как включить Azure Backup:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом.

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 9.2 | 10.2 | Customer |

Включите Azure Backup и целевые виртуальные машины, а также желаемую частоту и сроки хранения. Резервное копирование управляемых пользователем ключей в Azure Key Vault.

Как включить Azure Backup:

https://docs.microsoft.com/azure/backup/

Как создать резервную копию ключей хранилища ключей в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 9.3 | 10.3 | Customer |

Обеспечение возможности периодически выполнять восстановление данных в Azure Backup. При необходимости протестируйте восстановление в изолированную виртуальную ЛС. Проверка восстановления резервных копий ключей, управляемых клиентом.

Как восстановить файлы из резервной копии виртуальной машины Azure:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Как восстановить ключи хранилища ключей в Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и управляемых клиентом ключей

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 9.4 | 10,4 | Customer |

Для резервных копий локальных данных шифрование выполняется с использованием парольной фразы, которую вы указываете при настройке резервного копирования в Azure. Для виртуальных машин Azure шифрование выполняется с помощью функции "Шифрование службы хранилища" (SSE). Обратимое удаление можно включить в Key Vault, чтобы защитить ключи от случайного или вредоносного удаления.

Включение обратимого удаления в Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Дальнейшие действия

См. следующий контроль безопасности: [реагирование на инциденты](security-control-incident-response.md)
