---
title: Руководство по Node.js для API SQL в Azure Cosmos DB
description: Руководство по Node.js, в котором описывается, как подключиться к Azure Cosmos DB и выполнить ее запрос с помощью API SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: fe925ed408f64424de8da98f6e182a06a41bf015
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58075055"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Руководство. Создание консольного приложения Node.js с помощью пакета SDK JavaScript для управления данными API SQL для Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (предварительная версия)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (предварительная версия)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Как у разработчика у вас могут быть приложения, использующие данные документов NoSQL. Вы можете использовать учетную запись API SQL в Azure Cosmos DB для хранения этих данных документов и получения доступа к ним. В этом руководстве показано, как создать консольное приложение Node.js, которое создает ресурсы Azure Cosmos DB, и запросить эти ресурсы.

Изучив данный учебник, вы научитесь:

> [!div class="checklist"]
> * создавать учетную запись Azure Cosmos DB и подключаться к ней;
> * настраивать свое приложение;
> * создавать базу данных;
> * Создайте контейнер.
> * добавлять элементы в контейнер;
> * выполнять операции CRUD с элементами, контейнером и базой данных.

## <a name="prerequisites"></a>Предварительные требования 

Убедитесь, что у вас есть указанные ниже ресурсы.

* Активная учетная запись Azure. Если у вас нет такой учетной записи, вы можете зарегистрироваться для использования [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) 6.0.0 или более поздней версии.

