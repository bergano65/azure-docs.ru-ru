---
title: Руководство. Выполнение операций извлечения, преобразования и загрузки с помощью Azure Databricks
description: Узнайте, как извлечь данные из Data Lake Storage 2-го поколения в Azure Databricks, преобразовать их, а затем загрузить в Хранилище данных SQL Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/24/2019
ms.openlocfilehash: b48ac9cf8eff001e62f54e41b5f76a9d006bc5ba
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328934"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Руководство. Извлечение, преобразование и загрузка данных с помощью Azure Databricks

В этом руководстве рассматривается выполнение операций извлечения, преобразования и загрузки данных с помощью Azure Databricks. Мы извлечем данные из Azure Data Lake Storage 2-го поколения в Azure Databricks, выполним преобразование данных в Azure Databricks, а затем загрузим преобразованные данные в Хранилище данных SQL Azure.

Для действий, описанных в этом руководстве, используется соединитель хранилища данных SQL для Azure Databricks, позволяющий передавать данные в Azure Databricks. Этот соединитель, в свою очередь, использует хранилище BLOB-объектов Azure как временное хранилище для данных, передаваемых между кластером Azure Databricks и хранилищем данных SQL Azure.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Создайте службу Azure Databricks.
> * создание кластера Spark в Azure Databricks;
> * Создание файловой системы и передача данных в Azure Data Lake Storage 2-го поколения.
> * Создание субъекта-службы.
> * Извлечение данных из Data Lake Store.
> * преобразование данных в Azure Databricks;
> * Загрузка данных в хранилище данных SQL Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать работу с этим руководством, выполните следующие задачи:

* Создайте хранилище данных SQL Azure, создайте правило брандмауэра на уровне сервера и подключитесь к серверу с правами администратора. См. [Краткое руководство. Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](../sql-data-warehouse/create-data-warehouse-portal.md).

