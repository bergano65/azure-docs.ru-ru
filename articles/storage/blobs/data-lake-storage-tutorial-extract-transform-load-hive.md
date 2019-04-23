---
title: Руководство по Выполнение операций извлечения, преобразования и загрузки (ETL) с использованием Apache Hive в Azure HDInsight
description: В этом руководстве вы узнаете, как извлекать данные из необработанного набора данных в формате CSV, преобразовывать их с помощью Apache Hive в Azure HDInsight и загружать преобразованные данные в Базу данных SQL Azure с помощью Sqoop.
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jamesbak
ms.openlocfilehash: a5e7fd200617661c38b65ebbd4473a1a729de457
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682361"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Руководство по Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight

В этом руководстве рассматривается выполнение операций извлечения, преобразования и загрузки данных. Вы берете необработанный файл данных CSV, импортируете его в кластер Azure HDInsight, преобразовываете его с помощью Apache Hive и загружаете в базу данных SQL Azure с помощью Apache Sqoop.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Извлечение и отправка данных в кластер HDInsight.
> * Преобразование данных с помощью Apache Hive.
> * Загрузка данных в базу данных SQL Azure с помощью Sqoop.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* **Учетная запись Azure Data Lake Storage 2-го поколения, настроенная для HDInsight**.

    См. раздел [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) (Использование хранилища Azure Data Lake поколения 2 с кластерами Azure HDInsight).

