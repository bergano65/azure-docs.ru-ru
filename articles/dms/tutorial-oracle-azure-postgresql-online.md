---
title: Руководство по Выполнение сетевого переноса Oracle в Базу данных Azure для PostgreSQL с помощью Azure Database Migration Service | Документация Майкрософт
description: Из этой статьи вы узнаете, как выполнить сетевой перенос из Oracle в локальной среде или на виртуальной машине в Базу данных Azure для PostgreSQL с помощью Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 0b3af3d29e6e938f0301d751a79170c7c1964b45
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243800"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Руководство по Сетевой перенос Oracle в Базу данных Azure для PostgreSQL с помощью DMS (предварительная версия)

Службу Azure Database Migration Service можно использовать для переноса баз данных Oracle из локальной среды или виртуальных машин в [Базу данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/) с минимальным временем простоя. Другими словами, миграцию можно завершить с минимальным временем простоя для приложения. В этом руководстве выполняется перенос примера базы данных **отдела кадров** из экземпляра Oracle 11g, размещенного в локальной среде или на виртуальной машине, в Базу данных Azure для PostgreSQL с помощью операции сетевого переноса в Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
>
> * оценка мероприятий по миграции с помощью средства ora2pg;
> * перенос примера схемы с помощью средства ora2pg;
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с использованием Azure Database Migration Service.
> * выполнение миграции.
> * мониторинг миграции.

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум".

> [!IMPORTANT]
> Чтобы процесс миграции был выполнен без проблем, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

В этой статье описывается выполнение сетевого переноса из Oracle в Базу данных Azure для PostgreSQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

