---
title: Руководство по выполнению сетевых миграций из RDS PostgreSQL в Базу данных Azure для PostgreSQL с помощью Azure Database Migration Service | Документация Майкрософт
description: Узнайте, как выполнить сетевую миграцию из RDS PostgreSQL в Базу данных Azure для PostgreSQL с помощью Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/06/2019
ms.openlocfilehash: 6cb10f09772bf6666e197a4b622792c5b62d3ace
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734800"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Руководство по Перенос баз данных RDS PostgreSQL в Базу данных Azure для PostgreSQL через Интернет с помощью DMS

С помощью службы Azure Database Migration Service базы данных из экземпляра RDS PostgreSQL можно перенести в [Базу данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/), не отключая базу данных-источник от сети. Другими словами, миграцию можно выполнить с минимальным временем простоя для приложения. В этом руководстве выполняется миграция примера базы данных **Прокат DVD** из экземпляра RDS PostgreSQL 9.6 в Базу данных Azure для PostgreSQL с помощью действия сетевой миграции в Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
>
> * перенос примера схемы с помощью служебной программы pg_dump;
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с использованием Azure Database Migration Service.
> * выполнение миграции.
> * мониторинг миграции.

> [!NOTE]
> Чтобы выполнить подключенную миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум". Дополнительные сведения см. на странице с [ценами](https://azure.microsoft.com/pricing/details/database-migration/) на Azure Database Migration Service.

> [!IMPORTANT]
> Чтобы оптимизировать процесс миграции, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

