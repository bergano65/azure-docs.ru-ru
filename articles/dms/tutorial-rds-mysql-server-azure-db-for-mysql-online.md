---
title: Руководство по выполнению интерактивного переноса RDS MySQL в Базу данных Azure для MySQL с помощью Azure Database Migration Service | Документация Майкрософт
description: Узнайте, как выполнить интерактивную миграцию из RDS MySQL в Базу данных Azure для MySQL с помощью Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 09b90f4b53750b94c0ecee7290d6b5405c984ff9
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154884"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Руководство по интерактивному переносу RDS MySQL в Базу данных Azure для MySQL с помощью DMS

С помощью службы Azure Database Migration Service базы данных из экземпляра RDS MySQL можно перенести в [Базу данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/), не отключая базу данных-источник от сети. Другими словами, миграцию можно выполнить с минимальным временем простоя для приложения. В этом руководстве выполняется перенос примера базы данных **сотрудников** из экземпляра RDS MySQL в Базу данных Azure для MySQL с помощью интерактивного действия миграции в Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Перенос примера схемы с помощью служебных программ mysqldump и mysql.
> * Создание экземпляра Azure Database Migration Service.
> * создание проекта миграции с использованием Azure Database Migration Service.
> * выполнение миграции.
> * мониторинг миграции.

> [!NOTE]
> Чтобы выполнить подключенную миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум". Дополнительные сведения см. на странице с [ценами](https://azure.microsoft.com/pricing/details/database-migration/) на Azure Database Migration Service.

> [!IMPORTANT]
> Чтобы оптимизировать процесс миграции, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

