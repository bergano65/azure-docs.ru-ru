---
title: Заметки о выпуске Microsoft Azure Backup Server версии 3
description: В этой статье представлены сведения об известных проблемах и их решениях для MABS версии 3.
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 39f91838a3b5ae360c47d38b22f63e6d94e8850f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655767"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Заметки о выпуске Microsoft Azure Backup Server
В этой статье представлены известные проблемы и их решения для Microsoft Azure Backup Server (MABS) версии 3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Сбои резервного копирования и восстановления для кластеризованных рабочих нагрузок

**Описание.** Происходит сбой резервного копирования и восстановления для кластеризованных источников данных, например, таких как кластер Hyper-V, SQL (SQL Always On) или Exchange в группе доступности базы данных (DAG), после обновления с MABS версии 2 до MABS версии 3.

**Обходной путь.** Чтобы избежать этого, откройте SQL Server Management Studio (SSMS) и выполните следующий скрипт SQL для базы данных DPM:


    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Сбои при обновлении до MABS версии 3 в русском языковом стандарте

**Описание.** Обновление с MABS версии 2 до MABS версии 3 в русском языковом стандарте завершается сбоем с кодом ошибки **4387**.

**Обходной путь.** Выполните следующие действия для обновления до MABS версии 3 с помощью русского пакета установки:

1.  [Создайте резервную копию](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) базы данных SQL и удалите MABS версии 2 (выберите сохранение защищенных данных во время удаления).
2.  Обновитесь до SQL 2017 (Enterprise) и удалите отчеты в рамках обновления.
3. [Установите](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4.  [Установите](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms#download-ssms-181) SQL Server Management Studio (SSMS).
5.  Настройте отчетность с использованием параметров, как описано в разделе о [настройке SSRS с помощью SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Установите](backup-azure-microsoft-azure-backup.md) MABS версии 3.
7. [Восстановите](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL с помощью SSMS и запустите средство DPM-Sync, как описано [здесь](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Обновите свойство DataBaseVersion в таблице dbo.tbl_DLS_GlobalSetting, используя следующую команду:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Запустите службу MSDPM.


## <a name="next-steps"></a>Следующие шаги

[Новые возможности в Microsoft Azure Backup Server](backup-mabs-whats-new-mabs.md)
