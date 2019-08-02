---
title: 'Azure Backup Server и DPM: вопросы и ответы'
description: Ответы на часто задаваемые вопросы о Azure Backup Server и DPM.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 16cf4bcb6d83d22417d83d0b20ee564431f82840
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689344"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server и DPM — часто задаваемые вопросы
В этой статье содержатся ответы на часто задаваемые вопросы о Azure Backup Server и DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Можно ли использовать Azure Backup Server, чтобы создать резервную копию для восстановления исходного состояния физического сервера? <br/>
Да.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Могу ли я зарегистрировать сервер в нескольких хранилищах?
Нет. Сервер DPM или Azure Backup можно зарегистрировать только в одном хранилище.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Можно ли использовать DPM для резервного копирования приложений в Azure Stack?
Нет. Вы можете использовать службу Azure Backup для защиты Azure Stack, но сейчас Azure Backup не поддерживает использование DPM для создания резервных копий приложений в Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Если мною установлен агент Azure Backup для защиты файлов и папок, могу ли я установить System Center DPM для резервного копирования локальных рабочих нагрузок в Azure?
Да. Но сначала следует настроить DPM и только потом устанавливать агент Azure Backup.  Такой порядок установки компонентов обеспечивает возможность взаимодействия между агентом Azure Backup и DPM. Установка агента до установки DPM не рекомендуется и не поддерживается.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Почему не удается добавить внешний сервер DPM после установки накопительного пакета обновления 7 (UR7) и последней версии агента Microsoft Azure Backup?
Для серверов DPM с источниками данных, которые защищены в облаке (с помощью накопительного пакета обновления более ранней версии, чем 7 (UR7)), необходимо подождать по крайней мере один день после установки пакета UR7 и последней версии агента Microsoft Azure Backup. После этого можно **добавить внешний сервер DPM**. В этот период выполняется загрузка метаданных в группы защиты DPM в Azure. Метаданные загружаются в группы защиты при первом выполнении задания, запланированного на ночное время.

## <a name="vmware-and-hyper-v-backup"></a>Резервное копирование виртуальных машин VMware и Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Можно ли выполнять резервное копирование серверов VMware vCenter в облако Azure?
Да. Для резервного копирования VMware vCenter Server и узлов ESXi в облако Azure можно использовать Azure Backup Server.

- См. дополнительные сведения о [поддерживаемых версиях](backup-mabs-protection-matrix.md).
- См. [инструкции по резервному копированию сервера VMware](backup-azure-backup-server-vmware.md).

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Требуется ли отдельная лицензия для восстановления всего локального кластера VMware или Hyper-V?
Для защиты VMware или Hyper-V отдельная лицензия не требуется.

- Если вы являетесь клиентом System Center, для защиты виртуальных машин VMware используйте System Center Data Protection Manager (DPM).
- Если вы не пользуетесь System Center, виртуальные машины VMware можно защитить с помощью Azure Backup Server (с оплатой по мере использования).


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Можно ли восстановить элемент SharePoint в исходное расположение, если сайт SharePoint настроен с использованием SQL AlwaysOn (с защитой на диске)?
Да, элемент можно восстановить на исходный сайт SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Можно ли восстановить базу данных SharePoint в исходное расположение, если сайт SharePoint настроен с использованием SQL AlwaysOn?
Так как базы данных SharePoint настраиваются в SQL AlwaysOn, их нельзя изменить, не удалив группу доступности. В связи с этим DPM не может восстанавливать базы данных в исходное расположение. Вы можете восстановить базу данных SQL Server в другой экземпляр SQL Server.

## <a name="next-steps"></a>Следующие шаги

См. другие статьи с вопросами и ответами:

- Дополнительные [сведения](backup-support-matrix-mabs-dpm.md) о Azure Backup Server и матрице поддержки DPM.
- Дополнительные [сведения](backup-azure-mabs-troubleshoot.md) о Azure Backup Server и рекомендациях по устранению неполадок DPM.