В этой статье описывается выполнение интерактивного переноса из экземпляра RDS MySQL в Базу данных Azure для MySQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Убедитесь, что на исходном сервере MySQL работает поддерживаемая версия MySQL Community Edition. Чтобы определить версию экземпляра MySQL, в служебной программе mysql или MySQL Workbench выполните команду:

    ```
    SELECT @@version;
    ```

    Дополнительные сведения см. в статье [Supported Azure Database for MySQL server versions](https://docs.microsoft.com/azure/mysql/concepts-supported-versions) (Поддерживаемые версии серверов Базы данных Azure для MySQL).

* Скачайте и установите пример базы данных [MySQL **Сотрудники**](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Создайте экземпляр [Базы данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Создайте виртуальную сеть Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Дополнительные сведения о создании виртуальной сети приведены в [документации по виртуальным сетям](https://docs.microsoft.com/azure/virtual-network/). В частности, уделите внимание кратким руководствам с пошаговыми инструкциями.
* Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие входящие порты для Azure Database Migration Service: 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Plan virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Планирование виртуальных сетей).
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу MySQL Server. По умолчанию это TCP-порт 3306.
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для сервера Базы данных Azure для MySQL, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.

> [!NOTE]
> База данных Azure для MySQL поддерживает только таблицы InnoDB. Чтобы преобразовать таблицы MyISAM в InnoDB, ознакомьтесь со сведениями на странице о [преобразовании таблиц](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html).

### <a name="set-up-aws-rds-mysql-for-replication"></a>Настройка AWS RDS MySQL для репликации

1. Чтобы создать группу параметров, выполните инструкции, предоставленные AWS в статье о [файлах журнала Базы данных MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html) в разделе о **формате ведения двоичного журнала**.
2. Создайте группу параметров со следующей конфигурацией:
    * binlog_format = row
    * binlog_checksum = NONE
3. Сохраните новую группу параметров.

## <a name="migrate-the-schema"></a>Перенос схемы

1. Чтобы перенести все объекты базы данных, такие как схемы таблицы, индексы и хранимые процедуры, извлеките схему из исходной базы данных и примените ее к нужной базе данных.

    Самый простой способ переноса только схемы заключается в использовании mysqldump с параметром --no-data. Команда для переноса схемы выглядит так:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Например, чтобы выгрузить файл схемы дампа для базы данных **Сотрудники**, используйте следующую команду:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Импортируйте схему в целевую службу, которая является Базой данных Azure для MySQL. Чтобы восстановить файл схемы дампа, выполните следующую команду:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Например, чтобы импортировать схему для базы данных **Сотрудники**:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Если у вас есть внешние ключи в схеме, начальная загрузка и непрерывная синхронизация во время миграции завершатся сбоем. Чтобы извлечь сценарий удаления и сценарий добавления внешнего ключа в назначении (База данных Azure для MySQL), выполните следующий сценарий в MySQL Workbench.

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
        KCU.TABLE_NAME,
        KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ('SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Запустите сценарий удаления внешнего ключа (второй столбец) в результате запроса, чтобы удалить внешний ключ.

5. Если у вас есть триггеры (триггер вставки или обновления), они будут обеспечивать целостность данных в целевом объекте перед реплицированием данных из источника. Рекомендуется отключить триггеры во всех таблицах *целевого объекта* на время миграции, а затем включить их после завершения миграции.

    Чтобы удалить триггеры в целевой базе данных, выполните:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Если в таблицах есть экземпляры типа данных ENUM, мы рекомендуем временно обновить его до типа данных character varying в целевой таблице. После завершения репликации данных отмените изменение типа данных и снова преобразуйте их в ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Создание экземпляра Azure Database Migration Service

1. На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором нужно создать экземпляр Azure Database Migration Service.

5. Создайте виртуальную сеть или выберите имеющуюся.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру MySQL и целевому экземпляру Базы данных Azure для MySQL.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Краткое руководство. Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

6. Выберите ценовую категорию. Для этой миграции по сети необходимо выбрать ценовую категорию "Премиум: 4 виртуальных ядра".

    ![Настройка параметров экземпляра Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра Azure Database Migration Service и выберите его.

     ![Поиск экземпляра службы миграции базы данных Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Выберите **+ Новый проект миграции**.
4. В окне **New migration project** (Новый проект миграции) задайте имя для проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **MySQL**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **AzureDbForMySQL**.
5. В разделе **Выберите тип действия** выберите **Миграция данных по сети**.

    > [!IMPORTANT]
    > Не забудьте выбрать режим **миграции данных по сети**, так как автономная миграция не поддерживается для этого сценария.

    ![Создание проекта Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Кроме того, вы можете выбрать **Создать только проект**, чтобы создать проект миграции для отложенного выполнения.

6. Щелкните **Сохранить**.

7. Выберите **Создать и выполнить действие**, чтобы создать проект и выполнить действие миграции.

    > [!NOTE]
    > Запишите предварительные требования, необходимые для настройки интерактивной миграции, в колонке создания проекта.

## <a name="specify-source-details"></a>Указание сведений об источнике

* На экране **Migration source detail** (Сведения об источнике миграции) задайте сведения о подключении для исходного экземпляра MySQL.

   ![Сведения об источнике](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Указание сведений о цели

1. Нажмите кнопку **Сохранить**, а затем на экране **Сведения о целевом объекте** укажите сведения о подключении для целевого сервера Базы данных Azure для MySQL, а именно — предварительно подготовленного и для которого была развернута схема **Сотрудники** с помощью MySQLDump.

    ![Выбор цели](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится база данных с тем же именем, что у исходной базы данных, Azure Database Migration Service по умолчанию выберет целевую базу данных.

    ![Сопоставление с целевыми базами данных](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Сводка по миграции](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

    Появится окно действия миграции и в поле **Состояние** будет указано **Инициализация**.

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажимайте **Обновить**, чтобы периодически обновлять содержимое экрана, пока в поле **Состояние** не будет указано **Выполнение**.

    ![Состояние действия "Выполняется"](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. В столбце **Имя базы данных** выберите базу данных, чтобы узнать состояние миграции для операций **полной загрузки данных** и **добавочной синхронизации данных**.

    В разделе **полной загрузки данных** отображается состояние начальной загрузки, а в разделе **добавочной синхронизации данных** — состояние отслеживания измененных данных (CDC).

    ![Экран инвентаризации — полная загрузка данных](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Экран инвентаризации — добавочная синхронизация данных](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции

После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

    ![Начало перехода](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Обязательно остановите все входящие транзакции в исходную базу данных. Подождите, пока в счетчике **Ожидающие изменения** появится значение **0**.
3. Выберите **Подтвердить**, а затем — **Применить**.
4. Когда состояние переноса базы данных изменится на **Завершено**, подключите свои приложения к новой целевой Базе данных Azure для MySQL.

Интерактивный перенос локального экземпляра MySQL в Базу данных Azure для MySQL выполнен.

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Общие сведения о Базе данных Azure для MySQL см. в [этой статье](https://docs.microsoft.com/azure/mysql/overview).
* По другим вопросам отправляйте сообщения электронной почты на псевдоним [Спросить о миграции базы данных Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).
