---
title: Вопросы и ответы о резервном копировании баз данных SAP HANA на виртуальных машинах Azure
description: В этой статье можно найти ответы на распространенные вопросы о резервном копировании баз данных SAP HANA с помощью службы резервного копирования Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155398"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Часто задаваемые вопросы - Резервное копирование баз данных SAP HANA на M M Azure

В этой статье ответы на общие вопросы о резервном копировании баз данных SAP HANA с помощью службы резервного копирования Azure.

## <a name="backup"></a>Резервное копирование

### <a name="how-many-full-backups-are-supported-per-day"></a>Сколько полных резервных копирований поддерживается в день?

Мы поддерживаем только одно полное резервное копирование в день. В тот же день не может быть срабатывает дифференциальная резервная и полная резервная часть.

### <a name="do-successful-backup-jobs-create-alerts"></a>Отправляются ли оповещения об успешно выполненных заданиях резервного копирования?

Нет. Для успешно выполненных заданий резервного копирования не создаются оповещения. Оповещения отправляются только в случае сбоя. Подробное поведение для уведомлений портала задокументировано [здесь](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Однако, если вы заинтересованы в том, чтобы иметь оповещения даже для успешной работы, вы можете использовать [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Могу ли я видеть запланированные задания резервного копирования в меню резервных заданий?

В меню «Работа резервного копирования» будут отображаться только специальные задания резервного копирования. Для запланированных заданий используйте [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Добавляются ли автоматически будущие базы данных в резервное копирование?

Нет, это в настоящее время не поддерживается.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Если я удалю базу данных из экземпляра, что произойдет с резервными ожиданиями?

Если база данных удалена из экземпляра SAP HANA, резервное копирование базы данных по-прежнему пытается. Это означает, что удаленная база данных начинает отображаться как неработоспособная под **элементами резервного копирования** и по-прежнему защищена.
Правильный способ остановить защиту этой базы данных — выполнить **Stop Backup с удалением данных** в этой базе данных.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Если я изменю название базы данных после того, как она была защищена, каково будет поведение?

Переименованная база данных рассматривается как новая база данных. Таким образом, служба будет рассматривать эту ситуацию, как если бы база данных не была найдена и с отказом резервных upups. Переименованная база данных будет отображаться как новая база данных и должна быть настроена для защиты.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Каковы предпосылки для резервного копирования баз данных SAP HANA на Azure VM?

Ссылайтесь на [предпосылки](tutorial-backup-sap-hana-db.md#prerequisites) и [что делает сценарий предварительной регистрации.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Какие разрешения должны быть установлены для того, чтобы Azure мог создать резервное копирование баз данных SAP HANA?

Запуск сценария предварительной регистрации устанавливает необходимые разрешения, позволяющие Azure резервное копирование баз данных SAP HANA. Вы можете найти больше, что делает сценарий предварительной регистрации [здесь](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Будут ли работать резервные ожидания после миграции SAP HANA с 1,0 до 2,0?

Обратитесь к [этому разделу](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) руководства по устранению неполадок.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Можно ли настроить резервное копирование Azure HANA на виртуальный IP(баланс- инейвер нагрузки), а не на виртуальную машину?

В настоящее время у нас нет возможности настроить решение только против виртуального IP- иС. Нам нужна виртуальная машина для выполнения решения.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>У меня есть репликация системы SAP HANA (HSR), как настроить резервную копию для этой установки?

Первичные и вторичные узлы ВСР будут рассматриваться как два индивидуальных, не связанных с ВМ. Вам нужно настроить резервную перепорку на основной узла, и при сбою происходит, вам нужно настроить резервную часть на вторичном узлах (который теперь становится основным узла). Автоматического "провала" резервного копирования в другой узла не существует.

## <a name="restore"></a>Восстановить

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Почему я не вижу систему HANA, в которой я хочу восстановить свою базу данных?

Проверьте, выполнены ли все предпосылки для восстановления целевого экземпляра SAP HANA. Для получения дополнительной [информации см. Prerequisites - Восстановление баз данных SAP HANA в Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Почему восстановление Overwrite DB сбой для моей базы данных?

Убедитесь, что при восстановлении выбирается параметр **Перезаписи Силы.**

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Почему я вижу ошибку "Источник и целевые системы для восстановления несовместимы"?

Обратитесь к SAP HANA Note [1642148,](https://launchpad.support.sap.com/#/notes/1642148) чтобы узнать, какие типы восстановления в настоящее время поддерживаются.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создать резервную связь с [базами данных SAP HANA, работающими](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) на M M Azure.
