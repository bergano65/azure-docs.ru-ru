---
title: Руководство по по выполнению сетевых миграций PostgreSQL в Базу данных Azure для PostgreSQL с помощью Azure Database Migration Service | Документация Майкрософт
description: Узнайте, как выполнить сетевую миграцию из PostgreSQL в локальной среде в Базу данных Azure для PostgreSQL с помощью Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 29776c1a49161daf9cf972c43c1378e52f5c3069
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141503"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Руководство по Миграция PostgreSQL в Базу данных Azure для PostgreSQL через Интернет с помощью Azure Database Migration Service

Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра PostgreSQL в [Базу данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/) с минимальным временем простоя. Другими словами, миграцию можно выполнить с минимальным временем простоя для приложения. В этом руководстве выполняется миграция примера базы данных **Прокат DVD** из локального экземпляра PostgreSQL 9.6 в Базу данных Azure для PostgreSQL с помощью действия сетевой миграции в Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
>
> * перенос примера схемы с помощью служебной программы pg_dump;
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум".

> [!IMPORTANT]
> Чтобы процесс миграции был выполнен без проблем, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Скачайте и установите [PostgreSQL Community Edition](https://www.postgresql.org/download/) версии 9.5, 9.6 или 10. На исходном сервере должна быть установлена PostgreSQL версии 9.5.11, 9.6.7, 10 или более поздней. Дополнительные сведения см. в статье [Поддерживаемые версии базы данных PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Кроме того, локальная версия PostgreSQL должна соответствовать Базе данных Azure для версии PostgreSQL. Например, PostgreSQL версии 9.5.11.5 можно перенести только в Базу данных Azure для PostgreSQL версии 9.5.11, но не версии 9.6.7.

    > [!NOTE]
    > Для PostgreSQL версии 10 в настоящее время DMS поддерживает только миграцию версии 10.3 в Базу данных Azure для PostgreSQL.

* [Создайте экземпляр в Базе данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
* Создайте виртуальную сеть Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Дополнительные сведения о создании виртуальной сети приведены в [документации по виртуальным сетям](https://docs.microsoft.com/azure/virtual-network/). В частности, уделите внимание кратким руководствам с пошаговыми инструкциями.

    > [!NOTE]
    > Если вы используете ExpressRoute с пиринговой связью с сетью корпорации Майкрософт, во время настройки виртуальной сети добавьте в подсеть, в которой будет подготовлена служба, следующие [конечные точки](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview):
    > * целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
    > * конечную точку службы хранилища;
    > * конечную точку служебной шины.
    >
    > Эта настройка необходима, потому что у Azure Database Migration Service нет подключения к Интернету.

* Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие входящие порты для Azure Database Migration Service: 443, 53, 9354, 445, 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Plan virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) (Планирование виртуальных сетей).
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу PostgreSQL Server. По умолчанию это TCP-порт 5432.
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для Базы данных Azure для PostgreSQL, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
* Вызвать интерфейс командной строки можно двумя способами.

  * Выберите "Cloud Shell" в правом верхнем углу окна портала Azure.

       ![Кнопка "Cloud Shell" на портале Azure](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Установите и запустите CLI локально. CLI 2.0 — это инструмент командной строки для управления ресурсами Azure.

       Чтобы установить CLI, следуйте инструкциям в статье об [установке Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). В этой статье также указаны платформы, поддерживающие CLI 2.0.

       Следуйте инструкциям в [Руководстве по установке Windows 10](https://docs.microsoft.com/windows/wsl/install-win10), чтобы настроить подсистему Windows для Linux (WSL).

* Чтобы включить логическую репликацию в файле postgresql.config, задайте параметры, приведенные ниже.

  * wal_level = **logical**
  * max_replication_slots = [количество слотов], рекомендованный параметр до **5 слотов**
  * max_wal_senders = [количество параллельных задач]. Параметр max_wal_senders задает число параллельных задач, которые можно выполнить: рекомендуемый параметр — до **10 задач**

## <a name="migrate-the-sample-schema"></a>Перенос примера схемы

Чтобы подготовить все объекты базы данных, такие как схемы таблицы, индексы и хранимые процедуры, нам нужно извлечь схему из исходной базы данных и применить ее к нужной базе данных.

1. Используйте команду pg_dump -s, чтобы создать схемы файла дампа для базы данных. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Например, как выгрузить схему файла дампа базы данных dvdrental, см. ниже.
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Дополнительные сведения об использовании программы pg_dump см. в примерах руководства о [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Создайте пустую базу данных в целевой среде, которая является Базой данных Azure для PostgreSQL.

    Дополнительные сведения о том, как подключиться и создать базу данных, см. в статье [Создание базы данных Azure для сервера PostgreSQL с помощью портала Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).

3. Импортируйте схемы в целевую базу данных, созданную путем восстановления схемы файла дампа.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Например:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Если у вас есть внешние ключи в схеме, начальная загрузка и непрерывная синхронизация во время миграции завершатся сбоем. Выполните следующий сценарий в PgAdmin или psql, чтобы извлечь внешний ключ сценария удаления и добавить сценарий внешнего ключа в назначении (База данных Azure для PostgreSQL).
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

    Запустите сценарий удаления внешнего ключа (второй столбец) в результате запроса.

5. Триггер в данных (триггер вставки или обновления) будет обеспечивать их целостность в целевом объекте перед реплицированными данными из источника. Рекомендуется отключить триггеры во всех таблицах **на целевом компьютере** во время миграции и затем повторно включить триггеры после завершения миграции.

    Чтобы отключить триггеры в целевой базе данных, используйте следующую команду:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Если в таблицах есть тип данных ENUM, рекомендуется временно обновить его до типа данных character varying в целевой таблице. После выполнения репликации отмените изменение типа данных в ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Подготовка к работе DMS с помощью интерфейса командной строки

1. Установите расширения синхронизации DMS.
   * Войдите в Azure, выполнив следующую команду.
       ```
       az login
       ```

   * По запросу откройте браузер и введите код для проверки подлинности устройства. Выполните приведенные далее инструкции.
   * Добавьте расширения DMS следующим образом.
       * Чтобы получить список доступных расширений, выполните следующую команду.

           ```
           az extension list-available –otable
           ```

       * Чтобы установить расширение веб-приложения, выполните следующую команду.

           ```
           az extension add –n dms-preview
           ```

   * Чтобы проверить, верно ли установлено расширение DMS, выполните приведенную ниже команду.

       ```
       az extension list -otable
       ```
       Вы должны увидеть следующий результат.

       ```
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

   * В любое время просмотрите все команды, поддерживаемые в DMS, выполнив следующее.

       ```
       az dms -h
       ```

   * Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку, которую необходимо использовать, чтобы подготовить к работе экземпляр службы DMS.

        ```
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Подготовьте к работе экземпляр DMS, выполнив следующую команду.

   ```
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Ниже приведен пример команды и службы, которую она создает.

   * Расположение: Восток США 2
   * Подписка: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Имя группы ресурсов: PostgresDemo
   * Имя службы DMS: PostgresCLI

   ```
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
   ```

   Создание экземпляра службы DMS занимает около 10–12 минут.

3. Чтобы определить IP-адрес агента DMS, так чтобы его можно было добавить в файл Postgres pg_hba.conf, выполните следующую команду.

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Например:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Отобразится результат, похожий на следующий адрес. 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. Добавьте IP-адрес агента DMS к файлу Postgres pg_hba.conf.

    * Запишите IP-адрес DMS после того, как завершите подготовку DMS.
    * Как добавить IP-адрес к файлу pg_hba.conf в источнике, см. ниже.

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. Создайте проект миграции PostgreSQL, выполнив следующую команду.
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Например, следующая команда создает проект, используя следующие параметры.

   * Расположение: Западно-центральная часть США
   * Имя группы ресурсов: PostgresDemo
   * Имя службы: PostgresCLI
   * Имя проекта: PGMigration
   * Исходная платформа: PostgreSQL
   * Целевая платформа: AzureDbForPostgreSql

     ```
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Создайте задачу миграции PostgreSQL, выполнив следующие действия.

    Этот шаг включает использование IP-адреса источника, идентификатора пользователя и пароля, IP-адреса назначения, UserID, пароля и типа задачи для возможности подключения.

   * Чтобы просмотреть полный список параметров, выполните приведенную ниже команду.

       ```
       az dms project task create -h
       ```

       Для исходного и целевого соединений входной параметр относится к JSON-файлу, который имеет список объектов.

       Формат подключения JSON-объекта для PostgreSQL.
        
       ```
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Также имеется JSON-файл параметров базы данных, который содержит JSON-объекты. Для PostgreSQL ниже приведен параметр базы данных JSON-объекта.

       ```
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Создайте JSON-файл в Блокноте, скопируйте следующие команды и вставьте их в файл, а затем сохраните его в C:\DMS\source.json.

        ```
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Создайте другой файл с именем target.json и сохраните в C:\DMS\target.json. Следующие команды включены.

       ```
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Создайте параметры базы данных JSON-файла, который содержит инвентаризацию, например базу данных для переноса.

       ``` 
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Выполните следующую команду, которая принимает источник, назначение и файлы базы данных JSON-файлов.

       ``` 
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     На этом этапе вы успешно отправили задачу миграции.

7. Чтобы отобразить ход выполнения задачи, выполните следующую команду.

   ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   Или

    ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. Также можно запрашивать migrationState из выходных данных развертывания.

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>Основные сведения о состоянии задачи миграции

В выходном файле существует несколько параметров, которые указывают ход выполнения миграции. Например, просмотрите выходной файл, приведенный ниже.

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>Задача прямой миграции

База данных готова к прямой миграции после завершения полной загрузки. В зависимости от того, как загружен исходный сервер новыми транзакциями, задача DMS может по-прежнему применяться к изменениям после завершения полной загрузки.

Чтобы обеспечить согласование всех данных, проверить количество строк между исходной и целевой базой данных, используйте приведенный ниже пример команды.

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1. Выполните задачу прямой миграции базы данных с помощью следующей команды.

    ```
    az dms project task cutover -h
    ```

    Например:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2. Запустите следующие команды, чтобы отследить ход выполнения прямой миграции.

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>Службы, проект, задача очистки

Если необходимо отменить или удалить любую задачу, проект или службу DMS, выполните отмену в следующей последовательности:

* отменить любую выполняемую задачу;
* удалить задачу;
* удалить проект;
* удалить службу DMS.

1. Чтобы отменить выполняемую задачу, используйте следующую команду.

    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Чтобы удалить выполняемую задачу, используйте следующую команду.
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Чтобы отменить выполняемый проект, используйте следующую команду.
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Чтобы удалить выполняемый проект, используйте следующую команду.
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Чтобы удалить службу DMS, используйте следующую команду.

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Дополнительная информация

* Сведения об известных проблемах, ограничениях при выполнении сетевой миграции в Базу данных Azure для PostgreSQL см. в [этой](known-issues-azure-postgresql-online.md) статье.
* См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Общие сведения о Базе данных Azure для PostgreSQL см. в статье [Что такое база данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
