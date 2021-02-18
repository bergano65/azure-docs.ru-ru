---
title: Перенос баз данных в SQL Управляемый экземпляр с помощью службы воспроизведения журналов
description: Сведения о переносе баз данных из SQL Server в SQL Управляемый экземпляр с помощью службы воспроизведения журналов
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/17/2021
ms.openlocfilehash: 7892b1fe0fcad77d1fde8b44f4a8745b5c7dd334
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654596"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Перенос баз данных из SQL Server в SQL Управляемый экземпляр с помощью службы воспроизведения журналов
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

В этой статье объясняется, как вручную настроить миграцию базы данных с SQL Server 2008-2019 на SQL Управляемый экземпляр с помощью службы воспроизведения журналов (LRS). Это облачная служба, включенная для управляемого экземпляра на основе технологии доставки журналов SQL Server в режиме без восстановления. LRS следует использовать в случаях, когда не удается использовать службу миграции данных (DMS), если требуется больше элементов управления или существует небольшая погрешность для простоя.

## <a name="when-to-use-log-replay-service"></a>Когда следует использовать службу воспроизведения журналов

В случаях, когда служба [DMS Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) не может использоваться для миграции, LRS облачную службу можно использовать непосредственно с PowerShell, командлетами CLI или API для ручной сборки и координации миграции баз данных в управляемый экземпляр SQL. 

Вы можете использовать облачную службу LRS в следующих случаях:
- Для проекта переноса базы данных требуется дополнительный контроль
- Существует небольшая погрешность для простой прямую миграцию миграции.
- Невозможно установить исполняемый файл DMS в вашей среде
- Исполняемый файл DMS не имеет доступа к резервным копиям базы данных
- Доступ к ОС узла невозможен или отсутствуют права администратора

> [!NOTE]
> Рекомендуемый автоматизированный способ переноса баз данных из SQL Server в SQL Управляемый экземпляр — использование Azure DMS. Эта служба использует одну и ту же облачную службу LRS в серверной части с доставкой журналов в режиме без восстановления. Рекомендуется вручную использовать LRS для управления миграцией в тех случаях, когда Azure DMS не полностью поддерживает ваши сценарии.

## <a name="how-does-it-work"></a>Как это работает?

Для создания пользовательского решения с помощью LRS для миграции базы данных в облако требуется выполнить несколько действий по оркестрации, показанных на схеме и описанных в таблице ниже.

Миграция влечет за собой создание полных резервных копий базы данных на SQL Server и копирование файлов резервных копий в хранилище BLOB-объектов Azure. LRS используется для восстановления файлов резервных копий из хранилища BLOB-объектов Azure в управляемый экземпляр SQL. Хранилище BLOB-объектов Azure используется в качестве промежуточного хранилища между SQL Server и SQL Управляемый экземпляр.

LRS будет отслеживать хранилище больших двоичных объектов Azure на наличие новых разностных резервных копий, а также добавлять созданные после восстановления полной резервной копии журналы и автоматически восстанавливать новые добавленные файлы. Ход выполнения восстановления файлов резервных копий в управляемом экземпляре SQL можно отслеживать с помощью службы. Кроме того, процесс может быть прерван при необходимости. Базы данных, восстанавливаемые в процессе миграции, будут находиться в режиме восстановления и не могут использоваться для чтения или записи до завершения процесса.

