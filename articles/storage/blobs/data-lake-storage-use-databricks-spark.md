---
title: Руководство по Доступ к данным Azure Data Lake Storage 2-го поколения с помощью Azure Databricks и Spark | Документация Майкрософт
description: В этом руководстве показано, как выполнять запросы Spark в кластере Azure Databricks для получения доступа к данным в учетной записи хранения Azure Data Lake Storage 2-го поколения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 0607c2b848a486e24654081bd7937cb734394e58
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331843"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Руководство по Доступ к данным Data Lake Storage 2-го поколения с помощью Azure Databricks и Spark

В этом руководстве показано, как подключить кластер Azure Databricks к данным, хранящимся в учетной записи хранения Azure с поддержкой Azure Data Lake Storage 2-го поколения. Благодаря этому подключению запросы и аналитику можно изначально выполнять из кластера в данных.

Изучив данный учебник, вы научитесь:

> [!div class="checklist"]
> * Создание кластера Databricks
> * Прием неструктурированных данных в учетной записи хранения
> * Выполнять анализ данных в хранилище BLOB-объектов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Создайте учетную запись Azure Data Lake Storage 2-го поколения.

  См. статью [Краткое руководство. Создание поддерживаемой учетной записи хранения Azure Data Lake Storage 2-го поколения](data-lake-storage-quickstart-create-account.md).

* Убедитесь, что учетная запись пользователя содержит назначенную ей [роль участника для данных хранилища BLOB-объектов](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac).

