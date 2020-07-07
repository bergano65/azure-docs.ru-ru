---
title: Управление безопасностью Azure — восстановление данных
description: Восстановление данных управления безопасностью в Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408589"
---
# <a name="security-control-data-recovery"></a>Управление безопасностью: восстановление данных

Убедитесь, что все системные данные, конфигурации и секреты автоматически архивируются на регулярной основе.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1. Обеспечение регулярного автоматического резервного копирования

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 9.1 | 10.1 | Customer |

Включите Azure Backup и настройте источник резервного копирования (виртуальные машины Azure, SQL Server или файловые ресурсы), а также нужную частоту и срок хранения.

- [Включение Azure Backup](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Выполнение полного резервного копирования системы и любых ключей, управляемых клиентом

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 9.2 | 10.2 | Customer |

Включите Azure Backup и целевые виртуальные машины, а также желаемую частоту и сроки хранения. Резервное копирование управляемых пользователем ключей в Azure Key Vault.

- [Включение Azure Backup](https://docs.microsoft.com/azure/backup/)

- [Как создать резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3. Проверка всех резервных копий, включая управляемые клиентом ключи

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 9.3 | 10.3 | Customer |

Обеспечение возможности периодически выполнять восстановление данных в Azure Backup. Проверка восстановления резервных копий ключей, управляемых клиентом.

- [Как восстановить файлы из резервной копии виртуальной машины Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Как восстановить резервную копию ключей хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Обеспечение защиты резервных копий и управляемых клиентом ключей

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 9.4 | 10,4 | Customer |

Для резервных копий локальных данных шифрование выполняется с использованием парольной фразы, которую вы указываете при настройке резервного копирования в Azure. Для виртуальных машин Azure шифрование выполняется с помощью функции "Шифрование службы хранилища" (SSE). Используйте управление доступом на основе ролей для защиты резервных копий и управляемых клиентом ключей.  

Включите обратимое удаление и очистку защиты в Key Vault, чтобы защитить ключи от случайного или вредоносного удаления.  Если хранилище Azure используется для хранения резервных копий, включите обратимое удаление, чтобы сохранять и восстанавливать данные при удалении больших двоичных объектов или моментальных снимков больших двоичных объектов. 

- [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Включение обратимого удаления и очистки защиты в Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Soft delete for Azure Storage blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) (Обратимое удаление больших двоичных объектов службы хранилища Azure)


## <a name="next-steps"></a>Дальнейшие действия

- См. следующий контроль безопасности: [реагирование на инциденты](security-control-incident-response.md)