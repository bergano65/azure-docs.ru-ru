---
title: Краткое руководство. Выполнение запроса из учетной записи API SQL для Azure Cosmos DB с помощью Node.js
description: Сведения о том, как с помощью Node.js создать приложение, которое подключается к учетной записи API SQL для Azure Cosmos DB и запрашивает данные.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: 44cdd4307be56d864afb45d619958cc59a3fa978
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220519"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Краткое руководство. Подключение и выполнение запроса к данным из учетной записи API SQL для Azure Cosmos DB с помощью Node.js

> [!div class="op_single_selector"]
> * [.NET версии 3](create-sql-api-dotnet.md)
> * [.NET версии 4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

В этом кратком руководстве показано, как с помощью приложения Node.js подключиться к учетной записи [SQL API](sql-api-introduction.md) для Azure Cosmos DB. Затем, используя запросы SQL Azure Cosmos DB, можно запросить данные и управлять ими. Приложение Node.js, создаваемое в этой статье, использует [пакет SDK для JavaScript SQL](sql-api-sdk-node.md). В этом кратком руководстве используется версия 2.0 [пакета SDK для JavaScript](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Кроме того, сделайте следующее:
    * [Node.js](https://nodejs.org/en/) версии 6.0.0 или более поздней.
    * [Git](https://git-scm.com/)

## <a name="create-a-database"></a>Создание базы данных 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Добавление контейнера

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Добавление демонстрационных данных

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Обращение к данным

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение Node.js из GitHub. Задайте строку подключения и выполните ее.

1. Откройте командную строку, создайте папку git-samples, а затем закройте окно командной строки.

    ```bash
    md "C:\git-samples"
    ```

2. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в новую папку для установки примера приложения.

    ```bash
    cd "C:\git-samples"
    ```

3. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Просмотр кода

Этот шаг не является обязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных Azure Cosmos, изучите приведенные ниже фрагменты кода. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string). 

Обратите внимание, что если вам знакома предыдущая версия пакета SDK для JavaScript, вы уже видели термины "коллекция" и "документ". Так как Azure Cosmos DB поддерживает [несколько моделей API](https://docs.microsoft.com/azure/cosmos-db/introduction), пакет SDK для JavaScript версии 2.0+ использует общий термин "контейнер", который может быть коллекцией, графом или таблицей, и термин "элемент" для описания содержимого контейнера.

Приведенные ниже фрагменты кода взяты из файла **app.js**.

* Инициализирован объект `CosmosClient`.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Создайте базу данных Azure Cosmos.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Создается контейнер (коллекция) в базе данных.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Создается элемент (документ).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* SQL-запрос через JSON выполняется в базе данных семейства. Запрос возвращает все дочерние элементы семейства "Андерсон". 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить сведения о строке подключения учетной записи Azure Cosmos. Скопируйте строку подключения в приложение, чтобы с ее помощью подключиться к базе данных.

1. На [портале Azure](https://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos и на левой панели навигации щелкните **Ключи**, а затем выберите **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать универсальный код ресурса (URI) и первичный ключ в файл `config.js`.

    ![Просмотр и копирование ключа доступа на портале Azure, колонка "Ключи"](./media/create-sql-api-dotnet/keys.png)

2. Откройте файл `config.js`. 

3. Скопируйте значение универсального кода ресурса (URI) на портале (с помощью кнопки копирования) и добавьте его в качестве значения параметра ключа конечной точки в файле `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Затем скопируйте значение первичного ключа с портала и добавьте его в качестве значения параметра `config.key` в файле `config.js`. Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

    `config.key = "FILLME"`
    
## <a name="run-the-app"></a>Запуск приложения

1. В окне терминала запустите `npm install`, чтобы установить необходимые модули npm.

2. Запустите `node app.js` в окне терминала, чтобы запустить приложение Node.

Вернитесь в обозреватель данных, где вы можете запрашивать и изменять новые данные, а также работать с ними.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве описано, как создать учетную запись Azure Cosmos и контейнер с помощью обозревателя данных, а также как запустить приложение. Теперь вы можете импортировать дополнительные данные в базу данных Azure Cosmos. 

> [!div class="nextstepaction"]
> [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md)