* Создайте главный ключ базы данных для хранилища данных SQL Azure. Ознакомьтесь со статьей о [создании главного ключа базы данных](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Создайте учетную запись Azure Data Lake Storage 2-го поколения. Ознакомьтесь со статьей о [создании учетной записи хранения Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Создайте учетную запись хранилища BLOB-объектов Azure, содержащую контейнер. Также получите ключ для доступа к учетной записи хранения. См. [Краткое руководство. Передача, скачивание и составление списка больших двоичных объектов с помощью портала Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

* Войдите на [портале Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-service"></a>Создание службы Azure Databricks

В этом разделе вы создадите службу Azure Databricks с помощью портала Azure.

1. На портале Azure выберите **Создать ресурс** > **Analytics** > **Azure Databricks**.

    ![Databricks на портале Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks on Azure portal")

2. В разделе **Служба Azure Databricks** укажите следующие значения, чтобы создать службу Databricks.

    |Свойство  |ОПИСАНИЕ  |
    |---------|---------|
    |**Имя рабочей области**     | Укажите имя рабочей области Databricks.        |
    |**Подписка**     | Выберите подписку Azure в раскрывающемся списке.        |
    |**Группа ресурсов**     | Укажите, следует ли создать новую группу ресурсов или использовать имеющуюся. Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. Дополнительные сведения см. в [обзоре группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Местоположение.**     | Выберите **Западная часть США 2**.  Другие доступные регионы см. в статье о [доступности служб Azure по регионам](https://azure.microsoft.com/regions/services/).      |
    |**Ценовая категория**     |  Выберите **Стандартная**.     |

3. Установите флажок **Закрепить на панели мониторинга** и щелкните **Создать**.

4. Создание учетной записи займет несколько минут. Во время создания учетной записи на портале с правой стороны отображается плитка **Submitting deployment for Azure Databricks** (Идет отправка развертывания для Databricks). Чтобы отслеживать состояние операции, просмотрите индикатор выполнения вверху.

    ![Плитка развертывания Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Databricks deployment tile")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Создание кластера Spark в Azure Databricks.

1. На портале Azure перейдите к созданной службе Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

2. Вы будете перенаправлены на портал Azure Databricks. На портале выберите **Кластер**.

    ![Databricks в Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks on Azure")

3. На странице **создания кластера** укажите значения для создания кластера.

    ![Создание кластера Databricks Spark в Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

4. Заполните значения в следующих полях, и примите значения по умолчанию для других полей.

    * Введите имя кластера.

    * В рамках этой статьи создайте кластер со средой выполнения **5.1**.

    * Убедитесь, что установлен флажок **Terminate after \_\_ minutes of inactivity** (Завершить через __ минут бездействия). Укажите длительность (в минутах) для завершения работы кластера, если тот не используется.

    * Выберите **Create cluster** (Создать кластер). Когда кластер будет выполняться, можно присоединить к нему записные книжки и запустить на нем задания Spark.

## <a name="create-a-file-system-and-upload-sample-data"></a>Создание файловой системы и передача примера данных

Сначала создайте файловую систему в своей учетной записи Data Lake Storage 2-го поколения. Затем можно отправить пример файла данных в Data Lake Store. Этот файл позже будет использоваться в Azure Databricks для выполнения некоторых преобразований.

1. Скачайте образец файла данных [small_radio_json.json](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) в локальную файловую систему.

2. На [портале Azure](https://portal.azure.com/) перейдите к учетной записи Data Lake Storage 2-го поколения, которую вы создали в качестве предварительного условия для этого руководства.

3. На странице **Обзор** учетной записи хранения выберите **Открыть в обозревателе**.

   ![Открыть Обозреватель службы хранилища](./media/databricks-extract-load-sql-data-warehouse/data-lake-storage-open-storage-explorer.png "Open Storage Explorer")

4. Выберите **Открыть обозреватель службы хранилища Azure**, чтобы открыть Обозреватель службы хранилища.

   ![Второй запрос на открытие Обозревателя службы хранилища](./media/databricks-extract-load-sql-data-warehouse/data-lake-storage-open-storage-explorer-2.png "Open Storage Explorer second prompt")

   Обозреватель службы хранилища открывается. Вы можете создать файловую систему и загрузить пример данных, используя инструкции в этой статье: [Краткое руководство Создание большого двоичного объекта в хранилище объектов с помощью Обозревателя службы хранилища Azure](../storage/blobs/data-lake-storage-explorer.md).

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу, следуя инструкциям в статье [How to: Создание приложения Azure Active Directory и субъект-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

При изучении этой статьи необходимо выполнить несколько действий.

:heavy_check_mark: При выполнении действий, описанных в разделе [Назначение приложению роли](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) этой статьи, не забудьте назначить приложению **роль участника хранилища BLOB-объектов**.

:heavy_check_mark: При выполнении действий, описанных в разделе [Получение значений для входа](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) этой статьи, вставьте идентификатор клиента, код приложения и значения ключа аутентификации в текстовый файл. Они вам скоро понадобятся.
Сначала создайте записную книжку в рабочей области Azure Databricks, а затем выполните фрагменты кода для создания файловой системы в учетной записи хранения.

## <a name="extract-data-from-the-data-lake-store"></a>Извлечение данных из Data Lake Store

В этом разделе мы создадим записную книжку в рабочей области Azure Databricks, а затем выполним фрагменты кода для извлечения данных из Data Lake Store в Azure Databricks.

1. На [портале Azure](https://portal.azure.com) перейдите к созданной службе Azure Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

2. В левой области выберите **Рабочая область**. В раскрывающемся списке **Рабочая область** выберите **Создать** > **Notebook** (Записная книжка).

    ![Создание записной книжки в Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Создание записной книжки в Databricks")

3. В диалоговом окне **создания записной книжки** введите имя записной книжки. Выберите **Scala** в качестве языка, а затем выберите созданный ранее кластер Spark.

    ![Предоставление сведений для записной книжки в Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Предоставление сведений для записной книжки в Databricks")

4. Нажмите кнопку **Создать**.

5. Скопируйте следующий блок кода и вставьте его в первую ячейку.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   ```

6. В этом блоке кода замените значения заполнителя `application-id`, `authentication-id` и `tenant-id` значениями, полученными после завершения действий в разделах статьи "Руководство. Извлечение, преобразование и загрузка данных с помощью Azure Databricks". Замените значение заполнителя `storage-account-name` именем вашей учетной записи хранения.

7. Нажмите клавиши **SHIFT + ВВОД**, чтобы запустить код в этом блоке.

8. Теперь можно загрузить пример JSON-файла в виде кадра данных в Azure Databricks. Вставьте следующий код в новую ячейку. Замените значения заменителей, показанные в скобках, собственными.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Замените значение заполнителя `file-system-name` именем, которое вы дали своей файловой системе в Обозревателе службы хранилища.

   * Замените заполнитель `storage-account-name` именем вашей учетной записи хранения.

9. Нажмите клавиши **SHIFT + ВВОД**, чтобы запустить код в этом блоке.

10. Чтобы просмотреть содержимое кадра данных, выполните следующий код:

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

## <a name="transform-data-in-azure-databricks"></a>Преобразование данных в Azure Databricks.

Необработанный файл с примером данных **small_radio_json.json** содержит сведения о слушателях радиостанции и имеет множество столбцов. В этом разделе мы преобразуем данные, чтобы извлечь только определенные столбцы из набора данных.

1. Сначала извлеките только столбцы **firstName**, **lastName**, **gender**, **location** и **level** из созданного кадра данных.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
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

2. Эти данные можно еще преобразовывать, переименовав столбец **level** на **subscription_type**.

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>Загрузка данных в хранилище данных Azure SQL

В этом разделе преобразованные данные отправляются в хранилище данных SQL Azure. С помощью соединителя Хранилища данных SQL Azure для Azure Databricks можно напрямую отправить кадр данных в виде таблицы в хранилище данных SQL.

Как упоминалось ранее, соединитель Хранилища данных SQL использует хранилище BLOB-объектов Azure в качестве временного хранилища для передачи данных между Azure Databricks и Хранилищем данных SQL Azure. Таким образом сначала нужно предоставить конфигурацию для подключения к учетной записи хранения. Вы уже должны были создать учетную запись, выполняя предварительные требования для этой статьи.

1. Предоставьте конфигурацию для получения доступа к учетной записи хранения Azure из Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Укажите временную папку, которая будет использоваться при перемещении данных между Azure Databricks и Хранилищем данных SQL Azure.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Запустите следующий фрагмент кода, чтобы сохранить ключ доступа к хранилищу BLOB-объектов Azure в конфигурации. Благодаря этому действию вам не придется хранить ключ доступа в записной книжке в виде обычного текста.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Укажите значения для подключения к экземпляру хранилища данных SQL Azure. Вы должны были создать хранилище данных SQL, выполняя требования для этой статьи.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Запустите следующий фрагмент кода, чтобы загрузить преобразованный кадр данных **renamedColumnsDF** в качестве таблицы в хранилище данных SQL. Этот фрагмент кода создает таблицу с именем **SampleTable** в базе данных SQL.

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

6. Подключитесь к базе данных SQL и убедитесь, что вы видите базу данных **SampleTable**.

   ![Проверка примера таблицы](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Проверка примера таблицы")

7. Выполните запрос SELECT, чтобы проверить содержимое таблицы. В таблице должны быть те же данные, что и в кадре данных **renamedColumnsDF**.

    ![Проверка содержимого примера таблицы](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Проверка содержимого примера таблицы")

## <a name="clean-up-resources"></a>Очистка ресурсов

После выполнения заданий из этого руководства вы можете завершить работу кластера. В рабочей области Azure Databricks в области слева выберите **Кластеры**. Для завершения работы кластера в разделе **Действия** наведите указатель мыши на многоточие (...) и выберите значок **Завершить**.

![Завершение работы кластера Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Stop a Databricks cluster")

Если не завершить работу кластера вручную, она завершится автоматически, если во время создания кластера вы установили флажок **Terminate after \_\_ minutes of inactivity** (Завершить через __ минут бездействия). В этом случае работа кластера автоматически завершается, если он был неактивным в течение определенного времени.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание службы Azure Databricks.
> * Создание кластера Spark в Azure Databricks.
> * Создание записной книжки в Azure Databricks.
> * Извлечение данных из учетной записи Data Lake Storage 2-го поколения.
> * Преобразование данных в Azure Databricks.
> * Загрузка данных в хранилище данных Azure SQL

Перейдите к следующему руководству, чтобы узнать о потоковой передачи данных в реальном времени в Azure Databricks с помощью Центров событий Azure.

> [!div class="nextstepaction"]
>[Потоковая передача данных в Azure Databricks при помощи службы "Центры событий"](databricks-stream-from-eventhubs.md)
