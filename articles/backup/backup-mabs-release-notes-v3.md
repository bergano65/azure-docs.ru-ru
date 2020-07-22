---
title: Заметки о выпуске Microsoft Azure Backup Server версии 3
description: В этой статье содержатся сведения об известных проблемах и способах их решения для Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 2f67b73612bd970c903b179a4a02c787ee0320b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629170"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Заметки о выпуске Microsoft Azure Backup Server

В этой статье представлены известные проблемы и их решения для Microsoft Azure Backup Server (MABS) версии 3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Сбои резервного копирования и восстановления для кластеризованных рабочих нагрузок

**Описание.** Происходит сбой резервного копирования и восстановления для кластеризованных источников данных, например, таких как кластер Hyper-V, SQL (SQL Always On) или Exchange в группе доступности базы данных (DAG), после обновления с MABS версии 2 до MABS версии 3.

**Обходной путь.** Чтобы избежать этого, откройте SQL Server Management Studio (SSMS) и выполните следующий скрипт SQL для базы данных DPM:

```sql
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
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Сбои при обновлении до MABS версии 3 в русском языковом стандарте

**Описание.** Обновление с MABS версии 2 до MABS версии 3 в русском языковом стандарте завершается сбоем с кодом ошибки **4387**.

**Обходной путь.** Выполните следующие действия для обновления до MABS версии 3 с помощью русского пакета установки:

1. [Создайте резервную копию](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) базы данных SQL и удалите MABS версии 2 (выберите сохранение защищенных данных во время удаления).
2. Обновитесь до SQL 2017 (Enterprise) и удалите отчеты в рамках обновления.
3. [Установите](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Установите](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Настройте отчетность с использованием параметров, как описано в разделе о [настройке SSRS с помощью SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Установите](backup-azure-microsoft-azure-backup.md) MABS версии 3.
7. [Восстановите](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL с помощью SSMS и запустите средство DPM-Sync, как описано [здесь](https://docs.microsoft.com/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync).
8. Обновите свойство DataBaseVersion в таблице dbo.tbl_DLS_GlobalSetting, используя следующую команду:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Запустите службу MSDPM.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>После установки UR1 отчеты MABS не обновляются с помощью новых RDL-файлов

**Описание**: при использовании UR1 проблема форматирования отчета MABS исправлена с помощью обновленных RDL-файлов. Новые RDL-файлы не заменяются автоматически существующими файлами.

**Обходное решение**. чтобы заменить RDL-файлы, выполните следующие действия:

1. На компьютере MABS откройте веб-портал SQL Reporting Services URL.
1. В URL-адресе Web Portal папка DPMReports представлена в формате**`DPMReports_<GUID>`**

    >[!NOTE]
    >Всегда существует только одна папка с этим соглашением об именовании. Если MABS обновляется с предыдущей версии, также может существовать другая старая папка, но ее нельзя будет открыть.

    ![Папка DPMReports](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Выберите и откройте **`DPMReports_<GUID>`** папку. Отдельные файлы отчетов будут перечислены, как показано ниже.

    ![Список отдельных файлов отчетов](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Выберите файлы отчетов, которые не заканчиваются **отчетом**, щелкните правой кнопкой мыши **параметр** и выберите пункт **Управление**.

    ![Выбор управления для файлов отчетов](./media/backup-mabs-release-notes-v3/manage-files.png)

1. На новой странице выберите параметр **заменить** , чтобы заменить файлы последними файлами отчетов.

    Последние файлы отчетов можно найти по пути`<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Например: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Замена файлов последними файлами отчетов](./media/backup-mabs-release-notes-v3/replace-files.png)

    После замены файлов убедитесь, что **имя** и **Описание** не повреждены и не являются пустыми.

1. После замены файлов перезапустите службы MABS и используйте файлы отчетов.

## <a name="next-steps"></a>Дальнейшие шаги

[Новые возможности в MABS](backup-mabs-whats-new-mabs.md)
