---
title: Краткое руководство. Создание базы данных документов с помощью Java и Azure Cosmos DB
description: В этом кратком руководстве представлен пример кода Java, который можно использовать для подключения и выполнения запросов к API SQL в Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8c2ae82bae8457a1c715f160994c7a0da94193ff
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134490"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Краткое руководство. Создание приложения Java для управления данными API SQL для Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET версии 3](create-sql-api-dotnet.md)
> * [.NET версии 4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

В этом кратком руководстве вы научитесь создавать учетную запись API SQL Azure Cosmos DB и управлять ею на портале Azure, а также с помощью приложения Java, клонированного с GitHub. Сначала создайте учетную запись API SQL для Azure Cosmos DB с помощью портала Azure, затем создайте приложение Java с использованием пакета SDK Java для SQL и добавьте ресурсы в учетную запись Cosmos DB с помощью приложения Java. Azure Cosmos DB — это служба многомодельной базы данных, позволяющая быстро создавать и запрашивать документы, таблицы, пары "ключ-значение" и графовые базы данных, используя возможности глобального распределения и горизонтального масштабирования.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Или [воспользуйтесь пробной версией Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) без подписки Azure. Вы также можете воспользоваться [эмулятором Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) с URI `https://localhost:8081` и ключом `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Комплект SDK для Java (JDK) версии 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Укажите для переменной среды `JAVA_HOME` папку, в которой установлен комплект JDK.
- [Двоичный архив Maven](https://maven.apache.org/download.cgi). В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
- [Git](https://www.git-scm.com/downloads). В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Перед созданием базы данных документов необходимо создать в Azure Cosmos DB учетную запись API SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Добавление контейнера

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Добавление демонстрационных данных

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Обращение к данным

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом. Давайте клонируем приложение API SQL из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Просмотр кода

Это необязательный шаг. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Вы можете сразу перейти к [запуску приложения](#run-the-app). 

* Инициализация `CosmosClient`. `CosmosClient` является логическим представлением службы баз данных Azure Cosmos на стороне клиента. Этот клиент позволяет настраивать и выполнять запросы к службе.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* Создание объекта CosmosDatabase.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Создание объекта CosmosContainer.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Создание элемента с помощью метода `createItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Считывание точек выполняется с помощью методов `getItem` и `read`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* SQL-запросы через JSON выполняются с помощью метода `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Запустите приложение

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения, и запустите приложение, используя данные конечной точки. Так вы обеспечите обмен данными между приложением и размещенной базой данных.

1. В окне терминала Git выполните команду `cd`, чтобы перейти к папке с примером кода.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. В окне терминала Git введите приведенную ниже команду, чтобы установить необходимые пакеты Java.

    ```bash
    mvn package
    ```

3. В окне терминала Git используйте следующую команду, чтобы запустить приложение Java (замените YOUR_COSMOS_DB_HOSTNAME заключенным в кавычки значением URI с портала, а YOUR_COSMOS_DB_MASTER_KEY — заключенным в кавычки первичным ключом с портала).

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    В окне терминала отобразится уведомление о создании базы данных FamilyDB. 
    
4. Приложение создает базу данных с именем `AzureSampleFamilyDB`.
5. Приложение создает контейнер с именем `FamilyContainer`.
6. Приложение выполнит считывание точек по идентификаторам объектов и значению ключа раздела (в нашем примере — lastName). 
7. Приложение будет запрашивать элементы для получения всех семейств с фамилией из списка (Andersen, Wakefield, Johnson).

7. Это приложение не позволяет удалять созданные ресурсы. Вернитесь на портал и [удалите там ресурсы](#clean-up-resources)  из учетной записи, чтобы за них не взималась плата.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве было показано, как создать учетную запись API SQL Azure Cosmos, базу данных документов и контейнер с помощью обозревателя данных и как запустить приложение Java, чтобы эти операции выполнялись программно. Теперь вы можете импортировать дополнительные данные в учетную запись Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md)
