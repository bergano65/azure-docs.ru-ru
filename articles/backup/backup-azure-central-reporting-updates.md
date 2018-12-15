---
title: Обновление пакета содержимого централизованных отчетов службы Azure Backup
description: Сведения об обновлениях к пакету содержимого службы Azure Backup в Power BI
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan
ms.openlocfilehash: 65ab497b2467846a324d10f8487ab384a01441ee
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968891"
---
# <a name="update-the-azure-backup-central-reporting-content-pack"></a>Обновление пакета содержимого централизованных отчетов службы Azure Backup 

[Пакет содержимого службы Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) можно использовать для просмотра отчетов о централизованном резервном копировании. Пакет содержимого регулярно обновляется для добавления дополнительных функций, а также для исправления ошибок. Из этой статьи можно узнать, как выполнить обновление пакета содержимого. Здесь также приведены сведения о том, как отложить обновление и просмотреть обновления, сделанные с течением времени.

## <a name="get-updates-to-the-content-pack"></a>Получение обновлений к пакету содержимого

### <a name="get-the-updated-content-pack"></a>Получение обновлений пакета содержимого
Если в копию пакета содержимого не были внесены изменения, она обновится автоматически. Когда в пакет содержимого будут внесены изменения, в Power BI появится уведомление, а также будет выполнена отправка уведомления по электронной почте. Обновленный пакет содержимого можно получить удобным способом. 

### <a name="postpone-the-update"></a>Изменение сроков обновления
Рекомендуется импортировать пакет содержимого в [настраиваемую рабочую область](https://youtu.be/26zyOtyHPJM?t=1m57s). Теперь отчеты можно редактировать.
Как упоминалось ранее, при изменении пакета содержимого в Power BI появится уведомление. Можно выбрать вариант с задержкой получения пакета содержимого. 

## <a name="coming-soon"></a>Скоро
   
Обновление пакета содержимого службы Azure Backup выполняется для того, чтобы поддерживать дополнительные рабочие нагрузки. В рабочие нагрузки включена архивация виртуальных машин Базы данных SQL Azure для IaaS и System Center Data Protection Manager. Данная поддержка будет добавлена для службы Azure Backup и архивных копий виртуальных машин Azure. Это значит, что просмотр и анализ всех данных архивации можно выполнять в одном централизованном месте. [Отчеты также можно настроить](https://youtu.be/26zyOtyHPJM) в соответствии с потребностями организации.

Предварительно настроенные отчеты, которые поставляются вместе с пакетом содержимого службы Azure Backup, могут быть изменены. Эти изменения делают отчеты более значимыми для рабочих нагрузок. Краткий обзор предстоящего набора отчетов доступен ниже.

### <a name="summary"></a>Сводка
   
![Сводка](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Выставление счетов

![Выставление счетов](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Соответствие нормативным требованиям

![Соответствие нормативным требованиям](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Хранилище

![Хранилище](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Архивные элементы
![Архивные элементы](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Оповещения

![Оповещения](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Задания

![Задания](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>Дополнительная информация

* Видеоролик [How to share reports using email | Azure Backup Tips and Tricks](https://youtu.be/26zyOtyHPJM) (Как обмениваться отчетами по электронной почте | Советы и хитрости службы Azure Backup)
* [Часто задаваемые вопросы о службе архивации Azure](backup-azure-backup-faq.md)