LRS можно запустить в автозаполнении или непрерывном режиме. При запуске в режиме автозаполнения миграция будет выполнена автоматически, когда был восстановлен последний указанный файл резервной копии. При запуске в непрерывном режиме служба постоянно восстанавливает все добавленные файлы резервных копий, и миграция будет выполнена только в ручном прямую миграцию. Окончательный шаг прямую миграцию сделает базы данных доступными для чтения и записи в SQL Управляемый экземпляр. 

  ![Описание действий по оркестрации службы воспроизведения журналов для SQL Управляемый экземпляр](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Операция | Сведения |
| :----------------------------- | :------------------------- |
| **1. Скопируйте резервные копии базы данных из SQL Server в хранилище BLOB-объектов Azure**. | — Копирование полных, разностных и резервных копий журналов из SQL Server в хранилище BLOB-объектов Azure с помощью [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) или [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/). <br />— При переносе нескольких баз данных для каждой базы данных требуется отдельная папка. |
| **2. Запустите службу LRS в облаке**. | -Service можно запустить с помощью командлетов: <br /> PowerShell [Start-азсклинстанцедатабаселогреплай](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Командлеты CLI az_sql_midb_log_replay_start](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /><br />— После запуска служба выполнит резервное копирование из хранилища BLOB-объектов Azure и начнет их восстановление на Управляемый экземпляр SQL. <br /> — После восстановления всех начальных резервных копий служба будет следить за новыми файлами, переданными в папку, и будет постоянно применять журналы на основе цепочки LSN, пока служба не будет остановлена. |
| **2,1. Отслеживайте ход выполнения операции**. | — Ход выполнения операции восстановления можно отслеживать с помощью командлетов или: <br /> PowerShell [Get-азсклинстанцедатабаселогреплай](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> [Командлеты CLI az_sql_midb_log_replay_show](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2,2. при необходимости стоп\аборт операцию**. | В случае, если процесс миграции необходимо прерывать, операция может быть остановлена с помощью командлетов: <br /> PowerShell [-азсклинстанцедатабаселогреплай](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> Командлеты CLI [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) . <br /><br />— Это приведет к удалению базы данных, восстановленной в Управляемый экземпляр SQL. <br />-После остановки LRS не может быть продолжен для базы данных. Процесс миграции необходимо перезапустить с нуля. |
| **3. прямую миграцию в облако при готовности**. | — После восстановления всех резервных копий на SQL Управляемый экземпляр выполните операцию прямую миграцию, инициируя LRS Complete, с помощью вызова API или командлетов: <br />PowerShell [Complete — азсклинстанцедатабаселогреплай](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Командлеты CLI [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) . <br /><br />— Это приведет к остановке службы LRS, и база данных на Управляемый экземпляр будет восстановлена. <br />-Переведите строку подключения приложения с SQL Server на SQL Управляемый экземпляр. <br />— База данных завершения операций доступна для операций R/W в облаке. |

## <a name="requirements-for-getting-started"></a>Требования к началу работы

### <a name="sql-server-side"></a>SQL Serverная сторона
- SQL Server 2008-2019
- Полное резервное копирование баз данных (один или несколько файлов)
- Разностная резервная копия (один или несколько файлов)
- Резервная копия журнала (не разбиение для файла журнала транзакций)
- **Контрольная сумма должна быть включена** как обязательная

### <a name="azure-side"></a>На стороне Azure
-   PowerShell AZ. SQL Module версии 2.16.0 или выше ([Установка](https://www.powershellgallery.com/packages/Az.Sql/)или использование Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/))
-   CLI версии 2.19.0 или выше ([Установка](https://docs.microsoft.com/cli/azure/install-azure-cli))
-   Хранилище BLOB-объектов Azure подготовлено
-   Маркер безопасности SAS с разрешениями на **Чтение** и **перечисление** , созданные для хранилища BLOB-объектов

## <a name="best-practices"></a>Рекомендации

В качестве рекомендаций настоятельно рекомендуется использовать следующие рекомендации:
- Запустите [Помощник по миграции данных](https://docs.microsoft.com/sql/dma/dma-overview) для проверки того, что базы данных не будут переноситься в SQL управляемый экземпляр. 
- Разбиение полных и разностных резервных копий на несколько файлов вместо одного файла.
- Включите сжатие резервных копий.
- Используйте Cloud Shell для выполнения скриптов, так как они всегда будут обновлены до выпуска последних версий командлетов.
- Запланируйте Завершение миграции в течение 47 часов с момента запуска службы LRS.

> [!IMPORTANT]
> - Базу данных, восстанавливаемую с помощью LRS, нельзя использовать до завершения процесса миграции. Это обусловлено тем, что базовая технология — это доставка журналов в режиме без восстановления.
> - Режим ожидания для доставки журналов не поддерживается LRS из-за различий версий SQL Управляемый экземпляр и последней версии SQL Server на рынке.

## <a name="steps-to-execute"></a>Шаги для выполнения

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Копирование резервных копий из SQL Server в хранилище BLOB-объектов Azure

Можно использовать следующие два подхода для копирования резервных копий в хранилище BLOB-объектов в миграции баз данных на Управляемый экземпляр с помощью LRS:
- Использование SQL Server собственного [резервного копирования для работы с URL-адресами](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) .
- Копирование резервных копий в контейнер больших двоичных объектов с помощью [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)или [Обозреватель службы хранилища Azure](https://azure.microsoft.com/en-us/features/storage-explorer). 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Создание BLOB-объекта Azure и маркера проверки подлинности SAS

Хранилище BLOB-объектов Azure используется в качестве промежуточного хранилища для файлов резервных копий между SQL Server и SQL Управляемый экземпляр. Чтобы создать контейнер хранилища BLOB-объектов Azure, выполните следующие действия.

1. [создать учетную запись хранения;](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Создавайте контейнер больших двоичных объектов](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) в учетной записи хранения

После создания контейнера больших двоичных объектов создайте маркер проверки подлинности SAS с разрешениями на чтение и перечисление, выполнив следующие действия.

1. Доступ к учетной записи хранения с помощью портал Azure
2. Перейдите к Обозреватель службы хранилища
3. Разверните раздел Контейнеры больших двоичных объектов.
4. Щелкните правой кнопкой мыши контейнер больших двоичных объектов
5. Выберите получить подпись общего доступа
6. Выберите срок действия маркера. Убедитесь, что токен действителен для периода миграции.
7. Убедитесь, что выбраны разрешения только на чтение и на список.
8. Нажмите кнопку "Создать"
9. Скопируйте маркер, начинающийся с "SV =", в URI для использования в коде.

> [!IMPORTANT]
> Разрешения для маркера SAS для хранилища BLOB-объектов Azure должны быть прочитаны и перечислены только в списке. В случае любых других разрешений, предоставленных для маркера проверки подлинности SAS, запуск службы LRS завершится ошибкой. Эти требования безопасности предназначены для разработки.

## <a name="log-in-to-azure-and-select-subscription"></a>Войдите в Azure и выберите подписку.

Используйте следующий командлет PowerShell для входа в Azure:

```powershell
Login-AzAccount
```

Выберите подходящую подписку, в которой находится SQL Управляемый экземпляр, с помощью следующего командлета PowerShell:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Начало миграции

Миграция начнется с запуска службы LRS. Службу можно запустить в автозаполнении или непрерывном режиме. При запуске в режиме автозаполнения миграция будет выполнена автоматически, когда был восстановлен последний указанный файл резервной копии. Для этого параметра требуется команда Start, чтобы указать имя файла последнего файла резервной копии. Когда LRS запускается в непрерывном режиме, служба постоянно восстанавливает все добавленные файлы резервных копий, а миграция закончится только в ручном прямую миграцию. 

### <a name="start-lrs-in-autocomplete-mode"></a>Запуск LRS в режиме автозаполнения

Чтобы запустить службу LRS в режиме автозаполнения, используйте следующую команду PowerShell или команды CLI. Укажите имя последнего файла резервной копии с параметром-Ластбаккупнаме. При восстановлении указанного имени файла последней резервной копии служба автоматически инициирует прямую миграцию.

Запуск LRS в режиме автозаполнения с примером PowerShell:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

Запуск LRS в режиме автозаполнения — пример CLI:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Запуск LRS в непрерывном режиме

Запуск LRS в непрерывном режиме — пример PowerShell:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Запуск LRS в непрерывном режиме — пример CLI:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> После запуска LRS все системные исправления, управляемые системой, будут остановлены в течение следующих 47 часов. После передачи этого окна следующее автоматическое обновление программного обеспечения автоматически останавливает текущую LRS. В таком случае миграцию невозможно возобновить, и ее необходимо перезапустить с нуля. 

## <a name="monitor-the-migration-progress"></a>Мониторинг хода выполнения миграции

Чтобы отслеживать ход выполнения операции миграции, используйте следующую команду PowerShell:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Чтобы отслеживать ход выполнения операции миграции, используйте следующую команду CLI:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Завершение миграции

Если необходимо отключить миграцию, используйте следующие командлеты. Остановка миграции приведет к удалению восстановления базы данных на управляемом экземпляре SQL из-за того, что возобновление миграции будет невозможно.

Чтобы стоп\аборт процесс миграции, используйте следующую команду PowerShell:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Чтобы стоп\аборт процесс миграции, используйте следующую команду интерфейса командной строки:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Завершение миграции (непрерывный режим)

Если LRS запускается в непрерывном режиме, то после того, как вы убедитесь, что все резервные копии восстановлены, инициализация прямую миграцию завершит миграцию. После завершения прямую миграцию база данных будет перенесена и готова к доступу на чтение и запись.

Чтобы завершить процесс миграции в непрерывном режиме LRS, используйте следующую команду PowerShell:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" -LastBackupName "last_backup.bak"
```

Чтобы завершить процесс миграции в непрерывном режиме LRS, используйте следующую команду интерфейса командной строки:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о [миграции SQL Server в управляемый экземпляр SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Дополнительные сведения о [различиях между SQL Server и управляемый экземпляр SQL Azure](transact-sql-tsql-differences-sql-server.md).
- Узнайте больше о [рекомендациях по затратам и размеру рабочих нагрузок, перенесенных в Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