* **Кластер Hadoop в HDInsight на платформе Linux**.

    См. [Краткое руководство. Начало работы с Apache Hadoop и Apache Hive в Azure HDInsight с помощью портала Azure](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **База данных SQL Azure**. Вы используете базу данных SQL Azure в качестве конечного хранилища данных. Если у вас нет базы данных SQL, см. сведения в статье [Создание базы данных SQL Azure на портале Azure](../../sql-database/sql-database-get-started.md).

* **Azure CLI.** Если вы не установили интерфейс командной строки Azure, обратитесь к разделу [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Клиент Secure Shell (SSH)**. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Hadoop) с помощью SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Для выполнения действий, описанных в этой статье, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с Azure HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="download-the-flight-data"></a>Скачивание данных о рейсах

1. Перейдите на страницу [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. На странице выберите следующие значения:

   | ИМЯ | Значение |
   | --- | --- |
   | Фильтр года |2013 |
   | Период фильтра |Январь |
   | Поля |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Очистите все остальные поля.

3. Выберите **Скачать**. Вы получите ZIP-файл с выбранными полями данных.

## <a name="extract-and-upload-the-data"></a>Извлечение и отправка данных

В этом разделе вы отправите данные в кластер HDInsight, а затем скопируете эти данные в учетную запись Azure Data Lake Storage 2-го поколения.

1. Откройте командную строку и воспользуйтесь следующей командой безопасного копирования (SCP), чтобы передать ZIP-файл в головной узел кластера HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Замените заполнитель `<file-name>` именем вашего ZIP-файла.
   * Замените заполнитель `<ssh-user-name>` именем для входа SSH для кластера HDInsight.
   * Замените заполнитель `<cluster-name>` именем кластера HDInsight.

   Если для аутентификации входа посредством SSH используется пароль, будет предложено ввести пароль.

   Если используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу. Например, `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. После завершения отправки можно подключиться к кластеру с помощью SSH. Введите приведенную ниже команду в окне командной строки.

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Чтобы распаковать ZIP-файл, используйте следующую команду:

   ```bash
   unzip <file-name>.zip
   ```

   Она извлекает **CSV**-файл.

4. Используйте следующую команду для создания файловой системы Data Lake Storage 2-го поколения.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Замените заполнитель `<file-system-name>` именем, которое вы хотите предоставить вашей файловой системе.

   Замените заполнитель `<storage-account-name>` именем вашей учетной записи хранения.

5. Создайте каталог с помощью следующей команды:

   ```bash
   hdfs dfs -mkdir -p abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Используйте следующую команду для копирования *CSV*-файла в каталог:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Возьмите имя файла в кавычки, если оно содержит пробелы или специальные символы.

## <a name="transform-the-data"></a>Преобразование данных

В этом разделе вы используете клиент Beeline для выполнения задания Apache Hive.

Как часть задания Apache Hive вы импортируете данные из CSV-файла в таблицу Apache Hive с именем **delays**.

1. В командной строке SSH, которую вы уже использовали для кластера HDInsight, выполните следующую команду для создания и редактирования нового файла **flightdelays.hql**.

   ```bash
   nano flightdelays.hql
   ```

2. Измените следующий текст, заменив заполнители `<file-system-name>` и `<storage-account-name>` именем файловой системы и учетной записи хранения. Затем скопируйте и вставьте текст в nano-консоль, нажав клавишу SHIFT и правую кнопку мыши одновременно.

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
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
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

3. Чтобы сохранить файл, нажмите клавиши CTRL+X, а затем при появлении запроса введите `Y`.

4. Для запуска Hive и выполнения файла **flightdelays.hql** используйте следующую команду:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. По завершении выполнения сценария __flightdelays.hql__ используйте следующую команду, чтобы открыть интерактивный сеанс Beeline:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
   ```

6. При появлении командной строки `jdbc:hive2://localhost:10001/>` используйте приведенный ниже запрос, чтобы извлечь информацию из импортированных данных о задержке рейсов:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

   Вы получите список городов, рейсы в которых задержаны из-за погодных условий, а также среднее время задержки. Он будет сохранен в `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Позже Sqoop считает данные из этого расположения и экспортирует их в базу данных SQL Azure.

7. Чтобы выйти из Beeline, введите `!quit` в командной строке.

## <a name="create-a-sql-database-table"></a>Создание таблицы базы данных SQL

Вам требуется указать имя сервера из базы данных SQL для этой операции. Выполните следующие действия, чтобы найти имя вашего сервера.

1. Перейдите на [портал Azure](https://portal.azure.com).

2. Выберите **Базы данных SQL**.

3. Выполните фильтрацию по имени базы данных, которую вы выбрали. Имя сервера указано в столбце **Имя сервера**.

4. Выполните фильтрацию по имени базы данных, которую вы хотите использовать. Имя сервера указано в столбце **Имя сервера**.

    ![Получение сведений о сервере SQL Azure](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Получение сведений о сервере SQL Azure")

    Существует множество способов подключения к базе данных SQL и создания таблицы. В приведенных ниже действиях используется [FreeTDS](http://www.freetds.org/) из кластера HDInsight.

5. Чтобы установить FreeTDS, выполните следующую команду с помощью SSH-подключения к кластеру:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. После завершения установки используйте следующую команду для подключения к серверу Базы данных SQL.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Замените заполнитель `<server-name>` именем сервера Базы данных SQL.

   * Замените заполнитель `<admin-login>` именем для входа администратора для Базы данных SQL.

   * Замените заполнитель `<database-name>` именем базы данных.

   При появлении запроса введите пароль администратора Базы данных SQL.

   Должен появиться результат, аналогичный приведенному ниже тексту.

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. В окне запроса `1>` введите следующие инструкции:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Если вводится инструкция `GO`, то оцениваются предыдущие инструкции.

   Этот запрос создает таблицу **delays** с кластеризованным индексом.

9. Используйте следующий запрос для проверки создания таблицы.

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   Результат будет аналогичен приведенному ниже:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Enter `exit` at the `1>` , чтобы выйти из служебной программы tsql.

## <a name="export-and-load-the-data"></a>Экспорт и загрузка данных

В предыдущих разделах вы скопировали преобразованные данные в расположение `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. В этом разделе вы с помощью Sqoop экспортируете данные из папки `abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` в созданную в базе данных SQL Azure таблицу.

1. Чтобы проверить, видно ли в Sqoop базу данных SQL, используйте следующую команду:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   Эта команда выводит список баз данных, включая базу данных, в которой вы создали таблицу **delays**.

2. Для экспорта данных из таблицы **hivesampletable** в таблицу **delays** используйте следующую команду:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<file-system-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop подключается к базе данных, которая содержит таблицу **delays**, и экспортирует данные из каталога `/tutorials/flightdelays/output` в таблицу **delays**.

3. Когда команда `sqoop` будет выполнена, используйте служебную программу tsql для подключения к базе данных:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Используйте следующие инструкции, чтобы проверить состояние экспорта данных в таблицу **delays**:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Вы увидите список данных в таблице. Таблица содержит название города и среднее время задержки рейса для этого города.

5. Введите `exit` для выхода из служебной программы tsql.

## <a name="clean-up-resources"></a>Очистка ресурсов

Все ресурсы, используемые в этом руководстве, предварительно созданы. Очистка не требуется.

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать дополнительные возможности работы с данными в HDInsight, ознакомьтесь со статьей по следующей ссылке:

> [!div class="nextstepaction"]
> [Использование Azure Data Lake Storage Gen2 с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
