---
title: Используйте Studio 3T для подключения к API-разбитому API Azure Cosmos DB для MongoDB
description: Узнайте, как подключиться к API-разбитому API Azure Cosmos DB для MongoDB с помощью Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548822"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Подключение к учетной записи Azure Cosmos с помощью Studio 3T

Чтобы подключиться к API-разбитому API Azure Cosmos DB для MongoDB с помощью Studio 3T, необходимо:

* Скачать и установить [Studio 3T](https://studio3t.com/).
* Иподробнь сведения о [строке соединения учетной записи](connect-mongodb-account.md) Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Создание подключения в Studio 3T

Чтобы добавить свою учетную запись Azure Cosmos в менеджер соединения Studio 3T, используйте следующие действия:

1. Извлеките информацию о подключении к API-разбитому API Azure Cosmos DB для учетной записи MongoDB, используя инструкции в [приложении Connect a MongoDB в статье Azure Cosmos DB.](connect-mongodb-account.md)

    ![Скриншот страницы строки соединения](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Щелкните **Connect** (Подключиться), чтобы открыть диспетчер подключений, и нажмите кнопку **New Connection** (Новое подключение).

    ![Скриншот менеджера по подключению Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. В окне **нового подключения** на вкладке **«Сервер»** введите HOST (ФЗДН) учетной записи Azure Cosmos и PORT.

    ![Скриншот вкладки сервера сервера менеджера соединения Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. В окне **New Connection** (Новое подключение) на вкладке **Authentication** (Аутентификация) выберите режим аутентификации **Basic (MONGODB-CR or SCARM-SHA-1)** (Базовая (MONGODB CR или SCARM-SHA-1)), а также введите имя пользователя и пароль.  Подтвердите базу данных по умолчанию для проверки подлинности (admin) или укажите другое значение.

    ![Скриншот вкладки менеджера аутентификации Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. В окне **New Connection** (Новое подключение) на вкладке **SSL** установите флажок **Use SSL protocol to connect** (Использовать для подключения протокол SSL) и переключатель **Accept server self-signed SSL certificates** (Принимать самозаверяющие SSL-сертификаты сервера).

    ![Скриншот вкладки менеджера sSL-студии 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Нажмите кнопку **Test Connection** (Проверить подключение), чтобы проверить сведения о подключении. Затем нажмите кнопку **ОК**, чтобы вернуться в окно "New Connection" (Новое подключение), а затем нажмите кнопку **Save** (Сохранить).

    ![Скриншот окна тестового соединения Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Использование Studio 3T для создания базы данных, коллекции и документов
Чтобы создать базу данных, коллекцию и документы с помощью Studio 3T, выполните следующие действия.

1. В **диспетчере подключений** выделите нужное подключение и щелкните **Connect** (Подключиться).

    ![Скриншот менеджера по подключению Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Щелкните узел правой кнопкой мыши и выберите **Add Database** (Добавить базу данных).  Укажите имя базы данных и нажмите кнопку **ОК**.

    ![Скриншот варианта базы данных Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Щелкните правой кнопкой мыши базу данных и выберите **Add Collection** (Добавить коллекцию).  Укажите имя коллекции и нажмите кнопку **Создать**.

    ![Скриншот варианта коллекции Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Щелкните пункт меню **Collection** (Коллекция), затем щелкните **Add Document** (Добавить документ).

    ![Скриншот элемента меню Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. В диалоговом окне "Добавление документа" вставьте следующий текст и щелкните **Добавить документ**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Добавьте еще один документ со следующим содержимым:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Выполните пробный запрос. Например, попробуйте найти семьи с фамилией Andersen и вернуть для них поля parents и state.

    ![Скриншот результатов запроса Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.