В этой статье описывается сетевая миграция из экземпляра RDS PostgreSQL в Базу данных Azure для PostgreSQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Скачайте и установите [PostgreSQL Community Edition](https://www.postgresql.org/download/) версии 9.5, 9.6 или 10. На исходном сервере должна быть установлена PostgreSQL версии 9.5.11, 9.6.7, 10 или более поздней. Дополнительные сведения см. в статье [Поддерживаемые версии базы данных PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Кроме того, версия RDS PostgreSQL должна соответствовать Базе данных Azure для версии PostgreSQL. Например, RDS PostgreSQL версии 9.5.11.5 можно перенести только в Базу данных Azure для PostgreSQL версии 9.5.11, но не версии 9.6.7.

* Создайте экземпляр [Базы данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal). В [этом разделе](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) документа приводятся сведения о том, как подключиться к серверу PostgreSQL с помощью pgAdmin.
* Создайте виртуальную сеть Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Дополнительные сведения о создании виртуальной сети приведены в [документации по виртуальным сетям](https://docs.microsoft.com/azure/virtual-network/). В частности, уделите внимание кратким руководствам с пошаговыми инструкциями.
* Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие входящие порты для Azure Database Migration Service: 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Plan virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Планирование виртуальных сетей).
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу PostgreSQL. По умолчанию это TCP-порт 5432.
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для сервера Базы данных Azure для PostgreSQL, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Настройка репликации для AWS RDS PostgreSQL

1. Чтобы создать новую группу параметров, выполните инструкции AWS из статьи [о работе с группами параметров базы данных](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Используйте имя главного пользователя, чтобы подключиться к источнику из Azure Database Migration Service. Если вы используете учетную запись, отличную от учетной записи главного пользователя, она должна иметь роли rds_superuser и rds_replication. Роль rds_replication предоставляет разрешения на управление логическими слотами и потоковую передачу данных через логические слоты.
3. Создайте группу параметров со следующей конфигурацией: а. Для параметра rds.logical_replication в группе параметров базы данных задайте значение 1.
    b. max_wal_senders = [количество параллельных задач]. Параметр max_wal_senders задает число параллельных задач, которые можно выполнить, рекомендуемое значение — 10 задач.
    c. max_replication_slots = [количество слотов], рекомендуемое значение — до 5 слотов.
4. Свяжите созданную группу параметров с экземпляром RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Перенос схемы

1. Чтобы перенести все объекты базы данных, такие как схемы таблицы, индексы и хранимые процедуры, извлеките схему из исходной базы данных и примените ее к нужной базе данных.

    Чтобы перенести только схему, проще всего выполнить pg_dump с параметром -s. Дополнительные сведения см. в разделе [примеров](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) в руководстве по pg_dump для Postgres.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Например, чтобы выгрузить файл схемы дампа для базы данных **dvdrental**, используйте следующую команду:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Создайте пустую базу данных в целевой службе, то есть в Базе данных Azure для PostgreSQL. Чтобы подключиться и создать базу данных, воспользуйтесь одной из следующих статей.

    * [Create an Azure Database for PostgreSQL by using the Azure portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) (Создание базы данных Azure для PostgreSQL с помощью портала Azure)
    * [Create an Azure Database for PostgreSQL by using the Azure CLI](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-azure-cli) (Создание базы данных Azure для PostgreSQL с помощью Azure CLI)

3. Импортируйте схему в целевую службу, то есть в Базу данных Azure для PostgreSQL. Чтобы восстановить файл дампа схемы, выполните следующую команду:

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

5. Запустите команду drop foreign key в результате запроса, чтобы удалить внешний ключ (второй столбец).

6. Если у вас есть триггеры (триггер вставки или обновления), они будут обеспечивать целостность данных в целевом объекте перед реплицированием данных из источника. Рекомендуется отключить триггеры во всех таблицах *целевого объекта* на время миграции, а затем включить их после завершения миграции.

    Чтобы удалить триггеры в целевой базе данных, выполните:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Создание экземпляра Azure Database Migration Service

1. На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором нужно создать экземпляр Azure Database Migration Service.

5. Создайте виртуальную сеть или выберите имеющуюся.

    Виртуальная сеть предоставляет для Azure Database Migration Service доступ к исходному экземпляру PostgreSQL и целевому экземпляру Базы данных Azure для PostgreSQL.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Краткое руководство. Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

6. Выберите ценовую категорию. Для этой миграции по сети необходимо выбрать ценовую категорию "Премиум: 4 виртуальных ядра".

    ![Настройка параметров экземпляра Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings4.png)

7. Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра Azure Database Migration Service и выберите его.

     ![Поиск экземпляра службы миграции базы данных Azure](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-instance-search.png)

3. Выберите **+ Новый проект миграции**.
4. На экране **Новый проект миграции** задайте имя проекта, в текстовом поле **Тип исходного сервера** выберите **AWS RDS для PostgreSQL**, а в текстовом поле **Тип целевого сервера** — **База данных Azure для PostgreSQL**.
5. В разделе **Выберите тип действия** выберите **Миграция данных по сети**.

    > [!IMPORTANT]
    > Не забудьте выбрать режим **миграции данных по сети**, так как автономная миграция не поддерживается для этого сценария.

    ![Создание проекта Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Кроме того, вы можете выбрать **Создать только проект**, чтобы создать проект миграции для отложенного выполнения.

6. Щелкните **Сохранить**.

7. Выберите **Создать и выполнить действие**, чтобы создать проект и выполнить действие миграции.

    > [!NOTE]
    > Запишите предварительные требования, необходимые для настройки интерактивной миграции, в колонке создания проекта.

## <a name="specify-source-details"></a>Указание сведений об источнике

* На экране **Migration source detail** (Сведения об источнике миграции) задайте сведения о подключении для исходного экземпляра PostgreSQL.

   ![Сведения об источнике](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details4.png)

## <a name="specify-target-details"></a>Указание сведений о цели

1. Нажмите кнопку **Сохранить**, а затем на экране **Сведения о целевом объекте** укажите сведения о подключении для предварительно подготовленного целевого сервера Базы данных Azure для PostgreSQL, для которого была развернута схема **Прокат DVD** с помощью PostgreSQL.

    ![Выбор цели](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-select-target4.png)

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится база данных с тем же именем, что у исходной базы данных, Azure Database Migration Service по умолчанию выберет целевую базу данных.

    ![Сопоставление с целевыми базами данных](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-targets-activity5.png)

3. Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Сводка по миграции](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

    Появится окно действия миграции и в поле **Состояние** будет указано **Инициализация**.

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажимайте **Обновить**, чтобы периодически обновлять содержимое экрана, пока в поле **Состояние** не будет указано **Выполнение**.

    ![Состояние действия "Выполняется"](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. В столбце **Имя базы данных** выберите базу данных, чтобы узнать состояние миграции для операций **полной загрузки данных** и **добавочной синхронизации данных**.

    В разделе **полной загрузки данных** отображается состояние начальной загрузки, а в разделе **добавочной синхронизации данных** — состояние отслеживания измененных данных (CDC).

    ![Экран инвентаризации — полная загрузка данных](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Экран инвентаризации — добавочная синхронизация данных](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции

После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

    ![Начало перехода](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-start-cutover.png)

2. Обязательно остановите все входящие транзакции в исходную базу данных. Подождите, пока в счетчике **Ожидающие изменения** появится значение **0**.
3. Выберите **Подтвердить**, а затем — **Применить**.
4. Когда состояние переноса базы данных изменится на **Завершено**, подключите свои приложения к новому целевому экземпляру Базы данных Azure для PostgreSQL.

Итак, сетевой перенос локального экземпляра PostgreSQL в Базу данных Azure для PostgreSQL завершен.

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Общие сведения о Базе данных Azure для PostgreSQL см. в статье [Что такое база данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview)
* По другим вопросам отправляйте сообщения электронной почты на псевдоним [Спросить о миграции базы данных Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).