* Скачайте и установите [Oracle 11g с выпуском 2 (Standard Edition, Standard Edition One или Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Скачайте базу данных **отдела кадров** из [этого примера](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Скачайте и установите ora2pg для ОС [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) или [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Создайте экземпляр в Базе данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Подключитесь к этому экземпляру и создайте базу данных, следуя инструкциям из этого [документа](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Создайте виртуальную сеть Azure для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Дополнительные сведения о создании виртуальной сети приведены в [документации по виртуальным сетям](https://docs.microsoft.com/azure/virtual-network/). В частности, уделите внимание кратким руководствам с пошаговыми инструкциями.

  > [!NOTE]
  > Если вы используете ExpressRoute с пиринговой связью с сетью корпорации Майкрософт, во время настройки виртуальной сети добавьте в подсеть, в которой будет подготовлена служба, следующие [конечные точки](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview):
  > * целевую конечную точку базы данных (например, конечная точка SQL, конечная точка Cosmos DB и т. д.);
  > * конечную точку службы хранилища;
  > * конечную точку служебной шины.
  >
  > Такая конфигурация вызвана тем, что у Azure Database Migration Service нет подключения к Интернету.

* Убедитесь, что правила группы безопасности сети для виртуальной сети не блокируют следующие входящие порты для Azure Database Migration Service: 443, 53, 9354, 445, 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Plan virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) (Планирование виртуальных сетей).
* Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу Oracle. По умолчанию это TCP-порт 1521.
* Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
* Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) на уровне сервера для Базы данных Azure для PostgreSQL, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
* Разрешите доступ к исходным базам данных Oracle.

  > [!NOTE]
  > Для подключения пользователя к источнику данных Oracle требуется роль DBA.

  * Журналы повторной архивации нужны для добавочной синхронизации измененных данных в Azure Database Migration Service. Чтобы настроить источник Oracle, выполните следующие действия:
    * Войдите в систему с правами SYSDBA, выполнив следующую команду:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Завершите работу экземпляра базы данных, выполнив следующую команду:

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Дождитесь подтверждения `'ORACLE instance shut down'`.

    * Запустите новый экземпляр и подключите базу данных (но не открывайте ее), чтобы включить или отключить архивирование, выполнив следующую команду:

      ```
      STARTUP MOUNT;
      ```

      База данных должна быть подключена. Дождитесь подтверждения "Запущен экземпляр Oracle".

    * Измените режим архивации базы данных, выполнив следующую команду:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Откройте базу данных для обычной работы, выполнив следующую команду:

      ```
      ALTER DATABASE OPEN;
      ```

      Возможно, потребуется перезапустить систему, чтобы отобразился файл ARC.

    * Чтобы проверить, так ли это, выполните следующую команду:

      ```
      SELECT log_mode FROM v$database;
      ```

      Вы должны получить ответ `'ARCHIVELOG'`. Если получен ответ `'NOARCHIVELOG'`, значит требование не соблюдается.

  * Включите дополнительное ведение журнала для репликации, выполнив любую из следующих процедур.

    * **Вариант 1**.
      Измените уровень дополнительного ведения журнала для базы данных, чтобы охватить все таблицы с первичными ключами и уникальными индексами. Запрос обнаружения будет возвращать ответ `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Измените уровень дополнительного ведения журнала на уровне таблиц. Выполняйте его только для таблиц, в которых изменяются данные, но нет первичных или уникальных индексов.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Вариант 2**.
      Измените уровень дополнительного ведения журнала для базы данных, чтобы охватить все таблицы, и запрос обнаружения возвратит ответ `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Измените уровень дополнительного ведения журнала на уровне таблиц. Выполните действия по описанной ниже логике, чтобы запустить только одну инструкцию для каждой таблицы.

      Если таблица имеет первичный ключ:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Если таблица имеет уникальный индекс:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Для остальных таблиц используйте следующую команду:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Чтобы проверить, так ли это, выполните следующую команду:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Вы должны получить ответ `'YES'`.

> [!IMPORTANT]
> Для общедоступной предварительной версии этого сценария Azure Database Migration Service поддерживает Oracle версии 10g или 11g. Клиентам, которые используют версию Oracle 12c или более позднюю, следует учитывать, что для подключения драйвера ODBC к Oracle требуется протокол аутентификации не ниже версии 8. Для источника Oracle версии 12c или более поздней необходимо настроить протокол аутентификации следующим образом:
>
> * Обновите файл SQLNET.ORA:
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * Перезагрузите компьютер, чтобы изменения настроек вступили в силу.
> * Измените пароли для существующих пользователей:
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   Дополнительные сведения см. на [этой странице](http://www.dba-oracle.com/t_allowed_login_version_server.htm).
>
> Не забывайте также, что изменение протокола аутентификации может повлиять на аутентификацию клиента.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Оценка мероприятий для перехода с Oracle на Базу данных Azure для PostgreSQL

Мы рекомендуем использовать ora2pg для оценки требуемых мероприятий с целью перехода с Oracle на Базу данных Azure для PostgreSQL. С помощью директивы `ora2pg -t SHOW_REPORT` создайте отчет, перечисляющий все объекты Oracle с оценкой стоимости миграции (в днях работы разработчика) и некоторые объекты базы данных, для которых потребуется особое внимание в процессе миграции.

Большинство клиентов тратят немало времени на изучение отчета об оценке и планирование мероприятий по миграции в автоматическом и ручном режиме.

Чтобы настроить и запустить ora2pg для подготовки отчета об оценке, воспользуйтесь разделом **Premigration: Assessment** (Перед миграцией: оценка) в [учебнике по переносу Oracle в Базу данных Azure для PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Пример создаваемого ora2pg отчета об оценке можно получить [здесь](http://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Экспорт схемы Oracle

Мы рекомендуем использовать ora2pg для преобразования схемы Oracle и других объектов Oracle (типы, процедуры, функции, и т. д.) в схему, совместимую с Базой данных Azure для PostgreSQL. ora2pg предоставляет множество директив для предварительного определения некоторых типов данных. Например, вы можете использовать директиву `DATA_TYPE`, чтобы заменить все NUMBER(*,0) на тип bigint вместо NUMERIC(38).

С помощью ora2pg можно выполнить экспорт всех объектов базы данных в формат SQL-файлов. Вы сможете просмотреть SQL-файлы, а затем импортировать их в Базу данных Azure для PostgreSQL с помощью psql или выполнить скрипт .sql в PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Например:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Чтобы настроить и запустить ora2pg для преобразования схемы, воспользуйтесь разделом **Migration: Schema and data** (Миграция: схема и данные) в [учебнике по переносу Oracle в Базу данных Azure для PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Настройка схемы в Базе данных Azure для PostgreSQL

По умолчанию Oracle хранит значения schema.table.column в верхнем регистре, а PostgreSQL использует для schema.table.column нижний регистр. Чтобы Azure Database Migration Service могла начать перемещение данных из Oracle в данных Базу данных Azure для PostgreSQL, для schema.table.column необходимо настроить тот же формат регистра, что и в исходной базе данных Oracle.

Например, если в источнике Oracle используется схема вида "HR"."EMPLOYEES"."EMPLOYEE_ID", этот же формат нужно указать и для PostgreSQL.

Чтобы проверить совпадение форматов для schema.table.column в Oracle и Базе данных Azure для PostgreSQL, мы рекомендуем выполнить следующие действия.

> [!NOTE]
> Но вы можете выбрать любой другой подход, чтобы настроить схему в верхнем регистре. Мы стремимся улучшить и автоматизировать этот шаг.

1. Экспортируйте схему с помощью ora2pg в нижнем регистре. В SQL-скрипт создания таблиц вручную добавьте схему в верхнем регистре (SCHEMA).
2. Импортируйте все остальные объекты Oracle, в том числе триггеры, последовательности, процедуры, типы и функции, в Базу данных Azure для PostgreSQL.
3. Чтобы преобразовать TABLE и COLUMN в верхний регистр, выполните следующий скрипт:

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* Удалите внешний ключ в целевой базе данных, чтобы выполнить полную загрузку данных. В разделе **Перенос примера схемы** [этой статьи](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) приводится скрипт, который можно использовать для удаления внешнего ключа.
* Примените Azure Database Migration Service для запуска полной загрузки и синхронизации.
* Когда данные в целевом экземпляре Базы данных Azure для PostgreSQL будут приведены в соответствие с источником, выполните прямой перенос базы данных в Azure Database Migration Service.
* Чтобы перевести элементы SCHEMA, TABLE и COLUMN в нижний регистр (если вы решили использовать этот формат для запросов приложения к Базе данных Azure для PostgreSQL), выполните следующий скрипт:

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

   ![Отображение подписок на портале](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Создание экземпляра DMS

1. На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Создайте виртуальную сеть или выберите имеющуюся.

    Виртуальная сеть предоставляет для Azure Database Migration Service доступ к исходному экземпляру Oracle и целевому экземпляру Базы данных Azure для PostgreSQL.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Краткое руководство. Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

5. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    ![Настройка параметров экземпляра Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

    ![Поиск всех экземпляров Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра Azure Database Migration Service и выберите его.

    ![Поиск экземпляра службы миграции базы данных Azure](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Выберите **+ Новый проект миграции**.
4. На экране **Новый проект миграции** задайте имя проекта, в текстовом поле **Тип исходного сервера** выберите **Oracle**, а в текстовом поле **Тип целевого сервера** — **База данных Azure для PostgreSQL**.
5. В разделе **Выберите тип действия** выберите **Миграция данных по сети**.

   ![Создание проекта Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Кроме того, вы можете выбрать **Создать только проект**, чтобы создать проект миграции для отложенного выполнения.

6. Выберите **Сохранить**, изучите требования для успешного использования Azure Database Migration Service для сетевой миграции, а затем щелкните **Создать и выполнить действие**.

## <a name="specify-source-details"></a>Указание сведений об источнике

* В окне **Добавление сведений об источнике** укажите сведения о подключении для исходного экземпляра Oracle.

  ![Экран добавления сведений об источнике](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Загрузка драйвера Oracle OCI

1. Выберите **Сохранить**, а затем на экране **Установка драйвера OCI** войдите в учетную запись Oracle и загрузите драйвер **instantclient-basiclite-windows.x64-12.2.0.1.0.zip**(размер: 37 128 586 байт; контрольная сумма SHA1: 865082268), который можно найти [здесь](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Поместите драйвер в общую папку.

   Убедитесь, что общий доступ к этой папке предоставляется от имени пользователя с минимальным доступом только для чтения. Azure Database Migration Service обращается к этой папке и считывает драйвер OCI для загрузки в Azure, олицетворяя пользователя с указанным именем.

   Это обязательно должна быть учетная запись пользователя Windows.

   ![Установка драйвера OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Указание сведений о цели

1. Нажмите кнопку **Сохранить**, а затем на экране **данных целевого объекта** укажите сведения о подключении для целевого сервера Базы данных Azure для PostgreSQL, то есть предварительно подготовленного экземпляра Базы данных Azure для PostgreSQL, для которого вы развернули схему **HR**.

    ![Экран сведений о целевом объекте](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится база данных с тем же именем, что у исходной базы данных, Azure Database Migration Service по умолчанию выберет целевую базу данных.

    ![Сопоставление с целевыми базами данных](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Сводка по миграции](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции

* Выберите **Запустить миграцию**.

  Появится окно действия миграции и в поле **Состояние** будет указано **Инициализация**.

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажимайте **Обновить**, чтобы периодически обновлять содержимое экрана, пока в поле **Состояние** не будет указано **Выполнение**.

     ![Состояние действия "Выполняется"](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. В столбце **Имя базы данных** выберите базу данных, чтобы получить состояние миграции для операций **полной загрузки данных** и **добавочной синхронизации данных**.

    В разделе полной загрузки данных отображается состояние начальной загрузки, а в разделе добавочной синхронизации данных — состояние отслеживания измененных данных (CDC).

     ![Состояние действия "Полная загрузка завершена"](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Состояние действия "Добавочная синхронизация данных"](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции

После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

2. Обязательно остановите все входящие транзакции в исходную базу данных. Подождите, пока в счетчике **Ожидающие изменения** появится значение **0**.

   ![Запуск прямой миграции](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Щелкните **Подтвердить**, а затем — **Apply** (Применить).
4. Когда состояние переноса базы данных изменится на **Завершено**, подключите свои приложения к новой целевой Базе данных Azure для PostgreSQL.

 > [!NOTE]
 > Так как PostgreSQL по умолчанию хранит schema.table.column в нижнем регистре, преобразуйте имена из верхнего в нижний регистр с помощью скрипта из раздела **Настройка схемы в Базе данных Azure для PostgreSQL** выше в этой статье.

## <a name="next-steps"></a>Дополнительная информация

* Сведения об известных проблемах, ограничениях при выполнении сетевой миграции в Базу данных Azure для PostgreSQL см. в [этой](known-issues-azure-postgresql-online.md) статье.
* См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
* Общие сведения о Базе данных Azure для PostgreSQL см. в статье [Что такое база данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
