---
title: Руководство. Миграция базы данных Azure для PostgreSQL в базу данных Azure для PostgreSQL Online через портал Azure
titleSuffix: Azure Database Migration Service
description: Узнайте, как выполнить миграцию в сети с одной базы данных Azure для PostgreSQL на другую службу Azure для PostgreSQL с помощью Azure Database Migration Service через портал Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 6a5415e12a5a063790077eeefdc9ea4d1487d68b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100461"
---
# <a name="tutorial-migrate-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server-or-hyperscale-citus-online-using-dms-via-the-azure-portal"></a>Руководство. Миграция базы данных Azure для PostgreSQL-Single Server в базу данных Azure для PostgreSQL-Single Server или (Цитус) Online с помощью DMS через портал Azure

Вы можете использовать Azure Database Migration Service, чтобы перенести базы [данных Azure для экземпляра PostgreSQL-Single Server](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) в [Цитус в экземпляре базы данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) с минимальным временем простоя. В этом руководстве вы переносите образец базы данных " **Аренда DVD** " из базы данных Azure для PostgreSQL V10 в режим масштабирования (Цитус) в базе данных Azure для PostgreSQL с помощью действия "миграция в сети" в Azure Database Migration Service.

В этом руководстве вы узнаете, как:
> [!div class="checklist"]
>
> * Перенесите образец схемы с помощью служебной программы pg_dump.
> * Создайте экземпляр Azure Database Migration Service.
> * Создайте проект миграции в Azure Database Migration Service.
> * выполнение миграции.
> * Мониторинг миграции.
> * Выполните миграцию прямую миграцию.

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум". Шифрование диска для предотвращения кражи данных во время процесса миграции

> [!IMPORTANT]
> Чтобы процесс миграции был выполнен без проблем, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

> [!IMPORTANT]
> Миграция из базы данных Azure для PostgreSQL поддерживается для PostgreSQL версии 10 и более поздних версий. Кроме того, с помощью этого руководства можно выполнить миграцию с одного экземпляра базы данных Azure для PostgreSQL на другой экземпляр базы данных Azure для экземпляра PostgreSQL или в экземпляре масштабирования (Цитус).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Проверьте [состояние сценариев миграции, поддерживаемых Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status) для поддерживаемых комбинаций миграции и версий. 
* Существующий экземпляр [базы данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/) версии 10 и более поздних версий с базой данных **аренды DVD** . Azure Database Migration Service не поддерживает миграцию из базы данных Azure для PostgreSQL 9,5 или 9,6.

    Также обратите внимание, что Целевая версия базы данных Azure для PostgreSQL должна быть больше, чем локальная версия PostgreSQL. Например, PostgreSQL 10 можно перенести в базу данных Azure для PostgreSQL 10 или 11, но не в базу данных Azure для PostgreSQL 9,6.

