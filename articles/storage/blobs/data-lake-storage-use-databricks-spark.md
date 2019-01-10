---
title: Руководство. Доступ к данным предварительной версии Azure Data Lake Storage 2-го поколения с помощью Azure Databricks и Spark | Документация Майкрософт
description: В этом руководстве показано, как выполнять запросы Spark на кластере Azure Databricks для получения доступа к данным в учетной записи хранения Azure Data Lake Storage 2-го поколения.
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: dineshm
ms.openlocfilehash: b0382d31f9d16228ca3447ace9c7d4f171b206f6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548992"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Руководство. Получение доступа к данным предварительной версии Data Lake Storage 2-го поколения с помощью Azure Databricks и Spark

В этом руководстве показано, как подключить кластер Azure Databricks к данным, хранящимся в учетной записи хранения Azure с поддержкой предварительной версии Azure Data Lake Storage 2-го поколения. Благодаря этому подключению запросы и аналитику можно изначально выполнять из кластера в данных.

Изучив данный учебник, вы научитесь:

> [!div class="checklist"]
> * Создание кластера Databricks
> * Прием неструктурированных данных в учетной записи хранения
> * Выполнение анализа данных в хранилище BLOB-объектов

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве демонстрируется, как использовать данные о рейсах авиакомпаний и запрашивать эти данные, доступные в [Министерстве транспорта США](https://transtats.bts.gov/DL_SelectFields.asp). 

1. Установите флажок **Prezipped file** (Предварительно сжатый файл), чтобы выбрать все поля данных.
2. Выберите **Загрузить** и сохраните результаты на компьютер.
3. Запишите имя файла и путь скачивания. Они понадобятся вам на следующем шаге.

Для работы с этим руководством необходима учетная запись хранения с поддержкой возможностей аналитики. Мы рекомендуем ознакомиться с [кратким руководством](data-lake-storage-quickstart-create-account.md) по этой теме для создания учетной записи хранения. Перейдите к учетной записи хранения после ее создания, чтобы получить параметры конфигурации.

1. В разделе **Параметры** выберите **Ключи доступа**.
2. Нажмите кнопку **Копировать** рядом с **Key1**, чтобы скопировать значение ключа.

В рамках этого руководства ключ и имя учетной записи необходимы для выполнения следующих действий. Откройте текстовый редактор и скопируйте в него имя учетной записи и ключ для дальнейшего использования.

## <a name="create-a-databricks-cluster"></a>Создание кластера Databricks

Затем нужно создать кластер Databricks для создания рабочей области данных.

1. Выберите **Создать ресурс** на [портале Azure](https://portal.azure.com).
2. Введите **Azure Databricks** в поле поиска.
3. В колонке Azure Databricks выберите **Создать**.
4. Назовите службу Databricks **myFlightDataService**. При этом установите флажок *Закрепить на панели мониторинга*.
5. Выберите **Launch Workspace** (Запустить рабочее пространство), чтобы открыть рабочее пространство в новом окне браузера.
6. Выберите **Кластеры** на панели навигации слева.
7. Выберите **Создать кластер**.
8. Введите **myFlightDataCluster** в поле **Имя кластера**.
9. Выберите **Standard_D8s_v3** в поле **Тип рабочего процесса**.
10. Измените значение параметра **Min Workers** (Минимальное количество рабочих процессов) на **4**.
11. Выберите **Создать кластер** в верхней части страницы. (На завершение процесса может потребоваться до пяти минут.)
12. После завершения процесса выберите **Azure Databricks** в верхней левой части панели навигации.
13. Выберите **Блокнот** в разделе **New** (Новый) в нижней части страницы.
14. Введите имя по своему усмотрению в поле **Имя** и выберите **Python** в качестве языка.
15. Во всех остальных полях можно оставить значения по умолчанию.
16. Нажмите кнопку **Создать**.
17. Скопируйте приведенный ниже код и вставьте его в ячейку **Cmd 1**. Замените значения заполнителей, показанные в примере в скобках, собственными значениями.

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
        
    dbutils.fs.mount(
        source = "abfss://dbricks@<account-name>.dfs.core.windows.net/folder1",
        mount_point = "/mnt/flightdata",
        extra_configs = configs)
    ```
18. Чтобы выполнить ячейку кода, нажмите клавиши **SHIFT+ВВОД**.

## <a name="ingest-data"></a>Прием данных

### <a name="copy-source-data-into-the-storage-account"></a>Копирование исходных данных в учетную запись хранения

Далее нужно использовать средство AzCopy для копирования данных из *CSV-файла* в хранилище Azure. Откройте окно командной строки и введите следующие команды: Обязательно замените заполнители `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` и`<ACCOUNT_KEY>` соответствующими значениями, сохраненными при выполнении предыдущего шага.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Использование Databricks Notebook для преобразования CSV-файла в файл Parquet

Повторно откройте Databricks в браузере и сделайте следующее:

1. Выберите **Azure Databricks** в верхней левой части панели навигации.
2. Выберите **Блокнот** в разделе **New** (Новый) в нижней части страницы.
3. Введите **CSV2Parquet** в поле **Имя**.
4. Во всех остальных полях можно оставить значения по умолчанию.
5. Нажмите кнопку **Создать**.
6. Скопируйте приведенный ниже код и вставьте его в ячейку **Cmd 1**. (Этот код автоматически сохраняется в текстовом редакторе.)

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>Изучение данных

Вернитесь в рабочее пространство Databricks и щелкните значок **Последние** на панели навигации слева.

1. Выберите блокнот **Flight Data Analytics** (Аналитика данных рейсов).
2. Одновременно нажмите клавиши **Ctrl + Alt + N**, чтобы создать ячейку.

Введите каждый из приведенных ниже блоков кода в **Cmd 1** и одновременно нажмите клавиши **Cmd + Ввод**, чтобы выполнить сценарий Python.

Чтобы получить список отправленных через средство AzCopy CSV-файлов, выполните следующий сценарий:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

Чтобы создать файл и составить перечень файлов в папке *parquet/flights*, выполните следующий сценарий:

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

С помощью этих примеров кода вы изучили иерархическую сущность HDFS, используя данные, хранящиеся в учетной записи хранения с поддержкой Azure Data Lake Storage 2-го поколения.

## <a name="query-the-data"></a>Запрос данных

Теперь вы можете запрашивать данные, отправленные в учетную запись хранения. Введите каждый из приведенных ниже блоков кода в **Cmd 1** и одновременно нажмите клавиши **Cmd + Ввод**, чтобы выполнить сценарий Python.

### <a name="run-simple-queries"></a>Выполнение простых запросов

Чтобы создать кадры данных для источников данных, выполните следующий сценарий:

> [!IMPORTANT]
> Обязательно замените заполнитель **<YOUR_CSV_FILE_NAME>** именем файла, скаченного на начальном этапе этого руководства.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

Для выполнения запросов анализа данных выполните следующий сценарий:

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

### <a name="run-complex-queries"></a>Выполнение сложных запросов

Чтобы выполнить следующие более сложные запросы, одновременно запустите каждый сегмент в блокноте и проверьте результаты.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов и все связанные с ней ресурсы, когда надобность в них отпадет. Для этого выберите группу ресурсов для учетной записи хранения и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

[!div class="nextstepaction"] 
> [Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)

