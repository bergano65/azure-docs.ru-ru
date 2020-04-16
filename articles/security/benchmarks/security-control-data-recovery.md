---
title: Контроль безопасности Azure - Восстановление данных
description: Восстановление данных управления безопасностью Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408589"
---
# <a name="security-control-data-recovery"></a>Контроль безопасности: Восстановление данных

Убедитесь, что все системные данные, конфигурации и секреты автоматически резервируются на регулярной основе.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Обеспечить регулярные автоматизированные резервные истектоли

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 9.1 | 10.1 | Customer |

Включите резервное копирование Azure и наконструировать источник резервного копирования (Azure VMs, S'L Server или Файл-шеринг), а также желаемый период частоты и удержания.

- [Как включить резервное копирование Azure](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Выполните полное резервное копирование системы и резервное копирование любых ключей, управляемых клиентом

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 9.2 | 10.2 | Customer |

Включите резервное копирование Azure и таргет инг VM (ы), а также желаемые периоды частоты и удержания. Клиент резервного копирования управлял ключами в Azure Key Vault.

- [Как включить резервное копирование Azure](https://docs.microsoft.com/azure/backup/)

- [Как создать резервную связь с ключами хранилища в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Проверка всех резервных ups, включая управляемые ключей клиента

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 9.3 | 10.3 | Customer |

Обеспечьте возможность периодически выполнять восстановление содержимого в azure Backup. Тест-восстановление резервных ключей, управляемых клиентом.

- [Как восстановить файлы из резервного копирования виртуальной машины Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Как восстановить ключи хранилища ключей в Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Обеспечить защиту резервных и управляемых ключей клиента

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 9.4 | 10.4 | Customer |

Для резервных копий локальных данных шифрование выполняется с использованием парольной фразы, которую вы указываете при настройке резервного копирования в Azure. Для виртуальных машин Azure шифрование выполняется с помощью функции "Шифрование службы хранилища" (SSE). Используйте элемент управления доступом на основе ролей для защиты резервных и управляемых клиентами ключей.  

Включите защиту от мягкого удаления и очистки в Key Vault, чтобы защитить ключи от случайного или вредоносного удаления.  Если Хранилище Azure используется для хранения резервных копий, включите мягкое удаление для сохранения и восстановления данных при удалении снимков кабы или кабли. 

- [Понять Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Как включить защиту от мягкого удаления и очистки в Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Soft delete for Azure Storage blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) (Обратимое удаление больших двоичных объектов службы хранилища Azure)


## <a name="next-steps"></a>Дальнейшие действия

- Смотрите следующий контроль безопасности: [реагирование на инциденты](security-control-incident-response.md)