* [Создайте сервер базы данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) или [Создайте сервер базы данных Azure для PostgreSQL-Scale (Цитус)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal) в качестве целевого сервера базы данных для переноса данных в.
* Создайте виртуальная сеть Microsoft Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager. Дополнительные сведения о создании виртуальной сети см. в [документации по виртуальной сети](https://docs.microsoft.com/azure/virtual-network/), особенно в кратком руководстве, где приведены пошаговые инструкции.

* Убедитесь, что правила группы безопасности сети (NSG) для виртуальной сети не блокируют следующие порты входящего трафика для Azure Database Migration Service: 443, 53, 9354, 445, 12000. Дополнительные сведения о фильтрации трафика NSG в виртуальной сети см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для источника "база данных Azure для PostgreSQL", чтобы разрешить Azure Database Migration Service доступ к исходным базам данных. Укажите диапазон подсети виртуальной сети, используемый для Azure Database Migration Service.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для целевой базы данных Azure для PostgreSQL, чтобы разрешить Azure Database Migration Service доступ к целевым базам данных. Укажите диапазон подсети виртуальной сети, используемый для Azure Database Migration Service.
* Задайте следующие параметры сервера в экземпляре базы данных Azure для PostgreSQL, который используется в качестве источника:

  * max_replication_slots = [количество слотов], рекомендуемое значение — до **5 слотов**
  * max_wal_senders = [количество параллельных задач]. Параметр max_wal_senders задает число параллельных задач, которые можно выполнить: рекомендуемый параметр — до **10 задач**

> [!NOTE]
> Указанные выше параметры сервера являются статическими и для их вступления в силу потребуется перезагрузка базы данных Azure для экземпляра PostgreSQL. Дополнительные сведения о включении параметров сервера см. в статье [Настройка параметров сервера базы данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal).

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
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
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

    Например.

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Чтобы извлечь скрипт удаления внешнего ключа и добавить его в место назначения (база данных Azure для PostgreSQL), в PgAdmin или в PSQL, выполните следующий скрипт.

   > [!IMPORTANT]
   > Внешние ключи в схеме приведут к сбою начальной загрузки и непрерывной синхронизации миграции.

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

6. Чтобы отключить триггеры в целевой базе данных, выполните приведенный ниже сценарий.

   > [!IMPORTANT]
   > Триггеры (INSERT или Update) в данных применяют целостность данных в целевом объекте перед репликацией данных из источника. В результате рекомендуется отключить триггеры во всех таблицах **целевого объекта** во время миграции, а затем повторно включить триггеры после завершения миграции.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Создание экземпляра DMS

1. На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. На экране **Создание службы миграции** укажите имя, подписку, новую или существующую группу ресурсов и расположение службы.

4. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service с доступом к исходному серверу PostgreSQL и целевому экземпляру базы данных Azure для PostgreSQL.

    Дополнительные сведения о создании виртуальной сети в портал Azure см. в статье [Создание виртуальной сети с помощью портал Azure](https://aka.ms/DMSVnet).

5. Выберите ценовую категорию.

    Дополнительные сведения о затратах и ценовых категориях см. на [странице с ценами](https://aka.ms/dms-pricing).

    ![Настройка параметров экземпляра Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Выберите **проверить и создать** , чтобы создать службу.

   Создание службы завершится в течение примерно 10 – 15 минут.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. На экране **службы миграции баз данных Azure** найдите имя созданного экземпляра Azure Database Migration Service, выберите экземпляр, а затем выберите + **Новый проект миграции**.

3. На экране **Новый проект миграции** укажите имя проекта, в текстовом поле **Тип исходного сервера** выберите **PostgreSQL**, в текстовом поле **тип целевого сервера** выберите **база данных Azure для PostgreSQL**.
    > [!NOTE]
    > Выберите **PostgreSQL** в качестве **типа исходного сервера** , даже если исходный сервер является экземпляром **базы данных Azure для PostgreSQL** .  

4. В разделе **Выбор типа действия** выберите **Перенос данных в сети**.

    ![Создание проекта Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Кроме того, вы можете выбрать **Создать только проект**, чтобы создать проект миграции для отложенного выполнения.

5. Выберите **сохранить**, запишите требования для успешного использования Azure Database Migration Service для переноса данных, а затем выберите **создать и запустить действие**.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **добавить сведения об источнике** укажите сведения о соединении для исходного экземпляра PostgreSQL.

    ![Экран добавления сведений об источнике](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Такие сведения, как "имя сервера", "порт сервера", "имя базы данных" и т. д., можно найти на портале **базы данных Azure для PostgreSQL** .

2. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1. На экране " **сведения о целевом объекте** " укажите сведения о соединении для сервера целевого масштабирования (Цитус), который является предварительно подготовленным экземпляром средства масштабирования (Цитус), к которому была развернута схема **DVD напрокат** с помощью pg_dump.

    ![Экран сведений о целевом объекте](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Вы можете выполнить миграцию из экземпляра базы данных Azure для PostgreSQL в другую базу данных Azure для экземпляра PostgreSQL с одним сервером или с помощью сервера масштабирования (Цитус).

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится база данных с тем же именем, что у исходной базы данных, Azure Database Migration Service по умолчанию выберет целевую базу данных.

    ![Экран "связать с целевыми базами данных"](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Нажмите кнопку **сохранить**, а затем на экране **параметры миграции** примите значения по умолчанию.

    ![Экран параметров миграции](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Экран сводки миграции](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

    Откроется окно действие миграции, и **состояние** действия должно обновиться, чтобы оно отображалось как **выполняющееся резервное копирование**.

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажмите кнопку **Обновить**, чтобы обновить содержимое экрана, пока **состояние** миграции не поменяется на **Завершено**.

     ![Мониторинг процесса миграции](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. По завершении миграции в разделе **имя базы данных**выберите конкретную базу данных, чтобы перейти к состоянию миграции для **полной загрузки данных** и **добавочной синхронизации данных** .

   > [!NOTE]
   > В разделе **полной загрузки данных** отображается состояние начальной загрузки, а в разделе **добавочной синхронизации данных** — состояние отслеживания измененных данных (CDC).

     ![Полные сведения о загрузке данных](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Сведения о добавочной синхронизации данных](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции

После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

2. Подождите, пока счетчик **ожидающих изменений** отобразит значение **0** , чтобы убедиться, что все входящие транзакции в базу данных источника остановлены, установите флажок **подтвердить** и нажмите кнопку **Применить**.

    ![Завершение прямую миграцию экрана](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Когда состояние переноса базы данных изменится на **завершено**, Подключите свои приложения к новому целевому экземпляру базы данных Azure для PostgreSQL.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об известных проблемах, ограничениях при выполнении сетевой миграции в Базу данных Azure для PostgreSQL см. в [этой](known-issues-azure-postgresql-online.md) статье.
* См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Общие сведения о Базе данных Azure для PostgreSQL см. в статье [Что такое база данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
