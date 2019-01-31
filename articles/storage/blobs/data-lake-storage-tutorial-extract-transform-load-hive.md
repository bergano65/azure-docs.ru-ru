---
title: Руководство. Выполнение операций извлечения, преобразования и загрузки (ETL) с использованием Apache Hive в Azure HDInsight
description: В этом руководстве вы узнаете, как извлекать данные из необработанного набора данных в формате CSV, преобразовывать их с помощью Apache Hive в Azure HDInsight и загружать преобразованные данные в Базу данных SQL Azure с помощью Sqoop.
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jamesbak
ms.openlocfilehash: 70ad37aa0ccbab762aa6e5cfb05d385e8b2a86ee
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244017"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Руководство. Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight

В этом руководстве рассматривается выполнение операций извлечения, преобразования и загрузки данных. Вы берете необработанный файл данных CSV, импортируете его в кластер Azure HDInsight, преобразовываете его с помощью Apache Hive и загружаете в базу данных SQL Azure с помощью Apache Sqoop.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Извлечение и отправка данных в кластер HDInsight.
> * Преобразование данных с помощью Apache Hive.
> * Загрузка данных в базу данных SQL Azure с помощью Sqoop.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* **Кластер Hadoop в HDInsight на платформе Linux**. Пошаговые инструкции по созданию нового кластера HDInsight под управлением Linux см. в руководстве по [настройке кластеров в HDInsight с помощью Hadoop, Spark, Kafka и других средств](./data-lake-storage-quickstart-create-connect-hdi-cluster.md).

* **База данных SQL Azure**. Вы используете базу данных SQL Azure в качестве конечного хранилища данных. Если у вас нет базы данных SQL, см. сведения в статье [Создание базы данных SQL Azure на портале Azure](../../sql-database/sql-database-get-started.md).

* **Azure CLI.** Если вы не установили интерфейс командной строки Azure, обратитесь к разделу [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Клиент SSH**. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Hadoop) с помощью SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Для выполнения действий, описанных в этой статье, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с Azure HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="download-the-flight-data"></a>Скачивание данных о рейсах

Чтобы продемонстрировать, как выполнять операции по извлечению, преобразованию и загрузке, в этом руководстве используются данные о рейсах из бюро транспортной статистики. Эти данные для работы с руководством необходимо скачать.

