---
title: Руководство. Узнайте, как выполнить извлечение, загрузку и передачу операций с использованием Azure Databricks
description: В этом руководстве содержатся сведения о том, как извлечь данные из предварительной версии Data Lake Storage 2-го поколения в Azure Databricks, преобразовывать их, а затем загрузить в Хранилище данных SQL Azure.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 01/14/2019
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e4e75c65178c4bbedcf781c2fbf2149a94a702cd
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321200"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Руководство. Извлечение, преобразование и загрузка данных с помощью Azure Databricks

В этом руководстве используется Azure Databricks для выполнения операций извлечения, преобразования и загрузки данных. Вы переместите данные из учетной записи хранения Azure с включенной службой Azure Data Lake Storage 2-го поколения в Хранилище данных SQL Azure.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание рабочей области Azure Databricks;
> * создание кластера Spark в Azure Databricks;
> * создание учетной записи, поддерживающей Azure Data Lake Storage 2-го поколения;
> * отправка данных в Azure Data Lake Storage 2-го поколения;
> * создание записной книжки в Azure Databricks;
> * извлечение данных из Data Lake Storage 2-го поколения;
> * преобразование данных в Azure Databricks;
> * Загрузка данных в хранилище данных SQL Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* Создайте хранилище данных SQL Azure, создайте правило брандмауэра на уровне сервера и подключитесь к серверу с правами администратора. Следуйте инструкциям в статье [Краткое руководство. Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](../../sql-data-warehouse/create-data-warehouse-portal.md).
* Создайте главный ключ базы данных для хранилища данных SQL Azure. Следуйте инструкциям в статье [Создание главного ключа базы данных](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
* [Создайте учетную запись Azure Data Lake Storage 2-го поколения](data-lake-storage-quickstart-create-account.md).
* Загрузите (**small_radio_json.json**) из репозитория [Примеры U-SQL и отслеживание вопросов](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) и запишите путь, куда сохраняете файл.
* Войдите на [портале Azure](https://portal.azure.com/).

## <a name="set-aside-storage-account-configuration"></a>Отдельная настройка учетной записи хранения

Вам потребуется имя учетной записи хранения и URI конечной точки файловой системы.

Чтобы получить имя учетной записи хранения на портале Azure, выберите **Все службы** и выполните фильтрацию по термину *хранилище*. Затем выберите **Учетные записи хранения** и перейдите к учетной записи хранения.

Чтобы получить URI конечной точки файловой системы, выберите **Свойства** и на панели свойств найдите значение поля **Первичная конечная точка файловой системы ADLS**.

Вставьте оба значения в текстовый файл. Они вам скоро понадобятся.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу, следуя инструкциям в статье [Практическое руководство. Создание приложения Azure Active Directory и субъект-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Существует несколько конкретных действий, которые необходимо выполнить при изучении этой статьи.

:heavy_check_mark: Выполняя действия из раздела [Создание приложения Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) той статьи, укажите URI только что собранной конечной точки в поле **URL-адрес для входа** диалогового окна **Создание**.

:heavy_check_mark: При выполнении действий, описанных в разделе [Назначение приложению роли](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) этой статьи, не забудьте назначить приложению **роль участника хранилища BLOB-объектов**.

:heavy_check_mark: При выполнении действий, описанных в разделе [Получение значений для входа](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) этой статьи, вставьте идентификатор клиента, код приложения и значения ключа аутентификации в текстовый файл. Они вам скоро понадобятся.

## <a name="create-the-workspace"></a>Создание рабочей области

В этом разделе вы создадите рабочую область Azure Databricks с помощью портала Azure.

1. На портале Azure выберите **Создать ресурс** > **Analytics** > **Azure Databricks**.

    ![Databricks на портале Azure](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Databricks on Azure portal")

1. В разделе **Служба Azure Databricks** укажите следующие значения, чтобы создать рабочую область Databricks:

    |Свойство  |ОПИСАНИЕ  |
    |---------|---------|
    |**Имя рабочей области**     | Укажите имя рабочей области Databricks.        |
    |**Подписка**     | Выберите подписку Azure в раскрывающемся списке.        |
    |**Группа ресурсов**     | Укажите, следует ли создать новую группу ресурсов или использовать имеющуюся. Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. Дополнительные сведения см. в [обзоре группы ресурсов Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Местоположение.**     | Выберите **Западная часть США 2**.        |
    |**Ценовая категория**     |  Выберите **Стандартная**.     |

    ![Создание рабочей области Azure Databricks](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Create an Azure Databricks workspace")

1. Установите флажок **Закрепить на панели мониторинга** и щелкните **Создать**.

1. Создание учетной записи займет несколько минут. Во время создания учетной записи на портале с правой стороны отображается плитка **Submitting deployment for Azure Databricks** (Идет отправка развертывания для Databricks). Чтобы отслеживать состояние операции, просмотрите индикатор выполнения вверху.

    ![Плитка развертывания Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Databricks deployment tile")

## <a name="create-the-spark-cluster"></a>Создание кластера Spark

Для выполнения операций в этом руководстве необходим кластер Spark. Чтобы создать кластер Spark, выполните приведенные ниже шаги.

1. На портале Azure перейдите к созданной рабочей области Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

1. Вы будете перенаправлены на портал Azure Databricks. На портале выберите **Кластер**.

    ![Databricks в Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks on Azure")

1. На странице **создания кластера** укажите значения для создания кластера.

    ![Создание кластера Databricks Spark в Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

1. Заполните значения в следующих полях, и примите значения по умолчанию для других полей.

    * Введите имя кластера.
    * В рамках этой статьи создайте кластер со средой выполнения **5.1**.
    * Убедитесь, что установлен флажок **Terminate after \_\_ minutes of inactivity** (Завершить через \_\_ минут бездействия). Укажите длительность (в минутах) для завершения работы кластера, если тот не используется.

1. Выберите **Create cluster** (Создать кластер).

Когда кластер будет выполняться, можно присоединить к нему записные книжки и запустить на нем задания Spark.

## <a name="create-a-file-system"></a>Создание файловой системы

Чтобы хранить данные в учетной записи хранения Data Lake Storage 2-го поколения, необходимо создать файловую систему.

Сначала создайте записную книжку в рабочей области Azure Databricks, а затем выполните фрагменты кода для создания файловой системы в учетной записи хранения.

1. На [портале Azure](https://portal.azure.com) перейдите к созданной рабочей области Azure Databricks и выберите **Launch Workspace** (Запустить рабочую область).

2. В левой области выберите **Рабочая область**. В раскрывающемся списке **Рабочая область** выберите **Создать** > **Notebook** (Записная книжка).

    ![Создание записной книжки в Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Создание записной книжки в Databricks")

3. В диалоговом окне **создания записной книжки** введите имя записной книжки. Выберите **Scala** в качестве языка, а затем выберите созданный ранее кластер Spark.

    ![Предоставление сведений для записной книжки в Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Предоставление сведений для записной книжки в Databricks")

    Нажмите кнопку **Создать**.

4. Скопируйте и вставьте следующий блок кода в первую ячейку, но не запускайте этот код.

    ```scala
    val configs = Map(
    "fs.azure.account.auth.type" -> "OAuth",
    "fs.azure.account.oauth.provider.type" -> "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
    "fs.azure.account.oauth2.client.id" -> "<application-id>",
    "fs.azure.account.oauth2.client.secret" -> "<authentication-key>"),
    "fs.azure.account.oauth2.client.endpoint" -> "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
    "fs.azure.createRemoteFileSystemDuringInitialization"->"true")

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/<directory-name>",
    mountPoint = "/mnt/<mount-name>",
    extraConfigs = configs)
    ```

5. В этом блоке кода замените значения заполнителя `storage-account-name`, `application-id`, `authentication-id` и `tenant-id` значениями, собранными после завершения действий в разделах [Отдельная настройка учетной записи хранения](#config) и [Создание субъекта-службы](#service-principal) этой статьи. Вместо значений заполнителей `file-system-name`, `directory-name` и `mount-name` задайте нужные имена файловой системы, каталога и точки подключения.

6. Нажмите клавиши **SHIFT + ВВОД**, чтобы запустить код в этом блоке.

## <a name="upload-the-sample-data"></a>Отправка примеров данных

На следующем шаге пример файла данных передается в учетную запись хранения для его последующего преобразования в Azure Databricks.

Передайте переданный пример данных в учетную запись хранения. Метод, который используется для отправки данных в учетную запись хранения, зависит от того, включено ли иерархическое пространство имен.

Чтобы выполнить отправку, можно использовать Фабрику данных Azure, distp или AzCopy (версии 10). В данный момент AzCopy версии 10 доступна только в рамках предварительной версии. Чтобы использовать AzCopy, вставьте следующий код в окно командной строки:

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>Извлечение данных

Для работы с примером данных в Databricks необходимо извлечь данные из учетной записи хранения.

Вернитесь к записной книжке Databricks и введите следующий код в новую ячейку.

Добавьте следующий фрагмент кода в пустую ячейку кода. Замените значения заполнителей, показанные в скобках, сохраненными ранее значениями из учетной записи хранения.

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Нажмите клавиши SHIFT+ENTER, чтобы выполнить код.

Теперь можно загрузить пример JSON-файла в виде кадра данных в Azure Databricks. Вставьте следующий код в новую ячейку. Замените значения заменителей, показанные в скобках, собственными.

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Нажмите клавиши SHIFT+ENTER, чтобы выполнить код.

Чтобы просмотреть содержимое кадра данных, выполните следующий код:

```scala
df.show()
```

Должен отобразиться результат, аналогичный приведенному ниже фрагменту кода.

```bash
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
|               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
| El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
| Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
| Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
...
...
```

В результате были извлечены данные из Azure Data Lake Storage Gen2 в Azure Databricks.

## <a name="transform-the-data"></a>Преобразование данных

Необработанный файл с примером данных **small_radio_json.json** содержит сведения о слушателях радиостанции и имеет множество столбцов. В этом разделе мы преобразуем данные, чтобы извлечь только определенные столбцы из набора данных.

Сначала извлеките только столбцы **firstName**, **lastName**, **gender**, **location** и **level** из созданного кадра данных.

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
```

Выходные данные должны выглядеть так, как показано в следующем фрагменте кода:

```bash
+---------+----------+------+--------------------+-----+
|firstname|  lastname|gender|            location|level|
+---------+----------+------+--------------------+-----+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
+---------+----------+------+--------------------+-----+
```

Эти данные можно еще преобразовывать, переименовав столбец **level** на **subscription_type**.

```scala
val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
renamedColumnsDF.show()
```

Выходные данные должны выглядеть так, как показано в следующем фрагменте кода.

```bash
+---------+----------+------+--------------------+-----------------+
|firstname|  lastname|gender|            location|subscription_type|
+---------+----------+------+--------------------+-----------------+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
+---------+----------+------+--------------------+-----------------+
```

## <a name="load-the-data"></a>Загрузка данных

В этом разделе преобразованные данные отправляются в хранилище данных SQL Azure. С помощью соединителя Хранилища данных SQL Azure для Azure Databricks можно напрямую отправить кадр данных в виде таблицы в хранилище данных SQL.

Соединитель Хранилища данных SQL использует хранилище BLOB-объектов Azure в качестве временного хранилища для передачи данных между Azure Databricks и Хранилищем данных SQL Azure. Таким образом сначала нужно предоставить конфигурацию для подключения к учетной записи хранения. Вы уже должны были создать учетную запись, выполняя предварительные требования для этой статьи.

Предоставьте конфигурацию для получения доступа к учетной записи хранения Azure из Azure Databricks.

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Укажите временную папку, которая будет использоваться при перемещении данных между Azure Databricks и Хранилищем данных SQL Azure.

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

Запустите следующий фрагмент кода, чтобы сохранить ключ доступа к хранилищу BLOB-объектов Azure в конфигурации. Благодаря этому действию вам не придется хранить ключ доступа в записной книжке в виде обычного текста.

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Укажите значения для подключения к экземпляру хранилища данных SQL Azure. Вы должны были создать хранилище данных SQL, выполняя требования для этой статьи.

```scala
//SQL Data Warehouse related settings
val dwDatabase = "<DATABASE NAME>"
val dwServer = "<DATABASE SERVER NAME>" 
val dwUser = "<USER NAME>"
val dwPass = "<PASSWORD>"
val dwJdbcPort =  "1433"
val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
```

Запустите следующий фрагмент кода, чтобы загрузить преобразованный кадр данных **renamedColumnsDF** в качестве таблицы в хранилище данных SQL. Этот фрагмент кода создает таблицу с именем **SampleTable** в базе данных SQL.

```scala
spark.conf.set(
    "spark.sql.parquet.writeLegacyFormat",
    "true")
    
renamedColumnsDF.write
    .format("com.databricks.spark.sqldw")
    .option("url", sqlDwUrlSmall) 
    .option("dbtable", "SampleTable")
    .option( "forward_spark_azure_storage_credentials","True")
    .option("tempdir", tempDir)
    .mode("overwrite")
    .save()
```

Подключитесь к базе данных SQL и убедитесь, что вы видите базу данных **SampleTable**.

![Проверка примера таблицы](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Проверка примера таблицы")

Выполните запрос SELECT, чтобы проверить содержимое таблицы. В таблице должны быть те же данные, что и в кадре данных **renamedColumnsDF**.

![Проверка содержимого примера таблицы](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Проверка содержимого примера таблицы")

## <a name="clean-up-resources"></a>Очистка ресурсов

После выполнения заданий из этого руководства вы можете завершить работу кластера. В рабочей области Azure Databricks в области слева выберите **Кластеры**. Для завершения работы кластера в разделе **Действия** наведите указатель мыши на многоточие (...) и выберите значок **Завершить**.

![Завершение работы кластера Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Stop a Databricks cluster")

Если не завершить работу кластера вручную, она завершится автоматически, если во время создания кластера вы установили флажок **Terminate after \_\_ minutes of inactivity** (Завершить через \_\_ минут бездействия). В этом случае работа кластера автоматически завершается, если он был неактивным в течение определенного времени.

## <a name="next-steps"></a>Дополнительная информация

Перейдите к следующему руководству, чтобы узнать о потоковой передачи данных в режиме реального времени в Azure Databricks с помощью Центров событий Azure.

> [!div class="nextstepaction"]
>[Руководство по потоковой передаче данных в Azure Databricks при помощи Центров событий](../../azure-databricks/databricks-stream-from-eventhubs.md)
