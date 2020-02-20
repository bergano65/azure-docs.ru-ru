---
title: Руководство. Миграция PostgreSQL в базу данных Azure для PostgreSQL Online через портал Azure
titleSuffix: Azure Database Migration Service
description: Узнайте, как выполнить миграцию из локальной сети PostgreSQL в базу данных Azure для PostgreSQL с помощью Azure Database Migration Service через портал Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 7c496d4c1bdc29064e4ec1d3d0150cf5186ca701
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472782"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Руководство. Миграция PostgreSQL в базу данных Azure для PostgreSQL Online с помощью DMS через портал Azure

Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра PostgreSQL в [базу данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/) с минимальным временем простоя приложения. В этом руководстве выполняется миграция примера базы данных **Прокат DVD** из локального экземпляра PostgreSQL 9.6 в Базу данных Azure для PostgreSQL с помощью действия сетевой миграции в Azure Database Migration Service.

В этом руководстве описано следующее.
> [!div class="checklist"]
>
> * Перенесите образец схемы с помощью служебной программы pg_dump.
> * создание экземпляра Azure Database Migration Service;
> * Создайте проект миграции в Azure Database Migration Service.
> * выполнение миграции.
> * мониторинг миграции.
> * Выполните миграцию прямую миграцию.

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум".

