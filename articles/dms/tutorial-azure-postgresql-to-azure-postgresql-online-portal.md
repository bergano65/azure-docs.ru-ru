---
title: Руководство по Миграция из одной Базы данных Azure для PostgreSQL в другую Базу данных Azure для PostgreSQL по сети с помощью портала Azure
titleSuffix: Azure Database Migration Service
description: Узнайте, как выполнить миграцию по сети из одной Базы данных Azure для PostgreSQL в другую Базу данных Azure для PostgreSQL с помощью Azure Database Migration Service и портала Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 713b1698bff703507f46e1a8f76c6be385f41ec5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282475"
---
# <a name="tutorial-migrate-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server-or-hyperscale-citus-online-using-dms-via-the-azure-portal"></a>Руководство по Миграция из Базы данных Azure для PostgreSQL (Отдельный сервер) в Базу данных Azure для PostgreSQL (Отдельный сервер или Гипермасштабирование (Citus)) по сети с помощью портала Azure

Azure Database Migration Service позволяет переносить базы данных из экземпляра [Базы данных Azure для PostgreSQL (Отдельный сервер)](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) в [Базу данных Azure для PostgreSQL (Отдельный сервер или Гипермасштабирование (Citus))](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) с минимальным простоем. В рамках этого руководства вы перенесете пример базы данных **Прокат DVD** из Базы данных Azure для PostgreSQL версии 10 в Базу данных Azure для PostgreSQL (Гипермасштабирование (Citus)) с помощью действия миграции по сети в Azure Database Migration Service.

В этом руководстве вы узнаете, как:
> [!div class="checklist"]
>
> * перенести пример схемы с помощью служебной программы pg_dump;
> * Создайте экземпляр Azure Database Migration Service.
> * создать проект миграции в Azure Database Migration Service;
> * выполнение миграции.
> * Мониторинг миграции.
> * выполнить переключение после миграции.

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум". Мы шифруем диск для защиты данных от кражи при миграции.

> [!IMPORTANT]
> Чтобы процесс миграции был выполнен без проблем, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

> [!IMPORTANT]
> Миграцию из Базы данных Azure для PostgreSQL поддерживает PostgreSQL 10 и более поздних версий. Кроме того, с помощью этого руководства вы можете перенести базы данных из одного экземпляра Базы данных Azure для PostgreSQL в другой экземпляр Базы данных Azure для PostgreSQL или в экземпляр с Гипермасштабированием (Citus).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Проверьте [состояние поддержки сценариев миграции в Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status), где описаны допустимые комбинации миграций и версий. 
* Существующая [База данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/) 10 и более поздней версии, где развернута база данных **Прокат DVD**. Azure Database Migration Service не поддерживает миграцию из Базы данных Azure для PostgreSQL 9.5 или 9.6.

    Также обратите внимание, что версия целевой Базы данных Azure для PostgreSQL не может быть более ранней, чем версия исходного экземпляра. Например, базу данных PostgreSQL 10 можно перенести в Базу данных Azure для PostgreSQL 10 или 11, но не в Базу данных Azure для PostgreSQL 9.6.

