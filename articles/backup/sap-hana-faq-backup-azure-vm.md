---
title: Часто задаваемые вопросы. Резервное копирование SAP HANA баз данных на виртуальных машинах Azure
description: В этой статье вы найдете ответы на часто задаваемые вопросы о резервном копировании SAP HANA баз данных с помощью службы Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e5684024668103ccbe13be4af3d7a9336651df77
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287844"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Часто задаваемые вопросы — резервное копирование SAP HANA баз данных на виртуальных машинах Azure

В этой статье содержатся ответы на часто задаваемые вопросы о резервном копировании баз данных SAP HANA с помощью службы Azure Backup.

## <a name="backup"></a>Azure Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Сколько полных резервных копий поддерживается в день?

Поддерживается только одна полная резервная копия в день. Нельзя создавать разностные резервные копии и полные резервные копии, активированные в тот же день.

### <a name="do-successful-backup-jobs-create-alerts"></a>Отправляются ли оповещения об успешно выполненных заданиях резервного копирования?

Нет. Для успешно выполненных заданий резервного копирования не создаются оповещения. Оповещения отправляются только в случае сбоя. Подробное поведение оповещений портала описано [здесь](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Однако если вы хотите получать оповещения даже для успешных заданий, можно использовать [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Можно ли просмотреть запланированные задания резервного копирования в меню задания резервного копирования?

В меню "Задание архивации" отображаются только задания резервного копирования с нерегламентированными правами. Для запланированных заданий используйте [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Добавляются ли автоматически будущие базы данных в резервное копирование?

Нет, в настоящее время это не поддерживается.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Что произойдет с резервным копированием, если удалить базу данных из экземпляра?

Если база данных удаляется из экземпляра SAP HANA, резервные копии базы данных по-прежнему будут пытаться. Это означает, что удаленная база данных начнет отображаться как неработоспособная в рамках **резервного копирования элементов** и все еще защищена.
Правильный способ отключения защиты этой базы данных — выполнение **отмены резервного копирования с удалением данных** в этой базе данных.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Если изменить имя базы данных после ее защиты, будет ли это поведение?

Переименованная база данных рассматривается как новая база данных. Таким образом, служба будет обрабатывать эту ситуацию так, как если бы база данных не была найдена и в ней не было ошибок резервного копирования. Переименованная база данных будет отображаться в виде новой базы данных и должна быть настроена для защиты.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Каковы предварительные требования для резервного копирования SAP HANA баз данных на виртуальной машине Azure?

Дополнительные сведения см. в разделах [Предварительные требования](tutorial-backup-sap-hana-db.md#prerequisites) и [Настройка разрешений](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Какие разрешения должны быть установлены, чтобы служба Azure могла выполнять резервное копирование SAP HANA баз данных?

При выполнении скрипта предварительной регистрации задаются необходимые разрешения, позволяющие Azure выполнять резервное копирование SAP HANA баз данных. Дополнительные сведения о разрешениях можно найти [здесь](tutorial-backup-sap-hana-db.md#setting-up-permissions).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Будут ли резервные копии работать после миграции SAP HANA с 1,0 на 2,0?

Ознакомьтесь с [этим разделом](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) в руководстве по устранению неполадок.

## <a name="restore"></a>Восстановление

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Почему не отображается система HANA, в которую нужно восстановить базу данных?

Проверьте, выполнены ли все предварительные условия для восстановления в целевом экземпляре SAP HANA. Дополнительные сведения см. [в статье предварительные требования. восстановление SAP HANA баз данных на виртуальной машине Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Почему не удается восстановить базу данных из-за ошибки восстановления БД?

При восстановлении необходимо выбрать параметр **принудительной перезаписи** .

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Почему отображается ошибка "исходная и Целевая системы для восстановления несовместимы"?

Сведения о типах восстановления, поддерживаемых в настоящее время, см. в SAP HANA Примечание [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как [выполнять резервное копирование SAP HANA баз данных](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) , работающих на виртуальных машинах Azure.
