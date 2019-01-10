---
title: Подключение к API Azure Cosmos DB для MongoDB с помощью Studio 3T
titleSuffix: Azure Cosmos DB
description: Сведения о подключении к Cosmos DB с помощью Studio 3T и API Azure Cosmos DB для MongoDB
keywords: mongochef, studio 3T
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 396db84186e00fefe14ac55b9a36497c6674cb43
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811245"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Подключение к учетной записи Cosmos с помощью Studio 3T

Чтобы подключиться к учетной записи Cosmos с помощью API Azure Cosmos DB для MongoDB, выполните следующие действия.

* Загрузка и установка [Studio 3T](https://studio3t.com/)
* Получите сведений о [строке подключения](connect-mongodb-account.md) Cosmos DB.

## <a name="create-the-connection-in-studio-3t"></a>Создание подключения в Studio 3T
Чтобы добавить в диспетчер подключений Studio 3T учетную запись Cosmos, сделайте следующее:

1. получите сведения о подключении для учетной записи Cosmos, настроенной с помощью API Azure Cosmos DB для MongoDB, следуя инструкциям из статьи [Подключение приложения MongoDB к Azure Cosmos DB](connect-mongodb-account.md).

    ![Снимок экрана со страницей строки подключения](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Щелкните **Connect** (Подключиться), чтобы открыть диспетчер подключений, и нажмите кнопку **New Connection** (Новое подключение).

    ![Снимок экрана диспетчера подключений Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. В окне **New Connection** (Новое подключение) на вкладке **Server** (Сервер) укажите узел (полное доменное имя) учетной записи Azure Cosmos DB и порт.

    ![Снимок экрана с вкладкой Server (Сервер) диспетчера подключений Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. В окне **New Connection** (Новое подключение) на вкладке **Authentication** (Аутентификация) выберите режим аутентификации **Basic (MONGODB-CR or SCARM-SHA-1)** (Базовая (MONGODB CR или SCARM-SHA-1)), а также введите имя пользователя и пароль.  Подтвердите базу данных по умолчанию для проверки подлинности (admin) или укажите другое значение.

    ![Снимок экрана с вкладкой аутентификации диспетчера подключений Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. В окне **New Connection** (Новое подключение) на вкладке **SSL** установите флажок **Use SSL protocol to connect** (Использовать для подключения протокол SSL) и переключатель **Accept server self-signed SSL certificates** (Принимать самозаверяющие SSL-сертификаты сервера).

    ![Снимок экрана с вкладкой SSL диспетчера подключений Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Нажмите кнопку **Test Connection** (Проверить подключение), чтобы проверить сведения о подключении. Затем нажмите кнопку **ОК**, чтобы вернуться в окно "New Connection" (Новое подключение), а затем нажмите кнопку **Save** (Сохранить).

    ![Снимок экрана окна тестового подключения Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Использование Studio 3T для создания базы данных, коллекции и документов
Чтобы создать базу данных, коллекцию и документы с помощью Studio 3T, выполните следующие действия.

1. В **диспетчере подключений** выделите нужное подключение и щелкните **Connect** (Подключиться).

    ![Снимок экрана диспетчера подключений Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Щелкните узел правой кнопкой мыши и выберите **Add Database** (Добавить базу данных).  Укажите имя базы данных и нажмите кнопку **ОК**.

    ![Снимок экрана Studio 3T, параметр добавления базы данных](./media/mongodb-mongochef/AddDatabase1.png)
3. Щелкните правой кнопкой мыши базу данных и выберите **Add Collection** (Добавить коллекцию).  Укажите имя коллекции и нажмите кнопку **Создать**.

    ![Снимок экрана Studio 3T, параметр добавления коллекции](./media/mongodb-mongochef/AddCollection.png)
4. Щелкните пункт меню **Collection** (Коллекция), затем щелкните **Add Document** (Добавить документ).

    ![Снимок экрана Studio 3T, пункт меню для добавления документа](./media/mongodb-mongochef/AddDocument1.png)
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

    ![Снимок экрана Mongo Chef, результаты запроса](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.
