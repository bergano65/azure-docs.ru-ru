---
title: Руководство. Проектирование панели мониторинга для отображения данных в реальном времени с помощью Базы данных Azure для PostgreSQL с решением Hyperscale (Citus) (предварительная версия)
description: В этом учебнике показано, как создавать и заполнять распределенные таблицы и выполнять к ним запрос с помощью решения Hyperscale (Citus) (предварительная версия) в Базе данных Azure для PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 7324ab1d7aa6e42100c9c6760c17b0ea6445f21d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079459"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Руководство по Проектирование панели мониторинга для отображения данных в реальном времени с помощью Базы данных Azure для PostgreSQL с решением Hyperscale (Citus) (предварительная версия)

В этом руководстве используется решение Hyperscale (Citus) (предварительная версия) в Базе данных Azure для PostgreSQL, чтобы продемонстрировать следующее:

> [!div class="checklist"]
> * создание группы серверов Hyperscale (Citus);
> * использование служебной программы psql для создания схемы;
> * сегментирование таблиц по узлам;
> * Создание примера данных
> * выполнение сведения данных;
> * выполнение запроса к необработанным и агрегированным данным;
> * удаление устаревших данных.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Создание базы данных Azure для PostgreSQL

Чтобы создать базу данных Azure для сервера PostgreSQL, сделайте следующее:
1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
2. На странице **создания** выберите **Базы данных**, а на странице **Базы данных** щелкните **Azure Database for PostgreSQL** (База данных Azure для PostgreSQL).
3. Чтобы выполнить развертывание, нажмите кнопку **Создать** в разделе **Hyperscale (Citus) server group - PREVIEW** (Группа серверов Hyperscale (Citus) — предварительная версия).
4. Заполните форму для создания сервера, указав следующую информацию:
   - Под текстовым полем в разделе "Группа ресурсов" щелкните ссылку **Создать**. Присвойте группе ресурсов имя, например **myresourcegroup**.
   - Группе серверов присвойте имя **mydemoserver** (имя сервера сопоставляется с DNS-именем, поэтому оно должно быть глобально уникальным).
   - Администратору присвойте имя **myadmin** (оно понадобится позже для подключения к базе данных).
   - Пароль. Должен содержать минимум восемь символов из таких трех категорий: латинские прописные и строчные буквы, цифры (0–9) и другие символы (!, $, #, % и т. д.).
   - Расположение. Задайте ближайшее к пользователям расположение, чтобы предоставить им максимально быстрый доступ к данным.

   > [!IMPORTANT]
   > Указанные здесь учетные данные и пароль администратора сервера понадобятся позже в этом руководстве, чтобы войти на сервер и в его базу данных. Запомните или запишите эту информацию для последующего использования.

5. Щелкните **Настройка группы серверов**. Не изменяйте настройки в этом разделе и щелкните **Сохранить**.
6. Щелкните **Просмотр и создание**, а затем — **Создать**, чтобы подготовить сервер к работе. Подготовка занимает несколько минут.
7. Произойдет переход на страницу отслеживания развертывания. Когда состояние изменится с **Развертывание выполняется** на **Развертывание выполнено**, щелкните пункт меню **Выходные данные** в левой части страницы.
8. На странице выходных данных будет указано имя узла-координатора, кнопка рядом с которым позволяет скопировать это значение в буфер обмена. Сохраните эту информацию на будущее.

## <a name="configure-a-server-level-firewall-rule"></a>Настройка правила брандмауэра на уровне сервера

База данных Azure для службы PostgreSQL использует брандмауэр на уровне сервера. По умолчанию брандмауэр блокирует подключение любых внешних приложений и средств к серверу и к базам данных на этом сервере. Необходимо добавить правило, открывающее брандмауэр для определенного диапазона IP-адресов.

1. В разделе **Выходные данные**, в котором вы ранее скопировали имя узла-координатора, щелкните пункт меню **Обзор**.

2. В списке найдите группу масштабирования для своего развертывания и щелкните ее. (Ее имя будет иметь префикс sg-.)

3. В меню слева в разделе **Безопасность** щелкните **Брандмауэр**.

4. Щелкните ссылку **+ Добавить правило брандмауэра для текущего IP-адреса клиента**. В завершение нажмите кнопку **Сохранить**.

5. Выберите команду **Сохранить**.

   > [!NOTE]
   > Сервер PostgreSQL Azure обменивается данными через порт 5432. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 5432 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу Базы данных SQL Azure, пока ваш ИТ-отдел не откроет порт 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Подключение к базе данных с помощью psql в Cloud Shell

Теперь подключимся к серверу базы данных Azure для PostgreSQL с помощью служебной программы командной строки [psql](https://www.postgresql.org/docs/current/app-psql.html).
1. Запустите Azure Cloud Shell с помощью значка терминала в верхней области навигации.

   ![База данных Azure для PostgreSQL. Значок терминала Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. В браузере откроется служба Azure Cloud Shell, которая позволяет выполнять команды Bash.

   ![База данных Azure для PostgreSQL. Строка Bash в Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. Подключитесь к базе данных Azure для сервера PostgreSQL, выполнив команду psql в командной строке Cloud Shell. Используйте следующий формат, чтобы подключиться к серверу базы данных Azure для PostgreSQL с помощью служебной программы [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Так, следующая команда устанавливает подключение к базе данных по умолчанию с именем **citus** на сервере PostgreSQL **mydemoserver.postgres.database.azure.com**, используя учетные данные для доступа. В ответ на запрос введите пароль администратора сервера.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Использование служебной программы psql для создания схемы

Подключившись с помощью psql к Hyperscale (Citus) (предварительная версия) в Базе данных Azure для PostgreSQL, вы сможете выполнить некоторые несложные задачи. В этом руководстве показано, как выполнить прием данных из средства веб-аналитики и сведение данных для их представления в реальном времени на панели мониторинга.

Давайте создадим таблицу, в которую будут поступать необработанные данные. В окне терминала psql выполните следующие команды:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Кроме того, мы собираемся создать таблицу, в которой будут находиться поминутно агрегированные данные, и таблицу со сведениями о последнем сведении данных. В psql выполните следующую команду:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Созданные таблицы можно отобразить в виде списка с помощью этой команды psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Сегментирование таблиц по узлам

При гипермасштабируемом развертывании строки таблиц распределяются по разным узлам на основе значения столбца, назначенного пользователем. Этот столбец определяет, как данные распределяются между узлами.

Назначим столбец распределения ключом сегментирования site\_id. Выполните в psql такие функции:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Создание примера данных

Теперь наша группа серверов должна быть готова к приему данных. Следующие команды можно выполнить локально в `psql` для постоянного добавления данных.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Запрос добавляет строку примерно каждую четверть секунды. Строки сохраняются на разных рабочих узлах, как указано в столбце распределения `site_id`.

   > [!NOTE]
   > Оставьте выполняться запрос на создание данных и откройте второй сеанс psql для ввода оставшихся команд из этого руководства.
   >

## <a name="query"></a>Запрос

Гипермасштабируемое размещение позволяет обрабатывать запросы на нескольких узлах в параллельном режиме, чтобы повысить скорость. Так, база данных вычисляет агрегаты, такие как SUM и COUNT, на рабочих узлах и объединяет результаты в итоговый ответ.

Ниже показан запрос для подсчета веб-запросов в минуту вместе с некоторой статистикой.
Попробуйте выполнить его в psql и просмотрите результаты.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="performing-rollups"></a>Выполнение сведения

Показанный выше запрос эффективен на ранних этапах, но его производительность падает по мере масштабирования. Даже при распределенной обработке будет быстрее предварительно вычислить эти данные, чем пересчитывать их каждый раз.

Быстродействие панели мониторинга можно обеспечить за счет регулярного сведения необработанных данных в таблицу агрегированных данных. В нашем примере мы выполним сведение данных в таблицу поминутно агрегированных данных. Вы также можете задать интервал агрегирования в 5 минут, 15 минут, 1  час и т. д.

Так как сведение данных будет выполняться постоянно, создадим для этого функцию. Выполните следующие команды в psql для создания функции `rollup_http_request`.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Используйте функцию, чтобы выполнить сведение данных.

```sql
SELECT rollup_http_request();
```

Используя неагрегированные данные, можно выполнить запрос к таблице сведения для получения такого же отчета, как и ранее. Выполните следующую команду:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Удаление устаревших данных

Сведение позволяет быстрее обрабатывать запросы, но нам также нужно удалять устаревшие данные, чтобы избежать расходов на хранение постоянно растущего объема данных. Определите, как долго требуется хранить данные разной степени детализации и используйте стандартные запросы для удаления данных с истекшим сроком хранения. В следующем примере мы решили хранить необработанные данные за один день, а поминутно агрегированные данные — за один месяц.

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

В рабочей среде эти запросы можно реализовать в виде функции и вызывать ее каждую минуту с помощью задания cron.

## <a name="clean-up-resources"></a>Очистка ресурсов

На предыдущих шагах вы создали ресурсы Azure в группе серверов. Если эти ресурсы вам больше не нужны, удалите группу серверов. Нажмите кнопку *Удалить* на странице *Обзор*, чтобы удалить группу серверов. При появлении запроса на всплывающей странице проверьте имя группы серверов и нажмите кнопку *Удалить*.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как подготовить к работе группу серверов с подключенным решением Hyperscale (Citus). Вы подключились к ней с помощью psql, создали схему и выполнили распределение данных. Вы научились выполнять запросы к необработанным данным и регулярно агрегировать их, выполнять запрос к таблице агрегированных данных и удалять устаревшие данные.

Теперь ознакомьтесь с основными понятиями гипермасштабирования.
> [!div class="nextstepaction"]
> [Типы гипермасштабируемых узлов](https://aka.ms/hyperscale-concepts)