---
title: Руководство. Доступ к данным Azure Data Lake Storage 2-го поколения с помощью Azure Databricks и Spark | Документация Майкрософт
description: В этом руководстве показано, как выполнять запросы Spark на кластере Azure Databricks для получения доступа к данным в учетной записи хранения Azure Data Lake Storage 2-го поколения.
services: storage
author: dineshmurthy
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: dineshm
ms.openlocfilehash: e448ef0de9ef5560c1b4ea0df5c02e8efd8c0ea9
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891663"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Руководство. Доступ к данным Data Lake Storage 2-го поколения с помощью Azure Databricks и Spark

В этом руководстве показано, как подключить кластер Azure Databricks к данным, хранящимся в учетной записи хранения Azure с поддержкой Azure Data Lake Storage 2-го поколения. Благодаря этому подключению запросы и аналитику можно изначально выполнять из кластера в данных.

Изучив данный учебник, вы научитесь:

> [!div class="checklist"]
> * Создание кластера Databricks
> * Прием неструктурированных данных в учетной записи хранения
> * Выполнять анализ данных в хранилище BLOB-объектов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Создайте учетную запись Azure Data Lake Storage 2-го поколения.

  Ознакомьтесь со статьей о [создании учетной записи хранения Azure Data Lake Storage 2-го поколения](data-lake-storage-quickstart-create-account.md).

* Убедитесь, что учетная запись пользователя содержит назначенную ей [роль участника для данных хранилища BLOB-объектов](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac).

