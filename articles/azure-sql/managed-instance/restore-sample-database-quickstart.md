---
title: Краткое руководство. Восстановление резервных копий (SSMS)
titleSuffix: Azure SQL SQL Managed Instance
description: В этом кратком руководстве описано, как восстановить резервную копию базы данных в Управляемый экземпляр SQL Azure с помощью SQL Server Management (SSMS).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: c750912e942d5dadeb97e6675427f1730912704a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267585"
---
# <a name="quickstart-restore-a-database-to-an-azure-sql-managed-instance-with-ssms"></a>Краткое руководство. Восстановление резервной копии базы данных в Управляемый экземпляр SQL Azure с помощью SSMS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

В этом кратком руководстве будет использоваться SQL Server Management Studio (SSMS) для восстановления базы данных (Wide World Importers — стандартный файл резервной копии) из хранилища BLOB-объектов Azure в [Управляемый экземпляр](sql-managed-instance-paas-overview.md) SQL Azure.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Дополнительные сведения об использовании Azure Database Migration Service (DMS) для миграции см. в статье [Перенос в Управляемый экземпляр SQL с помощью DMS](../../dms/tutorial-sql-server-to-managed-instance.md).
> Дополнительные сведения о методах миграции см. в статье [Перенос SQL Server в Управляемый экземпляр SQL Azure](migrate-to-instance-from-sql-server.md).

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве:

- Используйте ресурсы из статьи краткого руководства [Создание Управляемого экземпляра SQL Azure](instance-create-quickstart.md).
- Установите последнюю версию [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms).
- SSMS используется для подключения к Управляемому экземпляру SQL. Ознакомьтесь с этими краткими руководствами по подключению.
  - [Включение общедоступной конечной точки](public-endpoint-configure.md) в Управляемом экземпляре SQL. Рекомендуется использовать этот подход для работы с данным руководством.
  - [Подключение к Управляемому экземпляру SQL с виртуальной машины Azure](connect-vm-instance-configure.md)
  - [Настройка подключения "точка — сеть" к Управляемому экземпляру SQL из локальной сети](point-to-site-p2s-configure.md).

> [!NOTE]
> Дополнительные сведения о резервном копировании и восстановлении базы данных SQL Server при помощи хранилища BLOB-объектов Azure и [ключа подписанного URL-адреса (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) см. в статье [Краткое руководство. Восстановление резервной копии базы данных в Управляемый экземпляр Базы данных SQL Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-from-a-backup-file"></a>Восстановление из файла резервной копии

С помощью SQL Server Management Studio SSMS выполните следующие шаги для восстановления базы данных Wide World Importers в Управляемый экземпляр SQL. Файл резервной копии базы данных хранится в предварительно настроенной учетной записи хранения больших двоичных объектов Azure.

1. Откройте SSMS и подключитесь к Управляемому экземпляру SQL.
2. В **обозревателе объектов** щелкните правой кнопкой мыши "Управляемый экземпляр SQL" и выберите **Создать запрос**, чтобы открыть окно нового запроса.
3. Запустите следующий скрипт SQL, в котором используется предварительно настроенная учетная запись хранения и ключ SAS, для [создания учетных данных](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) в Управляемом экземпляре SQL.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![Создание учетных данных](./media/restore-sample-database-quickstart/credential.png)

4. Чтобы проверить учетные данные, запустите следующий скрипт, который использует URL-адрес [контейнера](https://azure.microsoft.com/services/container-instances/) для получения списка файлов резервной копии.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![список файлов](./media/restore-sample-database-quickstart/file-list.png)

5. Запустите следующий скрипт, чтобы восстановить базу данных Wide World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Восстановление](./media/restore-sample-database-quickstart/restore.png)

6. Запустите следующий скрипт, чтобы отслеживать состояние восстановления.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. По завершении восстановления просмотрите базу данных в обозревателе объектов. Чтобы убедиться, что восстановление базы данных завершено, используйте представление [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).

> [!NOTE]
> Операция восстановления базы данных является асинхронной и повторяемой. В случае разрыва подключения или истечения времени ожидания в SQL Server Management Studio может возникнуть ошибка. База данных SQL Azure будет пытаться восстановить базу данных в фоновом режиме, и вы сможете отслеживать ход восстановления с помощью представлений [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) и [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).
> На некоторых этапах процесса восстановления в системных представлениях вместо фактического имени базы данных будет отображаться ее уникальный идентификатор. [Узнайте больше](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement) о различиях в поведении инструкции `RESTORE`.

## <a name="next-steps"></a>Дальнейшие действия

- Если на шаге 5 восстановление базы данных завершается и появляется сообщение ID 22003 (Идентификатор 22003), создайте новый файл резервной копии, содержащий контрольные суммы резервной копии, и снова выполните восстановление. См. статью [Включение или отключение вычисления контрольных сумм резервных копий во время архивации или восстановления](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
- Сведения об устранении неполадок резервного копирования на URL-адрес см. в разделе [Резервное копирование SQL Server на URL-адрес — рекомендации и устранение неполадок](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Обзор вариантов подключения для приложений см. в статье [Подключение приложения к Управляемому экземпляру SQL](connect-application-instance.md).
- Чтобы отправлять запросы с помощью привычных средств или языков, ознакомьтесь со статьей [Краткие руководства. Подключение и создание запросов к Базе данных SQL Azure](../database/connect-query-content-reference-guide.md).
