---
title: Учебник по внедрению Azure Databricks с помощью конечной точки Cosmos DB
description: В этом руководстве описывается, как внедрить Azure Databricks в виртуальную сеть с использованием конечной точки службы с поддержкой Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706164"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Руководство по Внедрение Azure Databricks с помощью конечной точки Cosmos DB

В этом руководстве описывается, как реализовать среду Databricks, внедренную в виртуальную сеть, с помощью конечной точки службы с поддержкой Cosmos DB.

Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание рабочей области Azure Databricks в виртуальной сети.
> * Создание конечной точки службы Cosmos DB.
> * Создание учетной записи Cosmos DB и импорт данных.
> * Создание кластера Azure Databricks.
> * Отправка запроса к Cosmos DB из записной книжки Azure Databricks.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать, сделайте следующее:

* Создайте [рабочую область Azure Databricks в виртуальной сети](quickstart-create-databricks-workspace-vnet-injection.md).

* Скачайте [соединитель Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Скачайте демонстрационные данные с сайта [Национального центра природоохранных данных NOAA](https://www.ncdc.noaa.gov/stormevents/). Выберите штат или область и выберите **Search** (Найти). На следующей странице примите значения по умолчанию и выберите **Search** (Найти). Затем выберите **CSV Download** (Скачать CSV-файл) в левой части страницы, чтобы скачать результаты.

* Скачайте [предварительно скомпилированный двоичный файл](https://aka.ms/csdmtool) средства переноса данных Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Создание конечной точки службы Cosmos DB.

1. После развертывания рабочей области Azure Databricks к виртуальной сети перейдите к этой виртуальной сети на [портале Azure](https://portal.azure.com). Обратите внимание на общедоступные и частные подсети, которые были созданы при развертывании Databricks.

   ![Подсети виртуальной сети](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Выберите *public-subnet* и создайте конечную точку службы Cosmos DB. Затем нажмите кнопку **Сохранить**.
   
   ![Добавление конечной точки службы Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Создание учетной записи Cosmos DB

1. Перейдите на портал Azure. Вверху в левой части экрана выберите **Создать ресурс > Базы данных > Azure Cosmos DB**.

2. Заполните параметры в разделе **Сведения об экземпляре** на вкладке **Основы** следующим образом.

   |Параметр|Значение|
   |-------|-----|
   |Subscription|*ваша подписка*|
   |Группа ресурсов|*ваша группа ресурсов*|
   |Имя учетной записи|db-vnet-service-endpoint|
   |API|Core (SQL)|
   |Location|Запад США|
   |Геоизбыточность|Disable|
   |Выполнение операций записи в нескольких регионах|Включение|

   ![Добавление конечной точки службы Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Выберите вкладку **Сеть** и настройте виртуальную сеть. 

   a. Выберите виртуальную сеть, созданную при выполнении предварительных условий, а затем выберите *public-subnet*. Обратите внимание на примечание к *private-subnet*: *Конечная точка "Microsoft AzureCosmosDB" отсутствует"* . Причина этого состоит в том, что вы включили конечную точку службы Cosmos DB только в подсети на *public-subnet*.

   b. Убедитесь, что параметр **Разрешить доступ с портала Azure** включен. Этот параметр позволяет получить доступ к вашей учетной записи Cosmos DB на портале Azure. Если этот параметр имеет значение **Запретить**, при попытке доступа к вашей учетной записи возникнут ошибки. 

   > [!NOTE]
   > Хотя это и не требуется в рамках данного руководства, можно также включить параметр *Разрешить доступ с моего IP-адреса*, если требуется возможность доступа к учетной записи Cosmos DB с локального компьютера. Например, если вы подключаетесь к учетной записи с помощью пакета SDK для Cosmos DB, этот параметр необходимо включить. Если он отключен, вы получите сообщения об ошибке "Отказано в доступе".

   ![Параметры сети учетной записи Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Выберите **Отзыв и создание**, затем щелкните **Создать**, чтобы создать учетную запись Cosmos DB внутри виртуальной сети.

5. После создания учетной записи Cosmos DB выберите **Ключи** в разделе **Параметры**. Скопируйте основную строку подключения и сохраните ее в файл с помощью текстового редактора для последующего использования.

    ![Страница ключей учетной записи Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Выберите **Обозреватель данных** и **Новая коллекция**, чтобы добавить новую базу данных и коллекцию в учетную запись Cosmos DB.

    ![Новая коллекция Cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Передача данных в Cosmos DB

1. Откройте оснащенную графическим интерфейсом версию [средства переноса данных для Cosmos DB](https://aka.ms/csdmtool) — **Dtui.exe**.

    ![Средство переноса данных Azure Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. На вкладке **Source Information** (Сведения об источнике) из раскрывающегося списка **Import from** (Импорт из) выберите **CSV File(s)** (CSV-файлы). Затем выберите **Add Files** (Добавить файлы) и добавьте CSV-файл данных о бурях, скачанный в качестве необходимого компонента.

    ![Средство переноса данных Azure Cosmos DB: сведения об источнике](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. На вкладке **Target Information** (Сведения о цели) введите строку подключения. Она имеет формат `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. AccountEndpoint и AccountKey включены в основную строку подключения, которую вы сохранили в предыдущем разделе. Добавьте `Database=<your database name>` в конец строки подключения, а затем выберите **Verify** (Проверить). Затем добавьте имя коллекции и ключ секции.

    ![Средство переноса данных Azure Cosmos DB: сведения о цели](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Нажимайте кнопку **Next** (Далее), пока не перейдете на страницу "Summary" (Сводка). Выберите **Import** (Импортировать).

## <a name="create-a-cluster-and-add-library"></a>Создание кластера и добавление библиотеки

1. На [портале Azure](https://portal.azure.com) перейдите к службе Azure Databricks, а затем выберите **Launch Workspace** (Запустить рабочую область).

   ![Запуск рабочей области Databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Создайте кластер. Выберите имя кластера и примите остальные значения по умолчанию.

   ![Параметры нового кластера](./media/service-endpoint-cosmosdb/create-cluster.png)

3. После создания кластера перейдите на его страницу и выберите вкладку **Библиотеки**. Выберите **Установить новое** и передайте JAR-файл соединителя Spark, чтобы установить библиотеку.

    ![Установка библиотеки соединителя Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Убедитесь, что библиотека была установлена, на вкладке **Библиотеки**.

    ![Вкладка "Библиотеки" кластера Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Отправка запроса к Cosmos DB из записной книжки Databricks

1. Перейдите в рабочую область Azure Databricks и создайте записную книжку Python.

    ![Создание записной книжки Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Запустите приведенный ниже код Python, чтобы задать конфигурацию подключения к Cosmos DB. Измените значения **Endpoint**, **Masterkey**, **Database** и **Collection** соответствующим образом.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Используйте приведенный ниже код Python, чтобы загрузить данные и создать временное представление.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Используйте приведенную ниже магическую команду для выполнения инструкции SQL, возвращающей данные.

    ```python
    %sql
    select * from storm
    ```

    Вы успешно подключили рабочую область Databricks, внедренную в виртуальную сеть, к ресурсу Cosmos DB с поддержкой конечной точки службы. Чтобы узнать больше о том, как подключиться к базе данных Cosmos DB, обратитесь к разделу [Azure Cosmos DB Connector for Apache Spark](https://github.com/Azure/azure-cosmosdb-spark) (Соединитель Azure Cosmos DB для Apache Spark).

## <a name="clean-up-resources"></a>Очистка ресурсов

Ставшие ненужными группу ресурсов, рабочую область Azure Databricks и все связанные ресурсы можно удалить. Удаление задания позволяет избежать ненужных затрат. Если вы планируете использовать рабочую область Azure Databricks в будущем, можно остановить кластер и перезапустить его позже. Если вы не собираетесь использовать рабочую область Azure Databricks, удалите все ресурсы, созданные в ходе работы с этим руководством, выполнив следующие шаги.

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданной группы ресурсов.

2. На странице группы ресурсов выберите **Удалить**, в текстовом поле введите имя ресурса для удаления и еще раз щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули рабочую область Azure Databricks в виртуальной сети и использовали соединитель Spark для Cosmos DB для запроса данных Cosmos DB из Databricks. Чтобы узнать больше о работе с Azure Databricks в виртуальной сети, перейдите к руководству по использованию SQL Server с Azure Databricks.

> [!div class="nextstepaction"]
> [Руководство. Запрос контейнера Docker Linux для SQL Server в виртуальной сети из записной книжки Azure Databricks](vnet-injection-sql-server.md)
