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
ms.openlocfilehash: d8b17472bb531ec799be227706261962d7914d68
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134457"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Краткое руководство. Подключение и выполнение запроса к данным из учетной записи API SQL для Azure Cosmos DB с помощью Node.js

> [!div class="op_single_selector"]
> * [.NET версии 3](create-sql-api-dotnet.md)
> * [.NET версии 4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

В этом кратком руководстве вы научитесь создавать учетную запись API SQL Azure Cosmos DB и управлять ею на портале Azure, а также с помощью приложения Node.js, клонированного с GitHub. Azure Cosmos DB — это служба многомодельной базы данных, позволяющая быстро создавать и запрашивать документы, таблицы, пары "ключ-значение" и графовые базы данных, используя возможности глобального распределения и горизонтального масштабирования.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Или [воспользуйтесь пробной версией Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) без подписки Azure. Вы также можете воспользоваться [эмулятором Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) с URI `https://localhost:8081` и ключом `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

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

Это необязательный шаг. Если вы хотите узнать, как создать в коде ресурсы базы данных Azure Cosmos, изучите приведенные ниже фрагменты кода. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string). 

Если вам знакома предыдущая версия пакета SDK для SQL на JavaScript, вы уже видели термины *коллекция* и *документ*. Так как Azure Cosmos DB поддерживает [несколько моделей API](introduction.md), [пакет SDK для JavaScript версии 2.0+](https://www.npmjs.com/package/@azure/cosmos) использует общий термин *контейнер*, который может быть коллекцией, графом или таблицей, и термин *элемент* для описания содержимого контейнера.

Приведенные ниже фрагменты кода взяты из файла *app.js*.

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

1. В учетной записи Azure Cosmos DB на [портале Azure](https://portal.azure.com/) выберите **Ключи** на панели навигации слева, а затем выберите **Ключи записи-чтения**. На следующем шаге нажмите кнопку копирования в правой части экрана, чтобы скопировать универсальный код ресурса (URI) и первичный ключ в файл *config.js*.

    ![Просмотр и копирование ключа доступа на портале Azure, колонка "Ключи"](./media/create-sql-api-dotnet/keys.png)

2. Откройте файл *config.js*. 

3. Скопируйте значение универсального кода ресурса (URI) на портале (с помощью кнопки копирования) и добавьте его в качестве значения параметра ключа конечной точки в файл *config.js*. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Затем скопируйте значение первичного ключа с портала и добавьте его в качестве значения `config.key` в файл *config.js*. Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>Запустите приложение

1. В окне терминала запустите `npm install`, чтобы установить необходимые модули npm.

2. Запустите `node app.js` в окне терминала, чтобы запустить приложение Node.

Вернитесь в обозреватель данных, где вы можете запрашивать и изменять новые данные, а также работать с ними.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описано, как создать учетную запись Azure Cosmos DB и контейнер с помощью обозревателя данных, а также как запустить приложение Node.js. Теперь можно импортировать дополнительные данные в учетную запись Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md)