## <a name="create-azure-cosmos-db-account"></a>Создание учетной записи Azure Cosmos DB

Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка приложения Node.js](#SetupNode). Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [настройке приложения Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Настройка приложения Node.js

Прежде чем вы начнете писать код для создания приложения, можете создать платформу для приложения. Выполните следующие шаги, чтобы настроить приложение Node.js, содержащее код платформы:

1. Откройте удобный для вас терминал.
2. Перейдите в папку или каталог, где вы хотите сохранить приложение Node.js.
3. Создайте два пустых файла JavaScript с помощью следующих команд:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux или OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Создание и инициализация файла `package.json`. Используйте следующую команду:
   * ```npm init -y```

5. Установите модуль @azure/cosmos с помощью npm. Используйте следующую команду:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Настройка конфигурации приложения

Теперь, когда ваше приложение существует, необходимо убедиться, что оно может взаимодействовать с Azure Cosmos DB. Как показано в следующих шагах, обновляя некоторые параметры конфигурации, вы можете настроить свое приложение для взаимодействия с Azure Cosmos DB.

1. Откройте файл ```config.js``` в предпочитаемом текстовом редакторе.

1. Скопируйте и вставьте приведенный ниже фрагмент кода, после чего задайте URI конечной точки и первичный ключ Azure Cosmos DB в качестве значений свойств ```config.endpoint``` и ```config.primaryKey```. Обе эти конфигурации можно найти на [портале Azure](https://portal.azure.com).

   ![Снимок экрана получения ключей на портале Azure][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Скопируйте данные ```database```, ```container``` и ```items```, а затем вставьте их в объект ```config``` ниже, где задавались значения свойств ```config.endpoint``` и ```config.primaryKey```. Если у вас уже есть данные, которые вы хотите сохранить в своей базе данных, вы можете использовать средство переноса данных в Azure Cosmos DB вместо определения данных здесь.

   ```javascript
   var config = {}

   config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
   config.primaryKey = "~your primary key here~";

   config.database = {
      "id": "FamilyDatabase"
   };

   config.container = {
     "id": "FamilyContainer"
   };

   config.items = {
      "Andersen": {
          "id": "Anderson.1",
          "lastName": "Andersen",
          "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
      }
   };
   ```

   Пакет SDK для JavaScript использует универсальные термины: *контейнер* и *элемент*. Контейнер может представлять собой коллекцию, граф или таблицу. Элемент может представлять собой документ, ребро, вершину или запись и является содержимым внутри контейнера. 

1. Теперь экспортируйте объект ```config```, чтобы на него можно было ссылаться в файле ```app.js```.

   ```javascript
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>Подключение к учетной записи Azure Cosmos DB

1. Откройте пустой файл ```app.js``` в текстовом редакторе. Скопируйте и вставьте следующий код, чтобы импортировать модуль ```@azure/cosmos``` и созданный модуль ```config```.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Скопируйте и вставьте код, чтобы использовать ранее сохраненные значения ```config.endpoint``` и ```config.primaryKey``` и создать CosmosClient.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> При подключении к **эмулятору Cosmos DB** отключите проверку SSL, создав пользовательскую политику подключения.
>   ```
>   const connectionPolicy = new cosmos.ConnectionPolicy ()
>   connectionPolicy.DisableSSLVerification = true
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

Теперь, когда у нас есть код для инициализации клиента Azure Cosmos DB, давайте рассмотрим принципы работы с ресурсами Azure Cosmos DB.

## <a name="create-a-database"></a>Создание базы данных

1. Скопируйте и вставьте приведенный ниже код, чтобы задать идентификатор базы данных и идентификатор контейнера. По этим идентификаторам клиент Azure Cosmos DB найдет нужные базу данных и контейнер.

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   Вы можете создать базу данных с помощью метода `createIfNotExists` или функции create класса **Databases**. База данных представляет собой логический контейнер элементов, разделенных между контейнерами. 

2. Скопируйте и вставьте методы **createDatabase** и **readDatabase** в файл app.js в определениях ```databaseId``` и ```containerId```. Функция **createDatabase** создаст базу данных с идентификатором ```FamilyDatabase```, указанным из объекта ```config```, если она не была создана ранее. Функция **readDatabase** считает определение базы данных, чтобы удостовериться в ее наличии.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Скопируйте и вставьте приведенный ниже код, в котором вы задали функции **createDatabase** и **readDatabase**, для добавления функции вспомогательного приложения **exit**, которая выведет сообщение о выходе. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Скопируйте и вставьте приведенный ниже код, в котором вы задали функцию **exit**, для вызова функций **createDatabase** и **readDatabase**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   На этом этапе ваш код в ```app.js``` теперь должен выглядеть так, как показано ниже:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Создание контейнера

Затем создайте контейнер в учетной записи Azure Cosmos DB, чтобы можно было хранить и запрашивать данные. 

> [!WARNING]
> Создание контейнера связано с ценовыми требованиями. Посетите нашу [страницу с ценами](https://azure.microsoft.com/pricing/details/cosmos-db/), чтобы знать, чего следует ожидать.

Контейнер можно создать с помощью метода `createIfNotExists` или функции create из класса **Containers**.  Контейнер состоит из элементов (которые в случае API SQL являются документами JSON) и связанной логики приложения JavaScript.

1. Скопируйте и вставьте функции **createContainer** и **readContainer** после функции **readDatabase** в файле app.js. Функция **createContainer** создаст контейнер с идентификатором ```containerId```, указанным из объекта ```config```, если он не был создан ранее. Функция **readContainer** будет считывать определение контейнера, чтобы проверить наличие контейнера.

   ```javascript
   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Скопируйте и вставьте код после вызова функции **readDatabase**, чтобы выполнить функции **createContainer** и **readContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   На этом этапе ваш код в ```app.js``` теперь должен выглядеть так, как показано ниже:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
     const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
     console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Создание элемента

Элемент можно создать с помощью функции create класса **Items**. При использовании API SQL элементы проецируются как документы, которые являются содержимым JSON, определенным пользователем (произвольным). Теперь можно вставить элемент в Azure Cosmos DB.

1. Скопируйте и вставьте функцию **createFamilyItem** после функции **readContainer**. Функция **createFamilyItem** создает элементы, содержащие данные JSON, сохраненные в объекте ```config```. Перед созданием элемента нужно проверить, нет ли элемента с таким же идентификатором.

   ```javascript
   /**
   * Create family item if it does not exist
   */
   async function createFamilyItem(itemBody) {
     try {
         // read the item to see if it exists
         const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
         console.log(`Item with family id ${itemBody.id} already exists\n`);
     }
     catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
     }
   };
   ```

1. Скопируйте и вставьте код после вызова функции **readContainer**, чтобы выполнить функцию **createFamilyItem**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Запрос ресурсов Azure Cosmos DB
Azure Cosmos DB поддерживает полнофункциональные запросы к документам JSON, хранящимся в каждом контейнере. В следующем примере кода показан выполняемый запрос к документам в контейнере.

1. Скопируйте и вставьте функцию **queryContainer** после функции **createFamilyItem** в файле app.js. Azure Cosmos DB поддерживает SQL-подобные запросы, как показано ниже.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Скопируйте и вставьте код после вызовов функции **createFamilyItem**, чтобы выполнить функцию **queryContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. В окне терминала перейдите к файлу ```app.js``` и выполните эту команду:

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Замена элемента
Azure Cosmos DB поддерживает замену содержимого элементов.

1. Скопируйте и вставьте функцию **replaceFamilyItem** после функции **queryContainer** в файле app.js. Обратите внимание, что мы сменили значение свойства grade дочернего элемента с 5 на 6.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
      const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
   };
   ```

1. Скопируйте и вставьте код после вызова функции **queryContainer**, чтобы выполнить функцию **replaceFamilyItem**. Кроме того, добавьте код, чтобы еще раз вызвать функцию **queryContainer** и проверить успешное изменение элемента.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. В окне терминала перейдите к файлу ```app.js``` и выполните эту команду:

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Удаление элемента

Azure Cosmos DB поддерживает удаление элементов JSON.

1. Скопируйте функцию **deleteFamilyItem** и вставьте ее после функции **replaceFamilyItem**.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Скопируйте и вставьте код после вызова второй функции **queryContainer**, чтобы выполнить функцию **deleteFamilyItem**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Удаление базы данных

Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (контейнеров, элементов и т. д.).

1. Скопируйте функцию **cleanup** и вставьте ее после функции **deleteFamilyItem**, чтобы удалить базу данных и все ее дочерние ресурсы.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Скопируйте код и вставьте его после вызова функции **deleteFamilyItem**, чтобы выполнить функцию **cleanup**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Запуск приложения Node.js

В целом, код должен выглядеть следующим образом:

```javascript
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

```bash 
node app.js
```

Должны отобразиться выходные данные вашего приложения. Выходные данные должны соответствовать примеру текста ниже.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>Получение полного решения для руководства по Node.js 

Если вы не располагаете временем, чтобы выполнить шаги из этого руководства, или просто хотите скачать код, это можно сделать на портале [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). 

Чтобы запустить решение по началу работы, которое содержит все коды из этой статьи, вам понадобится следующее: 

* [Учетная запись Azure Cosmos DB][create-account] 
* Решение для [начала работы](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), доступное на GitHub. 

Установите зависимости проекта с помощью npm. Используйте следующую команду: 

* ```npm install``` 

Затем в файле ```config.js``` обновите значения config.endpoint и config.primaryKey, как описано в разделе [Шаг 3. Настройка конфигурации приложения](#Config).  

Затем в окне терминала перейдите к файлу ```app.js``` и выполните эту команду:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Можно удалить группу ресурсов, учетную запись Azure Cosmos DB и все связанные ресурсы, когда они больше не нужны. Для этого выберите группу ресурсов, которая использовалась для учетной записи Azure Cosmos DB, выберите **Удалить**, а затем подтвердите имя удаляемой группы ресурсов.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Мониторинг Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