* Установите AzCopy v10. Дополнительные сведения см. в статье [Передача данных с помощью AzCopy версии 10 (предварительная версия)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="download-the-flight-data"></a>Скачивание данных о рейсах

Чтобы продемонстрировать, как выполнять операции по извлечению, преобразованию и загрузке, в этом руководстве используются данные о рейсах из бюро транспортной статистики. Эти данные для работы с руководством необходимо скачать.

1. Перейдите на страницу [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Установите флажок **Prezipped File** (Предварительно сжатый файл), чтобы выбрать все поля данных.

3. Щелкните кнопку **Загрузить** и сохраните результаты на компьютере. 

4. Распакуйте содержимое ZIP-файла и запомните имя файла и путь к файлу. Они понадобятся для выполнения последующего шага.

## <a name="get-your-storage-account-name"></a>Получение имени учетной записи хранения

Вам потребуется имя учетной записи хранения. Чтобы получить его, войдите на [портал Azure](https://portal.azure.com/), выберите **Все службы** и выполните фильтрацию по термину *хранилище*. Затем выберите **Учетные записи хранения** и перейдите к учетной записи хранения.

Вставьте имя в текстовый файл. Он скоро вам понадобится.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу, следуя инструкциям в статье [How to: Создание приложения Azure Active Directory и субъект-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

При изучении этой статьи необходимо выполнить несколько действий.

:heavy_check_mark: При выполнении действий, описанных в разделе [Назначение приложению роли](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) этой статьи, не забудьте назначить приложению **роль участника хранилища BLOB-объектов**.

:heavy_check_mark: При выполнении действий, описанных в разделе [Получение значений для входа](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) этой статьи, вставьте идентификатор клиента, код приложения и значения ключа аутентификации в текстовый файл. Они вам скоро понадобятся.

## <a name="create-an-azure-databricks-service"></a>Создание службы Azure Databricks

В этом разделе вы создадите службу Azure Databricks с помощью портала Azure.

1. На портале Azure выберите **Создать ресурс** > **Analytics** > **Azure Databricks**.

    ![Databricks на портале Azure](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks on Azure portal")

2. В разделе **Служба Azure Databricks** укажите следующие значения, чтобы создать службу Databricks.

    |Свойство  |ОПИСАНИЕ  |
    |---------|---------|
    |**Имя рабочей области**     | Укажите имя рабочей области Databricks.  |
    |**Подписка**     | Выберите подписку Azure в раскрывающемся списке.        |
    |**Группа ресурсов**     | Укажите, следует ли создать новую группу ресурсов или использовать имеющуюся. Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. Дополнительные сведения см. в [обзоре группы ресурсов Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Местоположение.**     | Выберите **Западная часть США 2**. Другие доступные регионы см. в статье о [доступности служб Azure по регионам](https://azure.microsoft.com/regions/services/).       |
    |**Ценовая категория**     |  Выберите **Стандартная**.     |

    ![Создание рабочей области Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Создание службы Azure Databricks")

3. Установите флажок **Закрепить на панели мониторинга** и щелкните **Создать**.

4. Создание учетной записи займет несколько минут. Во время создания учетной записи на портале с правой стороны отображается плитка **Submitting deployment for Azure Databricks** (Идет отправка развертывания для Databricks). Чтобы отслеживать состояние операции, просмотрите индикатор выполнения вверху.

    ![Плитка развертывания Databricks](./media/data-lake-storage-use-databricks-spark/databricks-deployment-tile.png "Databricks deployment tile")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Создание кластера Spark в Azure Databricks.

1. На портале Azure перейдите к созданной службе Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

2. Вы будете перенаправлены на портал Azure Databricks. На портале выберите **Кластер**.

    ![Databricks в Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks on Azure")

3. На странице **создания кластера** укажите значения для создания кластера.

    ![Создание кластера Databricks Spark в Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

4. Заполните значения в следующих полях, и примите значения по умолчанию для других полей.

    * Введите имя кластера.

    * В рамках этой статьи создайте кластер со средой выполнения **5.1**.

    * Убедитесь, что установлен флажок **Terminate after \_\_ minutes of inactivity** (Завершить через __ минут бездействия). Укажите длительность (в минутах) для завершения работы кластера, если тот не используется.

    * Выберите **Create cluster** (Создать кластер). Когда кластер будет выполняться, можно присоединить к нему записные книжки и запустить на нем задания Spark.

## <a name="create-a-file-system-and-mount-it"></a>Создание и подключение файловой системы

В этом разделе вы создадите файловую систему и папку в своей учетной записи хранения.

1. На [портале Azure](https://portal.azure.com) перейдите к созданной службе Azure Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

2. В левой области выберите **Рабочая область**. В раскрывающемся списке **Рабочая область** выберите **Создать** > **Notebook** (Записная книжка).

    ![Создание записной книжки в Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Создание записной книжки в Databricks")

3. В диалоговом окне **создания записной книжки** введите имя записной книжки. Выберите **Python** в качестве языка, а затем выберите созданный ранее кластер Spark.

4. Нажмите кнопку **Создать**.

5. Скопируйте и вставьте следующий блок кода в первую ячейку, но не запускайте этот код.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<application-id>",
           "fs.azure.account.oauth2.client.secret": "<authentication-id>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```
18. В этом блоке кода замените значения заполнителя `storage-account-name`, `application-id`, `authentication-id` и `tenant-id` значениями, собранными после завершения действий в разделах "Отдельная настройка учетной записи хранения" и [Создание субъекта-службы](#service-principal) этой статьи. Замените заполнитель `file-system-name` любым именем, которое вы хотите предоставить вашей файловой системе.

19. Нажмите клавиши **SHIFT + ВВОД**, чтобы запустить код в этом блоке. 

    Оставьте открытой эту записную книжку, так как позже вы добавите к ней команды.

## <a name="ingest-data"></a>Прием данных

### <a name="copy-source-data-into-the-storage-account"></a>Копирование исходных данных в учетную запись хранения

Для копирования данных из *CSV*-файла в вашу учетную запись Data Lake Storage 2-го поколения используйте AzCopy.

1. Откройте окно командной строки и введите следующие команды для входа в свою учетную запись хранения.

   ```bash
   azcopy login
   ```

   Для проверки подлинности учетной записи пользователя следуйте инструкциям, появляющимся в окне командной строки.

2. Чтобы скопировать данные из *CSV*-файла учетной записи, введите следующую команду.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time
   ```
   * Замените значение заполнителя `<csv-folder-path>` значением пути к каталогу *CSV*-файла (включая имя файла).

   * Замените значение заполнителя `storage-account-name` именем вашей учетной записи хранения.

   * Замените заполнитель `file-system-name` любым именем, которое вы хотите предоставить вашей файловой системе.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Использование Databricks Notebook для преобразования CSV-файла в файл Parquet

В созданной ранее записной книжке добавьте новую ячейку и вставьте в нее следующий код. Замените значение заполнителя `storage-account-name` в этом фрагменте кода именем папки, в которой вы сохранили CSV-файл.

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time/<your-folder-name>/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
 flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
 print("Done")
 ```

## <a name="explore-data"></a>Изучение данных

Вставьте в новую ячейку следующий код, чтобы получить список отправленных через средство AzCopy CSV-файлов. Замените значение заполнителя `<csv-folder-path>` значением заполнителя, использованного вами ранее.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/On_Time/<your-folder-name>"))
```

Чтобы создать файл и составить перечень файлов в папке *parquet/flights*, выполните следующий сценарий:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

С помощью этих примеров кода вы изучили иерархическую сущность HDFS, используя данные, хранящиеся в учетной записи хранения с поддержкой Azure Data Lake Storage 2-го поколения.

## <a name="query-the-data"></a>Запрос данных

Теперь вы можете запрашивать данные, отправленные в учетную запись хранения. Введите каждый из приведенных ниже блоков кода в **Cmd 1** и одновременно нажмите клавиши **Cmd + Ввод**, чтобы выполнить сценарий Python.

Чтобы создать кадры данных для источников данных, выполните следующий скрипт.

* Замените значение заполнителя `<csv-folder-path>` значением пути к каталогу *CSV*-файла (включая имя файла).

* Замените значение заполнителя `<your-csv-file-name` именем своего *CSV*-файла.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time/<your-folder-name>/<your-csv-file-name>.csv")
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

Вставьте этот скрипт, чтобы выполнить некоторые базовые запросы анализа к данным.

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
out1 = spark.sql("SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов и все связанные с ней ресурсы, когда надобность в них отпадет. Для этого выберите группу ресурсов для учетной записи хранения и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

[!div class="nextstepaction"] 
> [Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