* Установите AzCopy v10. Дополнительные сведения см. в статье [Передача данных с помощью AzCopy версии 10 (предварительная версия)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* Создание субъекта-службы. Дополнительные сведения см. в статье [Azure Создание приложения Azure Active Directory и субъект-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Существует несколько конкретных действий, которые необходимо выполнить при изучении этой статьи.

  :heavy_check_mark: При выполнении действий, описанных в разделе [Назначение приложению роли](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) этой статьи, не забудьте назначить субъекту-службе роль **участника данных BLOB-объектов хранилища**.

  > [!IMPORTANT]
  > Убедитесь в том, что роль назначается в учетной записи хранения Data Lake Storage 2-го поколения. Можно назначить роль родительской группе ресурсов или подписке, но вы будете получать ошибки, связанные с разрешениями, пока роль не будет назначена учетной записи хранения.

  :heavy_check_mark: При выполнении действий, описанных в разделе [Получение значений для входа](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) этой статьи, вставьте идентификатор клиента, идентификатор приложения и значения паролей в текстовый файл. Они вам скоро понадобятся.

### <a name="download-the-flight-data"></a>Скачивание данных о рейсах

Чтобы продемонстрировать, как выполнять операции по извлечению, преобразованию и загрузке, в этом руководстве используются данные о рейсах из бюро транспортной статистики. Эти данные для работы с руководством необходимо скачать.

1. Перейдите на страницу [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Установите флажок **Prezipped File** (Предварительно сжатый файл), чтобы выбрать все поля данных.

3. Щелкните кнопку **Загрузить** и сохраните результаты на компьютере. 

4. Распакуйте содержимое ZIP-файла и запомните имя файла и путь к файлу. Они понадобятся для выполнения последующего шага.

## <a name="create-an-azure-databricks-service"></a>Создание службы Azure Databricks.

В этом разделе вы создадите службу Azure Databricks с помощью портала Azure.

1. На портале Azure выберите **Создать ресурс** > **Analytics** > **Azure Databricks**.

    ![Databricks на портале Azure](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks on Azure portal")

2. В разделе **Служба Azure Databricks** укажите следующие значения, чтобы создать службу Databricks.

    |Свойство  |ОПИСАНИЕ  |
    |---------|---------|
    |**Имя рабочей области**     | Укажите имя рабочей области Databricks.  |
    |**подписка**     | Выберите подписку Azure в раскрывающемся списке.        |
    |**группа ресурсов**     | Укажите, следует ли создать новую группу ресурсов или использовать имеющуюся. Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. Дополнительные сведения см. в [обзоре группы ресурсов Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Местоположение.**     | Выберите **Западная часть США 2**. Другие доступные регионы см. в статье о [доступности служб Azure по регионам](https://azure.microsoft.com/regions/services/).       |
    |**Ценовая категория**     |  Выберите **Стандартная**.     |

    ![Создание рабочей области Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Создание службы Azure Databricks")

3. Создание учетной записи займет несколько минут. Чтобы отслеживать состояние операции, просмотрите индикатор выполнения вверху.

4. Установите флажок **Закрепить на панели мониторинга** и щелкните **Создать**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Создание кластера Spark в Azure Databricks.

1. На портале Azure перейдите к созданной службе Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

2. Вы будете перенаправлены на портал Azure Databricks. На портале выберите **Кластер**.

    ![Databricks в Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks on Azure")

3. На странице **создания кластера** укажите значения для создания кластера.

    ![Создание кластера Databricks Spark в Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

    Заполните значения в следующих полях, и примите значения по умолчанию для других полей.

    - Введите имя кластера.
     
    - Убедитесь, что установлен флажок **Terminate after 120 minutes of activity** (Завершить работу после 120 минут отсутствия активности). Укажите длительность (в минутах) для завершения работы кластера, если тот не используется.

4. Выберите **Create cluster** (Создать кластер). Когда кластер будет выполняться, можно присоединить к нему записные книжки и запустить на нем задания Spark.

## <a name="ingest-data"></a>Прием данных

### <a name="copy-source-data-into-the-storage-account"></a>Копирование исходных данных в учетную запись хранения

Для копирования данных из *CSV*-файла в вашу учетную запись Data Lake Storage 2-го поколения используйте AzCopy.

1. Откройте окно командной строки и введите следующие команды для входа в свою учетную запись хранения.

   ```bash
   azcopy login
   ```

   Для аутентификации учетной записи пользователя следуйте инструкциям, появляющимся в окне командной строки.

2. Чтобы скопировать данные из *CSV*-файла учетной записи, введите следующую команду.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Замените значение заполнителя `<csv-folder-path>` путем к *CSV*-файлу.

   * Замените значение заполнителя `<storage-account-name>` именем вашей учетной записи хранения.

   * Замените заполнитель `<container-name>` любым именем, которое хотите присвоить своему контейнеру.

## <a name="create-a-container-and-mount-it"></a>Создание и подключение контейнера

В рамках этого раздела вы создадите контейнер и папку в своей учетной записи хранения.

1. На [портале Azure](https://portal.azure.com) перейдите к созданной службе Azure Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

2. В левой области выберите **Рабочая область**. В раскрывающемся списке **Рабочая область** выберите **Создать** > **Notebook** (Записная книжка).

    ![Создание записной книжки в Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Создание записной книжки в Databricks")

3. В диалоговом окне **создания записной книжки** введите имя записной книжки. Выберите **Python** в качестве языка, а затем выберите созданный ранее кластер Spark.

4. Нажмите кнопку **Создать**.

5. Скопируйте и вставьте следующий блок кода в первую ячейку, но не запускайте этот код.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<password>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. В этом блоке кода замените значения заполнителя `appId`, `password`, `tenant` и `storage-account-name` значениями, полученными в ходе выполнения предварительных условий этого руководства. Замените значение заполнителя `container-name` именем, которое вы присвоили своему контейнеру на предыдущем этапе.

Используйте эти значения, чтобы заменить упомянутые заполнители.

   * `appId` и `password` заменяются значениями из приложения, которое вы зарегистрировали в Azure AD при создании субъекта-службы.

   * Значение `tenant-id` берется из подписки.

   * `storage-account-name` — это имя учетной записи хранения Azure Data Lake Storage 2-го поколения.

   * Замените заполнитель `container-name` любым именем, которое хотите присвоить своему контейнеру.

   > [!NOTE]
   > При настройке рабочей среды рассмотрите возможность сохранения пароля в Azure Databricks. Затем в блоке кода замените пароль ключом поиска. Выполнив инструкции из этого краткого руководства, ознакомьтесь с примерами такого подхода в статье о [Data Lake Storage 2-го поколения](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) на веб-сайте Azure Databricks.

19. Нажмите клавиши **SHIFT + ВВОД**, чтобы запустить код в этом блоке.

   Оставьте открытой эту записную книжку, так как позже вы добавите к ней команды.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Использование Databricks Notebook для преобразования CSV-файла в файл Parquet

В созданной ранее записной книжке добавьте новую ячейку и вставьте в нее следующий код. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Изучение данных

Вставьте в новую ячейку следующий код, чтобы получить список отправленных через средство AzCopy CSV-файлов.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
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

* Замените значение заполнителя `<csv-folder-path>` путем к *CSV*-файлу.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Вставьте этот скрипт, чтобы выполнить некоторые базовые запросы анализа к данным.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов и все связанные с ней ресурсы, когда надобность в них отпадет. Для этого выберите группу ресурсов для учетной записи хранения и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"] 
> [Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