1. Перейдите на страницу [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

1. На странице выберите следующие значения:

   | ИМЯ | Значение |
   | --- | --- |
   | **Фильтр года** |2013 |
   | **Фильтр периода** |Январь |
   | **Поля** |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

1. Очистите все остальные поля.

1. Выберите **Скачать**. Вы получите ZIP-файл с выбранными полями данных.

## <a name="extract-and-upload-the-data"></a>Извлечение и отправка данных

В этом разделе вы используете `scp` для отправки данных в кластер HDInsight.

Откройте командную строку и воспользуйтесь следующей командой, чтобы передать ZIP-файл в головной узел кластера HDInsight:

```bash
scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
```

* Замените \<FILE_NAME> именем ZIP-файла.
* Замените \<SSH_USER_NAME> именем для входа SSH для кластера HDInsight.
* Замените \<CLUSTER_NAME> именем кластера HDInsight.

Если для аутентификации входа посредством SSH используется пароль, будет предложено ввести пароль. 

Если используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу. Например, `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

После завершения отправки можно подключиться к кластеру с помощью SSH. Введите приведенную ниже команду в окне командной строки.

```bash
ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
```

Чтобы распаковать ZIP-файл, используйте следующую команду:

```bash
unzip <FILE_NAME>.zip
```

Она извлекает **CSV**-файл, размер которого составляет приблизительно 60 МБ.

С помощью указанных ниже команд создайте каталог, а затем скопируйте *CSV*-файл в этот каталог.

```bash
hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
```

Используйте следующую команду для создания файловой системы Data Lake Storage 2-го поколения.

```bash
hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
```

## <a name="transform-the-data"></a>Преобразование данных

В этом разделе вы используете клиент Beeline для выполнения задания Apache Hive.

Как часть задания Apache Hive вы импортируете данные из CSV-файла в таблицу Apache Hive с именем **delays**.

В командной строке SSH, которую вы уже использовали для кластера HDInsight, выполните следующую команду для создания и редактирования нового файла **flightdelays.hql**.

```bash
nano flightdelays.hql
```

В качестве содержимого файла добавьте следующий текст:

```hiveql
 DROP TABLE delays_raw;
 -- Creates an external table over the csv file
 CREATE EXTERNAL TABLE delays_raw (
    YEAR string,
    FL_DATE string,
    UNIQUE_CARRIER string,
    CARRIER string,
    FL_NUM string,
    ORIGIN_AIRPORT_ID string,
    ORIGIN string,
    ORIGIN_CITY_NAME string,
    ORIGIN_CITY_NAME_TEMP string,
    ORIGIN_STATE_ABR string,
    DEST_AIRPORT_ID string,
    DEST string,
    DEST_CITY_NAME string,
    DEST_CITY_NAME_TEMP string,
    DEST_STATE_ABR string,
    DEP_DELAY_NEW float,
    ARR_DELAY_NEW float,
    CARRIER_DELAY float,
    WEATHER_DELAY float,
    NAS_DELAY float,
    SECURITY_DELAY float,
    LATE_AIRCRAFT_DELAY float)
 -- The following lines describe the format and location of the file
 ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
 LINES TERMINATED BY '\n'
 STORED AS TEXTFILE
 LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

-- Drop the delays table if it exists
DROP TABLE delays;
-- Create the delays table and populate it with data
-- pulled in from the CSV file (via the external table defined previously)
CREATE TABLE delays
LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
AS
SELECT YEAR AS year,
    FL_DATE AS flight_date,
    substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
    substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
    substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
    ORIGIN_AIRPORT_ID AS origin_airport_id,
    substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
    substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
    substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
    DEST_AIRPORT_ID AS dest_airport_id,
    substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
    substring(DEST_CITY_NAME,2) AS dest_city_name,
    substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
    DEP_DELAY_NEW AS dep_delay_new,
    ARR_DELAY_NEW AS arr_delay_new,
    CARRIER_DELAY AS carrier_delay,
    WEATHER_DELAY AS weather_delay,
    NAS_DELAY AS nas_delay,
    SECURITY_DELAY AS security_delay,
    LATE_AIRCRAFT_DELAY AS late_aircraft_delay
FROM delays_raw;
```

Чтобы сохранить файл, нажмите клавишу ESC и введите `:x`.

Для запуска Hive и выполнения файла **flightdelays.hql** используйте следующую команду:

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
```

По завершении выполнения сценария __flightdelays.hql__ используйте следующую команду, чтобы открыть интерактивный сеанс Beeline:

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
```

При появлении командной строки `jdbc:hive2://localhost:10001/>` используйте приведенный ниже запрос, чтобы извлечь информацию из импортированных данных о задержке рейсов:

```hiveql
INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
SELECT regexp_replace(origin_city_name, '''', ''),
    avg(weather_delay)
FROM delays
WHERE weather_delay IS NOT NULL
GROUP BY origin_city_name;
```

Вы получите список городов, рейсы в которых задержаны из-за погодных условий, а также среднее время задержки. Он будет сохранен в `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Позже Sqoop считает данные из этого расположения и экспортирует их в базу данных SQL Azure.

Чтобы выйти из Beeline, введите `!quit` в командной строке.

## <a name="create-a-sql-database-table"></a>Создание таблицы базы данных SQL

Вам требуется указать имя сервера из базы данных SQL для этой операции. Выполните следующие действия, чтобы найти имя вашего сервера.

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Выберите **Базы данных SQL**.

1. Выполните фильтрацию по имени базы данных, которую вы выбрали. Имя сервера указано в столбце **Имя сервера**.

1. Выполните фильтрацию по имени базы данных, которую вы хотите использовать. Имя сервера указано в столбце **Имя сервера**.

    ![Получение сведений о сервере SQL Azure](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Получение сведений о сервере SQL Azure")

    Существует множество способов подключения к базе данных SQL и создания таблицы. В приведенных ниже действиях используется [FreeTDS](http://www.freetds.org/) из кластера HDInsight.

Чтобы установить FreeTDS, выполните следующую команду с помощью SSH-подключения к кластеру:

```bash
sudo apt-get --assume-yes install freetds-dev freetds-bin
```

После завершения установки используйте следующую команду для подключения к серверу Базы данных SQL.

* Замените \<SERVER_NAME> именем сервера Базы данных SQL.
* Замените \<ADMIN_LOGIN> именем для входа администратора для Базы данных SQL.
* Замените \<DATABASE_NAME> именем базы данных.

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
```

При появлении запроса введите пароль администратора Базы данных SQL.

Должен появиться результат, аналогичный приведенному ниже тексту.

```
locale is "en_US.UTF-8"
locale charset is "UTF-8"
using default charset "UTF-8"
Default database being set to sqooptest
1>
```

В окне запроса `1>` введите следующие инструкции:

```hiveql
CREATE TABLE [dbo].[delays](
[origin_city_name] [nvarchar](50) NOT NULL,
[weather_delay] float,
CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
([origin_city_name] ASC))
GO
```

Если вводится инструкция `GO`, то оцениваются предыдущие инструкции.
Этот запрос создает таблицу **delays** с кластеризованным индексом.

Используйте следующий запрос для проверки создания таблицы.

```hiveql
SELECT * FROM information_schema.tables
GO
```

Результат будет аналогичен приведенному ниже:

```
TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
databaseName       dbo             delays        BASE TABLE
```

Enter `exit` at the `1>` , чтобы выйти из служебной программы tsql.

## <a name="export-and-load-the-data"></a>Экспорт и загрузка данных

В предыдущих разделах вы скопировали преобразованные данные в расположение `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. В этом разделе вы с помощью Sqoop экспортируете данные из папки `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` в созданную в базе данных SQL Azure таблицу.

Чтобы проверить, видно ли в Sqoop базу данных SQL, используйте следующую команду:

```bash
sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
```

Эта команда выводит список баз данных, включая базу данных, в которой вы создали таблицу **delays**.

Для экспорта данных из таблицы **hivesampletable** в таблицу **delays** используйте следующую команду:

```bash
sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
```

Sqoop подключается к базе данных, которая содержит таблицу **delays**, и экспортирует данные из каталога `/tutorials/flightdelays/output` в таблицу **delays**.

Когда команда `sqoop` будет выполнена, используйте служебную программу tsql для подключения к базе данных:

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
```

Используйте следующие инструкции, чтобы проверить состояние экспорта данных в таблицу **delays**:

```sql
SELECT * FROM delays
GO
```

Вы увидите список данных в таблице. Таблица содержит название города и среднее время задержки рейса для этого города.

Введите `exit` для выхода из служебной программы tsql.

## <a name="clean-up-resources"></a>Очистка ресурсов

Все ресурсы, используемые в этом руководстве, предварительно созданы. Очистка не требуется.

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать дополнительные возможности работы с данными в HDInsight, ознакомьтесь со статьей по следующей ссылке:

> [!div class="nextstepaction"]
> [Извлечение, преобразование и загрузка данных с помощью Azure Databricks](./data-lake-storage-use-hdi-cluster.md)