* Создайте [сервер Базы данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) или [сервер базы данных Azure для PostgreSQL (Гипермасштабирование (Citus))](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal), на который будете переносить данные.
* Создайте виртуальную сеть Azure для Azure Database Migration Service, используя модель развертывания с помощью Azure Resource Manager. Дополнительные сведения см. в статье [Документация по виртуальной сети](https://docs.microsoft.com/azure/virtual-network/), где особое внимание стоит уделить кратким руководствам с пошаговыми инструкциями.

* Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие входящие порты для Azure Database Migration Service: 443, 53, 9354, 445, 12000. См. дополнительные сведения о [фильтрации трафика, предназначенного для виртуальной сети, с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для исходной Базы данных Azure для PostgreSQL, чтобы предоставить Azure Database Migration Service доступ к исходным базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для Базы данных Azure для PostgreSQL, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
* Задайте следующие параметры сервера в экземпляре Базы данных Azure для PostgreSQL, который используется в качестве источника:

  * max_replication_slots = [количество слотов], рекомендуемое значение — до **5 слотов**
  * max_wal_senders = [количество параллельных задач]. Параметр max_wal_senders задает число параллельных задач, которые можно выполнить: рекомендуемый параметр — до **10 задач**

> [!NOTE]
> Указанные выше параметры сервера являются статическими. Для вступления их в силу потребуется перезагрузить этот экземпляр Базы данных Azure для PostgreSQL. Дополнительные сведения м. в статье о [настройке параметров сервера в Базе данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal).

> [!IMPORTANT]
> Чтобы поддерживать синхронизацию в целевую базу данных, все таблицы в существующей базе данных должны иметь первичный ключ.

## <a name="migrate-the-sample-schema"></a>Перенос примера схемы

Чтобы подготовить все объекты базы данных, такие как схемы таблицы, индексы и хранимые процедуры, нам нужно извлечь схему из исходной базы данных и применить ее к нужной базе данных.

1. Используйте команду pg_dump -s, чтобы создать схемы файла дампа для базы данных.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Например, для создания файла дампа схемы для базы данных **dvdrental** выполните команду:

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Дополнительные сведения об использовании программы pg_dump см. в примерах руководства о [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Создайте пустую базу данных в целевой среде, которая является Базой данных Azure для PostgreSQL.

    Дополнительные сведения о подключении к базе данных и о создании базы данных см. в кратких руководствах [Создание сервера Базы данных Azure для PostgreSQL с помощью портала Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) или [Создание серверной группы Гипермасштабирования (Citus) на портале Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Экземпляр Базы данных Azure для PostgreSQL (Гипермасштабирование (Citus)) содержит только одну базу данных с именем **citus**.

3. Импортируйте схемы в целевую базу данных, созданную путем восстановления схемы файла дампа.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Пример:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Выполните следующий сценарий в PgAdmin или psql, чтобы извлечь внешний ключ сценария удаления и добавить его в целевом экземпляре (База данных Azure для PostgreSQL).

   > [!IMPORTANT]
   > Внешние ключи в схеме приводят к сбою начальной загрузки и непрерывной синхронизации в процессе миграции.

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

6. Чтобы отключить триггеры в целевой базе данных, выполните приведенный ниже скрипт.

   > [!IMPORTANT]
   > Триггер в данных (вставки или обновления) поддерживает целостность данных в целевом объекте перед репликацией данных из источника. Поэтому мы рекомендуем отключить триггеры во всех таблицах **в целевом объекте** на период миграции, а затем снова включить триггеры по ее завершении.

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
  
3. На экране **Создание службы миграции** укажите имя службы, подписку, новую или существующую группу ресурсов, а также расположение службы.

4. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру сервера PostgreSQL и целевому экземпляру Базы данных Azure для PostgreSQL.

    См. статью [Краткое руководство. Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

5. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    ![Настройка параметров экземпляра Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Щелкните **Просмотреть и создать**, чтобы создать службу.

   Создание службы потребует примерно 10 – 15 минут.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

      ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. На экране **Службы Azure Database Migration Service** найдите и выберите имя созданного экземпляра Azure Database Migration Service, а затем щелкните элемент **Новый проект миграции**.

3. На экране **Новый проект миграции** задайте имя проекта, в текстовом поле **Тип исходного сервера** выберите элемент **PostgreSQL**, а в текстовом поле **Тип целевого сервера** — элемент **База данных Azure для PostgreSQL**.
    > [!NOTE]
    > Выберите элемент **PostgreSQL** в поле **Тип исходного сервера**, даже если исходным является экземпляр **Базы данных Azure для PostgreSQL**.  

4. В разделе **Выберите тип действия** выберите **Миграция данных по сети**.

    ![Создание проекта Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Кроме того, вы можете выбрать **Создать только проект**, чтобы создать проект миграции для отложенного выполнения.

5. Щелкните элемент **Сохранить**, изучите требования для успешного использования Azure Database Migration Service для переноса данных, а затем щелкните элемент **Создать и выполнить действие**.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. В окне **Добавление сведений об источнике** укажите сведения о подключении для исходного экземпляра PostgreSQL.

    ![Экран добавления сведений об источнике](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Такие сведения, как имя сервера, порт сервера, имя базы данных и т. д. можно найти на портале **Базы данных Azure для PostgreSQL**.

2. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1. На экране **Сведения о целевом объекте** укажите сведения для подключения к целевому серверу Гипермасштабирования (Citus), в качестве которого используется подготовленный ранее экземпляр Гипермасштабирования (Citus), где вы развернули схему **DVD** с помощью команды pg_dump.

    ![Экран сведений о целевом объекте](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Кроме того, вы можете выполнить миграцию из экземпляра Базы данных Azure для PostgreSQL в другой экземпляр Базы данных Azure для PostgreSQL (Отдельный сервер) или на сервер с Гипермасштабированием (Citus).

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится база данных с тем же именем, что у исходной базы данных, Azure Database Migration Service по умолчанию выберет целевую базу данных.

    ![Экран сопоставления с целевыми базами данных](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Щелкните элемент **Сохранить**, а затем на экране **Параметры миграции** подтвердите значения по умолчанию.

    ![Экран параметров миграции](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Экран сводных данных по миграции](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

    Откроется окно "Действие миграции", а для действия в поле **Состояние** отобразится надпись **Выполняется резервное копирование**.

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажмите кнопку **Обновить**, чтобы обновить содержимое экрана, пока **состояние** миграции не поменяется на **Завершено**.

     ![Мониторинг миграции](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Когда миграция завершится, выберите в столбце **Имя базы данных** конкретную базу данных, чтобы узнать состояние миграции для операций **полной загрузки данных** и **добавочной синхронизации данных**.

   > [!NOTE]
   > В разделе **полной загрузки данных** отображается состояние начальной загрузки, а в разделе **добавочной синхронизации данных** — состояние отслеживания измененных данных (CDC).

     ![Сведения о полной загрузке данных](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Сведения о добавочной синхронизации данных](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции

После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

2. Подождите, пока счетчик **Ожидающие изменения** не дойдет до **0**, что означает полное прекращение всех входящих транзакций в исходной базе данных, затем установите флажок **Подтвердить** и щелкните элемент **Применить**.

    ![Экран завершения миграции](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Когда состояние переноса базы данных изменится на **Завершено**, подключите свои приложения к новому целевому экземпляру Базы данных Azure для PostgreSQL.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об известных проблемах, ограничениях при выполнении сетевой миграции в Базу данных Azure для PostgreSQL см. в [этой](known-issues-azure-postgresql-online.md) статье.
* См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Общие сведения о Базе данных Azure для PostgreSQL см. в статье [Что такое база данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
