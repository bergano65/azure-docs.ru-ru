---
title: Часто задаваемые вопросы о резервном копировании виртуальных машин Azure
description: В этой статье вы найдете ответы на часто задаваемые вопросы о резервном копировании виртуальных машин Azure с помощью службы Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: b8e259c6212e9a1e81b6b0c8825287f3025f9068
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680534"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Часто задаваемые вопросы. Резервное копирование виртуальных машин Azure

В этой статье содержатся ответы на часто задаваемые вопросы о резервном копировании виртуальных машин Azure со службой [Azure Backup](backup-introduction-to-azure-backup.md) .

## <a name="backup"></a>Архивация

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Какие образы виртуальных машин можно включить для резервного копирования при их создании?

При создании виртуальной машины можно включить резервное копирование для виртуальных машин, работающих под управлением [поддерживаемых операционных систем](backup-support-matrix-iaas.md#supported-backup-actions) .

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Стоимость резервного копирования включена в стоимость виртуальной машины?

Нет. Затраты на резервное копирование отделены от затрат на виртуальную машину. Дополнительные сведения о [ценах на Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Какие разрешения требуются для включения резервного копирования виртуальной машины?

Если вы являетесь участником виртуальной машины, вы можете включить резервное копирование на виртуальной машине. Если вы используете настраиваемую роль, вам потребуются следующие разрешения для включения резервного копирования на виртуальной машине:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Если хранилище служб восстановления и виртуальная машина имеют разные группы ресурсов, убедитесь, что у вас есть разрешения на запись в группу ресурсов для хранилища служб восстановления.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Применяется ли к заданию резервного копирования по запросу то же расписание сохранения, что и к плановым заданиям резервного копирования?

Нет. Укажите диапазон хранения для задания резервного копирования по запросу. По умолчанию задание будет храниться в течение 30 дней после активации на портале.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Недавно на некоторых виртуальных машинах было включено шифрование дисков Azure. Будет ли после этого работать резервное копирование?

Предоставьте разрешения Azure Backup для доступа к Key Vault. Укажите разрешения в PowerShell, как описано в разделе **Включение резервного копирования** в документации [Azure Backup PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Диски виртуальной машины перенесены на управляемые диски. Будет ли после этого работать резервное копирование?

Да, резервные копии будут работать как и прежде. Не нужно ничего перенастраивать.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Почему моя виртуальная машина не отображается в мастере настройки резервного копирования?

В мастере отображаются только виртуальные машины в том же регионе, что и хранилище, и те, для которых еще не настроено резервное копирование.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Моя виртуальная машина завершила работу. Будет ли выполняться запланированное резервное копирование или резервное копирование по умолчанию?

Да. Резервные копии создаются при завершении работы машины. Точка восстановления отмечена как отказоустойчивая.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Можно ли отменить выполняющееся задание резервного копирования?

Да. Задание резервного копирования можно отменить, если оно находится в состоянии **Создание моментального снимка**. Невозможно отменить задание, если выполняется передача данных из моментального снимка.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Я включил блокировку для группы ресурсов, созданной службой Azure Backup (т. е. `AzureBackupRG_<geo>_<number>`), будут ли мои резервные копии продолжать работать?

Если заблокировать группу ресурсов, созданную службой Azure Backup, резервное копирование начнется сбоем, так как превышено максимальное ограничение в 18 точек восстановления.

Пользователю необходимо снять блокировку и очистить коллекцию точек восстановления из этой группы ресурсов, чтобы сделать резервные копии в будущем успешными, и [выполнить следующие действия](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) , чтобы удалить коллекцию точек восстановления.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Поддерживает ли служба архивации Azure стандартные диски, управляемые SSD?

Да, Azure Backup поддерживает [стандартные управляемые диски SSD](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Можно ли создавать резервную копию виртуальной машины с диском с поддержкой ускорителя записи?

Невозможно сделать моментальный снимок диска с поддержкой ускорителя записи. Но служба Azure Backup может исключить диск с поддержкой ускорителя записи из резервной копии.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>У меня есть виртуальная машина с дисками с ускорителем записи и установленным SAP HANA. Как выполнить резервное копирование?

Служба Azure Backup не может создавать резервные копии диска с поддержкой ускорителя записи, но его можно исключить из резервной копии. Тем не менее резервная копия не сможет обеспечить согласованность базы данных, так как данные на диске с поддержкой ускорителя записи не копируются. Вы можете создавать резервные копии дисков в этой конфигурации, если вам нужны резервные копии дисков операционной системы и дисков без ускорителя записи.

Мы используем закрытую предварительную версию для SAP HANA резервного копирования с RPO, равной 15 минутам. Она похожа на резервное копирование базы данных SQL и использует интерфейс backInt для сторонних решений, сертифицированных SAP HANA. Если вы заинтересованы, напишите нам по адресу `AskAzureBackupTeam@microsoft.com` с темой **Регистрация для использования закрытой предварительной версии для резервного копирования SAP HANA на виртуальных машинах Azure**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Какова максимальная задержка в момент начала резервного копирования с запланированного времени архивации, установленного в политике резервного копирования виртуальной машины?

Запланированное резервное копирование будет запущено в течение 2 часов запланированного времени архивации. Например, если время начала резервного копирования виртуальных машин 100 запланировано на 2:00 AM, то по максимальному 4:00 на всех виртуальных машинах 100 будет выполняться задание резервного копирования. Если запланированные резервные копии приостановлены из-за сбоя и возобновления или повторной попытки, резервное копирование может начаться за пределами этого интервала в два часа.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Каков минимальный допустимый диапазон хранения для ежедневной точки резервного копирования?

Политика архивации виртуальных машин Azure поддерживает минимальный диапазон хранения в семь дней до 9999 дней. Любое изменение существующей политики резервного копирования виртуальной машины с менее чем семь дней потребует обновления в соответствии с минимальным диапазоном хранения, равным семи дням.

## <a name="restore"></a>Восстановление

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Как решить, нужно восстановить только диски или всю виртуальную машину?

Восстановление виртуальной машины — это быстрый способ создать виртуальную машину Azure. Этот параметр изменяет имена дисков, контейнеры, используемые дисками, общедоступные IP-адреса и имена сетевых интерфейсов. Это изменение сохраняет уникальные ресурсы при создании виртуальной машины. Виртуальная машина не добавляется в группу доступности.

Используйте восстановление диска, если нужно сделать следующее:

- Настроить создаваемую виртуальную машину. Например, измените размер.
- Добавьте параметры конфигурации, которые не были на момент резервного копирования.
- Управлять соглашением об именовании для создающихся ресурсов.
- Добавить виртуальную машину в группу доступности.
- Добавить другие параметры, которые должны быть настроены с помощью PowerShell или шаблона.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Можно ли восстановить резервные копии неуправляемых дисков виртуальной машины после обновления до управляемых дисков?

Да, можно использовать резервные копии, созданные до преобразования дисков из неуправляемых в управляемые.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Как восстановить виртуальную машину до точки восстановления до переноса виртуальной машины на управляемые диски?

Процесс восстановления остается прежним. Если точка восстановления находится на момент времени, когда виртуальная машина содержит неуправляемые диски, [диски можно восстановить как неуправляемые](tutorial-restore-disk.md#unmanaged-disks-restore). Если виртуальная машина применяет управляемые диски, можно [восстановить диски как управляемые диски](tutorial-restore-disk.md#managed-disk-restore). Затем можно [создать виртуальную машину на основе этих дисков](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Узнайте больше](backup-azure-vms-automation.md#restore-an-azure-vm) о том, как сделать это в PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Можно ли восстановить виртуальную машину, которая была удалена?

Да. Даже если вы удалите виртуальную машину, вы можете перейти к соответствующей резервной копии в хранилище и восстановить ее из точки восстановления.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Как восстановить виртуальную машину в той же группе доступности?

Для виртуальной машины Azure с управляемыми дисками восстановление в группе доступности обеспечивается с помощью шаблона при восстановлении в качестве управляемых дисков. Этот шаблон имеет входной параметр **Группы доступности**.

### <a name="how-do-we-get-faster-restore-performances"></a>Как увеличить производительность восстановления?

Возможность [мгновенного восстановления](backup-instant-restore-capability.md) помогает ускорить резервное копирование и мгновенное восстановление из моментальных снимков.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Что происходит при изменении параметров хранилища ключей для зашифрованной виртуальной машины?

После изменения параметров KeyVault для зашифрованной виртуальной машины резервные копии будут продолжать работать с новым набором сведений. Однако после восстановления из точки восстановления до изменения потребуется восстановить секреты в KeyVault, прежде чем можно будет создать виртуальную машину из нее. Дополнительные сведения см. в этой [статье](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret) .

Операции, такие как закрытый секрет или ключ, не нуждаются в этом шаге, и один и тот же KeyVault можно использовать после восстановления.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Можно ли получить доступ к виртуальной машине после восстановления из-за неработающего отношения виртуальной машины с контроллером домена?

Да, вы получите доступ к виртуальной машине после восстановления из-за неработающего отношения виртуальной машины с контроллером домена. Дополнительные сведения см. в этой [статье](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) .

## <a name="manage-vm-backups"></a>Управление резервным копированием виртуальных машин

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Что произойдет, если изменить политику резервного копирования?

Резервная копия виртуальной машины создается в соответствии с параметрами расписания и хранения в измененной или новой политике.

- Если период хранения продлевается, существующие точки восстановления отмечаются для хранения в соответствии с новой политикой.
- Если период хранения сокращается, точки восстановления отмечаются для удаления (и удаляются) при следующем задании очистки.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Как переместить виртуальную машину, резервные копии которой создаются службой Azure Backup, в другую группу ресурсов?

1. Временно остановите резервное копирование и сохраните резервные копии данных.
2. Переместите виртуальную машину в целевую группу ресурсов.
3. Повторное включение резервного копирования в том же или новом хранилище.

Вы можете восстанавливать виртуальную машину из доступных точек восстановления, созданных до операции перемещения.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Существует ли ограничение на количество виртуальных машин, которые могут быть связаны с одной и той же политикой резервного копирования?

Да, существует ограничение в 100 виртуальных машин, которые можно связать с одной и той же политикой резервного копирования на портале. Рекомендуется использовать более 100 виртуальных машин, создав несколько политик архивации с одним и тем же расписанием или с другим расписанием.