> [!IMPORTANT]
> Чтобы процесс миграции был выполнен без проблем, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Скачайте и установите [PostgreSQL Community edition](https://www.postgresql.org/download/) 9,4, 9,5, 9,6 или 10. Версия исходного сервера PostgreSQL должна быть 9,4, 9,5, 9,6, 10 или 11. Дополнительные сведения см. в статье [Поддерживаемые версии базы данных PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Кроме того, локальная версия PostgreSQL должна соответствовать Базе данных Azure для версии PostgreSQL. Например, PostgreSQL 9,6 можно перенести только в базу данных Azure для PostgreSQL 9,6, 10 или 11, но не в базу данных Azure для PostgreSQL 9,5.

* [Создайте сервер базы данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) или [Создайте сервер базы данных Azure для PostgreSQL-Scale (Цитус)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).
* Создайте виртуальная сеть Microsoft Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение типа "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Дополнительные сведения о создании виртуальной сети см. в [документации по виртуальной сети](https://docs.microsoft.com/azure/virtual-network/), особенно в кратком руководстве, где приведены пошаговые инструкции.

    > [!NOTE]
    > При установке виртуальной сети с помощью ExpressRoute с пирингом сети в корпорацию Майкрософт добавьте следующие [конечные точки](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) службы в подсеть, в которой будет подготовлена служба:
    >
    > * целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
    > * конечную точку службы хранилища;
    > * конечную точку служебной шины.
    >
    > Такая конфигурация вызвана тем, что у Azure Database Migration Service нет подключения к Интернету.

* Убедитесь, что правила группы безопасности сети (NSG) для виртуальной сети не блокируют следующие порты входящего трафика для Azure Database Migration Service: 443, 53, 9354, 445, 12000. Дополнительные сведения о фильтрации трафика NSG в виртуальной сети см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу PostgreSQL Server. По умолчанию это TCP-порт 5432.
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для Базы данных Azure для PostgreSQL, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Укажите диапазон подсети виртуальной сети, используемый для Azure Database Migration Service.
* Чтобы включить логическую репликацию в файле postgresql.config, задайте параметры, приведенные ниже.

  * wal_level = **logical**
  * max_replication_slots = [количество слотов], рекомендуемое значение — до **5 слотов**
  * max_wal_senders = [количество параллельных задач]. Параметр max_wal_senders задает число параллельных задач, которые можно выполнить: рекомендуемый параметр — до **10 задач**

> [!IMPORTANT]
> Все таблицы в существующей базе данных должны иметь первичный ключ, чтобы гарантировать, что изменения можно синхронизировать с целевой базой данных.

## <a name="migrate-the-sample-schema"></a>Перенос примера схемы

Чтобы подготовить все объекты базы данных, такие как схемы таблицы, индексы и хранимые процедуры, нам нужно извлечь схему из исходной базы данных и применить ее к нужной базе данных.

1. Используйте команду pg_dump -s, чтобы создать схемы файла дампа для базы данных.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Например, чтобы создать файл дампа схемы для базы данных **двдрентал** , выполните следующие действия.

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Дополнительные сведения об использовании программы pg_dump см. в примерах руководства о [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Создайте пустую базу данных в целевой среде, которая является Базой данных Azure для PostgreSQL.

    Дополнительные сведения о подключении и создании базы данных см. в статье Создание сервера [базы данных Azure для PostgreSQL на портал Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) или [Создание сервера базы данных Azure для PostgreSQL-Scale (Цитус) в портал Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Экземпляр базы данных Azure для PostgreSQL-Scale (Цитус) имеет только одну базу данных: **Цитус**.

3. Импортируйте схемы в целевую базу данных, созданную путем восстановления схемы файла дампа.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Например:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Чтобы извлечь скрипт удаления внешнего ключа и добавить его в место назначения (база данных Azure для PostgreSQL), в PgAdmin или в PSQL, выполните следующий скрипт.

   > [!IMPORTANT]
   > Внешние ключи в схеме приведут к сбою начальной загрузки и непрерывной синхронизации миграции.

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

5. Запустите сценарий удаления внешнего ключа (второй столбец) в результате запроса.

6. Чтобы отключить триггеры в целевой базе данных, выполните приведенный ниже сценарий.

   > [!IMPORTANT]
   > Триггеры (INSERT или Update) в данных применяют целостность данных в целевом объекте перед репликацией данных из источника. В результате рекомендуется отключить триггеры во всех таблицах **целевого объекта** во время миграции, а затем повторно включить триггеры после завершения миграции.

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Создание экземпляра DMS

1. На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. На экране **Создание службы миграции** укажите имя, подписку, новую или существующую группу ресурсов и расположение службы.

4. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service с доступом к исходному серверу PostgreSQL и целевому экземпляру базы данных Azure для PostgreSQL.

    Дополнительные сведения о создании виртуальной сети в портал Azure см. в статье [Создание виртуальной сети с помощью портал Azure](https://aka.ms/DMSVnet).

5. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    ![Настройка параметров экземпляра Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Выберите **проверить и создать** , чтобы создать службу.

   Создание службы завершится в течение примерно 10 – 15 минут.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. На экране **службы миграции баз данных Azure** найдите имя созданного экземпляра Azure Database Migration Service, выберите экземпляр, а затем выберите + **Новый проект миграции**.

3. На экране **Новый проект миграции** укажите имя проекта, в текстовом поле **Тип исходного сервера** выберите **PostgresSQL**, в текстовом поле **тип целевого сервера** выберите **база данных Azure для PostgreSQL**.

4. В разделе **Выберите тип действия** выберите **Миграция данных по сети**.

    ![Создание проекта Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Кроме того, вы можете выбрать **Создать только проект**, чтобы создать проект миграции для отложенного выполнения.

5. Выберите **сохранить**, запишите требования для успешного использования Azure Database Migration Service для переноса данных, а затем выберите **создать и запустить действие**.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **добавить сведения об источнике** укажите сведения о соединении для исходного экземпляра PostgreSQL.

    ![Экран добавления сведений об источнике](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Нажмите кнопку **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1. На экране " **сведения о целевом объекте** " укажите сведения о соединении для сервера целевого масштабирования (Цитус), который является предварительно подготовленным экземпляром средства масштабирования (Цитус), к которому была развернута схема **DVD напрокат** с помощью pg_dump.

    ![Экран сведений о целевом объекте](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится база данных с тем же именем, что у исходной базы данных, Azure Database Migration Service по умолчанию выберет целевую базу данных.

    ![Экран "связать с целевыми базами данных"](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Нажмите кнопку **сохранить**, а затем на экране **параметры миграции** примите значения по умолчанию.

    ![Экран параметров миграции](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Экран сводки миграции](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

    Откроется окно действие миграции, и **состояние** действия должно обновиться, чтобы оно отображалось как **выполняющееся резервное копирование**.

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажмите кнопку **Обновить**, чтобы обновить содержимое экрана, пока **состояние** миграции не поменяется на **Завершено**.

     ![Мониторинг процесса миграции](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. По завершении миграции в разделе **имя базы данных**выберите конкретную базу данных, чтобы перейти к состоянию миграции для **полной загрузки данных** и **добавочной синхронизации данных** .

   > [!NOTE]
   > В разделе **полной загрузки данных** отображается состояние начальной загрузки, а в разделе **добавочной синхронизации данных** — состояние отслеживания измененных данных (CDC).

     ![Полные сведения о загрузке данных](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Сведения о добавочной синхронизации данных](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции

После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

2. Подождите, пока счетчик **ожидающих изменений** отобразит значение **0** , чтобы убедиться, что все входящие транзакции в базу данных источника остановлены, установите флажок **подтвердить** и нажмите кнопку **Применить**.

    ![Завершение прямую миграцию экрана](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Когда состояние переноса базы данных изменится на **завершено**, Подключите свои приложения к новому целевому экземпляру базы данных Azure для PostgreSQL.

## <a name="next-steps"></a>Следующие шаги

* Сведения об известных проблемах, ограничениях при выполнении сетевой миграции в Базу данных Azure для PostgreSQL см. в [этой](known-issues-azure-postgresql-online.md) статье.
* См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Общие сведения о Базе данных Azure для PostgreSQL см. в статье [Что такое база данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
