---
title: "Учебник: Мигрируйте PostgreS'L в Azure DB для PostgreS'L онлайн через портал Azure"
titleSuffix: Azure Database Migration Service
description: Научитесь выполнять онлайн-миграцию из postgreS'L в базу данных Azure для PostgreS'L с помощью службы миграции баз данных Azure через портал Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: e01cc1c07d720c4743a03b5001e640f8b851dd5c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114002"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Учебник: Мигрируйте PostgreS'L в Azure DB для PostgreS'L онлайн с помощью DMS через портал Azure

Миграционная служба базы данных Azure может перенести базы данных из предварительного экземпляра PostgreS'L в [базу данных Azure для PostgreS'L](https://docs.microsoft.com/azure/postgresql/) с минимальным временем простоя в приложение. В этом руководстве выполняется миграция примера базы данных **Прокат DVD** из локального экземпляра PostgreSQL 9.6 в Базу данных Azure для PostgreSQL с помощью действия сетевой миграции в Azure Database Migration Service.

В этом руководстве описано следующее:
> [!div class="checklist"]
>
> * Мигрируйте по схеме образца, используя pg_dump утилиту.
> * создание экземпляра Azure Database Migration Service;
> * Создайте проект миграции в миграционной службе базы данных Azure.
> * выполнение миграции.
> * мониторинг миграции.
> * Выполните сокращение миграции.

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум". Мы шифруем диск для предотвращения кражи данных в процессе миграции

> [!IMPORTANT]
> Чтобы процесс миграции был выполнен без проблем, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Скачать и установить [PostgreS'L сообщество издание](https://www.postgresql.org/download/) 9.4, 9.5, 9.6, или 10. Исходная версия PostgreS'L Server должна быть 9.4, 9.5, 9.6, 10 или 11. Дополнительные сведения см. в статье [Поддерживаемые версии базы данных PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Также обратите внимание, что целевая база данных Azure для версии PostgreS'L должна быть равна или позже, чем в предприимчивой версии PostgreS'L. Например, PostgreS'L 9.6 может перейти на базу данных Azure для PostgreS'L 9.6, 10 или 11, но не в базу данных Azure для PostgreS'L 9.5.

* [Создайте базу данных Azure для сервера PostgreS'L](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) или [создайте базу данных Azure для сервера PostgreS-L - Hyperscale (Citus).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* Создайте виртуальную сеть Microsoft Azure для миграционной службы лазурных данных с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение к исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Для получения дополнительной информации о создании виртуальной сети, см [Виртуальная сеть Документация](https://docs.microsoft.com/azure/virtual-network/), и особенно быстро начать статьи с пошаговой детали.

    > [!NOTE]
    > Во время виртуальной настройки сети, если вы используете ExpressRoute с помощью сети, вглядывающейся в Microsoft, добавьте следующие [конечные точки](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) службы в подсеть, в которой будет предоставляться услуга:
    >
    > * целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
    > * конечную точку службы хранилища;
    > * конечную точку служебной шины.
    >
    > Такая конфигурация вызвана тем, что у Azure Database Migration Service нет подключения к Интернету.

* Убедитесь, что правила Группы сетевой безопасности (NSG) для виртуальной сети не блокируют следующие входящие порты связи в службу миграции базы данных Azure: 443, 53, 9354, 445, 12000. Для получения более подробной информации о виртуальной сети NSG фильтрации трафика, [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу PostgreSQL Server. По умолчанию это TCP-порт 5432.
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для Базы данных Azure для PostgreSQL, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Предоставьте диапазон подсетей виртуальной сети, используемой для миграционной службы базы данных Azure.
* Чтобы включить логическую репликацию в файле postgresql.config, задайте параметры, приведенные ниже.

  * wal_level = **logical**
  * max_replication_slots = [количество слотов], рекомендуемое значение — до **5 слотов**
  * max_wal_senders = [количество параллельных задач]. Параметр max_wal_senders задает число параллельных задач, которые можно выполнить: рекомендуемый параметр — до **10 задач**

> [!IMPORTANT]
> Все таблицы в существующей базе данных нуждаются в главном ключе, чтобы гарантировать, что изменения могут быть синхронизированы с целевой базой данных.

## <a name="migrate-the-sample-schema"></a>Перенос примера схемы

Чтобы подготовить все объекты базы данных, такие как схемы таблицы, индексы и хранимые процедуры, нам нужно извлечь схему из исходной базы данных и применить ее к нужной базе данных.

1. Используйте команду pg_dump -s, чтобы создать схемы файла дампа для базы данных.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Например, для создания файла дампа схемы для базы данных **dvdrental:**

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Дополнительные сведения об использовании программы pg_dump см. в примерах руководства о [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Создайте пустую базу данных в целевой среде, которая является Базой данных Azure для PostgreSQL.

    Подробнее о том, как подключить и создать базу данных, можно получить в статье [«Создание базы данных Azure для сервера PostgreS'L» на портале Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) или [создать базу данных Azure для сервера PostgreS-L - Hyperscale (Citus) на портале Azure.](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

    > [!NOTE]
    > Экземпляр базы данных Azure для PostgreS-L - Hyperscale (Citus) имеет только единую базу данных: **Citus**.

3. Импортируйте схемы в целевую базу данных, созданную путем восстановления схемы файла дампа.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Пример:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Чтобы извлечь скрипт отсечения иностранного ключа и добавить его в пункт назначения (База данных Azure для PostgreS'L), в PgAdmin или в psql, запустите следующий сценарий.

   > [!IMPORTANT]
   > Иностранные ключи в вашей схеме приведет к сбою начальной нагрузки и непрерывной синхронизации миграции.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Запустите сценарий удаления внешнего ключа (второй столбец) в результате запроса.

6. Чтобы отключить триггеры в целевой базе данных, запустите сценарий ниже.

   > [!IMPORTANT]
   > Триггеры (вставка или обновление) в данных обеспечивают целостность данных в целевом показателе перед реплицируемыми данными из источника. В результате рекомендуется отключить триггеры во всех таблицах **цели** во время миграции, а затем повторно включить триггеры после завершения миграции.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Поиск миграции, а затем справа **от Microsoft.DataMigration**, выберите **Регистр**.

    ![Регистрация поставщика ресурсов](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Создание экземпляра DMS

1. На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. На экране **Службы создания миграции** укажите имя, подписку, новую или существующую группу ресурсов и местоположение службы.

4. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет миграционной службе базы данных Azure доступ к исходной серверу PostgreS'L и целевой базе данных Azure для экземпляра PostgreS-L.

    Для получения дополнительной информации о том, как создать виртуальную сеть на портале Azure, смотрите статью [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

5. Выберите ценовую категорию.

    Для получения дополнительной информации о затратах и уровнях [ценообразования,](https://aka.ms/dms-pricing)см.

    ![Настройка параметров экземпляра Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Выберите **Обзор и создайте** для создания службы.

   Создание сервиса завершится примерно через 10-15 минут.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. На экране **миграционных служб базы данных Azure** ищите имя созданной службы миграции базы данных Azure, выберите экземпляр, а затем выберите **новый проект миграции.**

3. На экране **проекта «Новая миграция»** укажите имя для проекта, в текстовом поле **типа «Источник»** — выберите **PostgresS'L**в текстовом поле **типа Целевого сервера,** выберите базу **данных Azure для PostgreS'L.**

4. В разделе **«Выбрать тип активности»** выберите **миграцию данных в Интернете.**

    ![Создание проекта миграционной службы баз данных Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Кроме того, вы можете выбрать **Создать только проект**, чтобы создать проект миграции для отложенного выполнения.

5. Выберите **Сохранить,** обратите внимание на требования к успешному использованию службы миграции данных базы данных Azure для переноса данных, а затем выберите **Создание и запуск деятельности.**

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **добавления исходных данных** укажите сведения о подключении к исходной экземпляру PostgreS'L.

    ![Экран добавления сведений об источнике](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1. На **экране деталей Target** укажите детали соединения для целевого сервера Hyperscale (Citus), который является заранее подготовленным экземпляром Hyperscale (Citus), к которому была развернута схема **DVD Rentals** с помощью pg_dump.

    ![Экран сведений о целевом объекте](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится база данных с тем же именем, что у исходной базы данных, Azure Database Migration Service по умолчанию выберет целевую базу данных.

    ![Карта для целевого экрана баз данных](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Выберите **Сохранить,** а затем на экране **настроек миграции,** принять значения по умолчанию.

    ![Экран настроек миграции](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Экран резюме миграции](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

    Отображается окно активности миграции, и **статус** действия должен обновляться, чтобы отображаться как **резервное копирование в прогрессе.**

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажмите кнопку **Обновить**, чтобы обновить содержимое экрана, пока **состояние** миграции не поменяется на **Завершено**.

     ![Мониторинг процесса миграции](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Когда миграция завершена, под **названием базы данных,** выберите конкретную базу данных, чтобы добраться до статуса миграции для **полной нагрузки данных** и **инкрементных операций синхронизации данных.**

   > [!NOTE]
   > В разделе **полной загрузки данных** отображается состояние начальной загрузки, а в разделе **добавочной синхронизации данных** — состояние отслеживания измененных данных (CDC).

     ![Полная информация о загрузке данных](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Инкрементные данные синхронизации данных](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции

После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

2. Подождите, пока счетчик **изменений Pending** покажет **0,** чтобы убедиться, что все входящие транзакции в исходную базу данных остановлены, выберите флажок **Confirm,** а затем выберите **Apply.**

    ![Полный экран огранки](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Когда статус миграции базы данных отображается **завершенным,** подключите приложения к новому целевому экземпляру базы данных Azure для PostgreS'L.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об известных проблемах, ограничениях при выполнении сетевой миграции в Базу данных Azure для PostgreSQL см. в [этой](known-issues-azure-postgresql-online.md) статье.
* См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Общие сведения о Базе данных Azure для PostgreSQL см. в статье [Что такое база данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
