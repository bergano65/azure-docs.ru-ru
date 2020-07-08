---
title: Подключение к API Azure Cosmos DB для MongoDB с помощью Studio 3T
description: Узнайте, как подключиться к API Azure Cosmos DB для MongoDB с помощью 3T Studio.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 18a9f97d2cee9dd17345a1c8c0ae0efe442d79a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85799400"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Подключение к учетной записи Azure Cosmos с помощью 3T Studio

Чтобы подключиться к API Azure Cosmos DB для MongoDB с помощью 3T Studio, необходимо выполнить следующие действия.

* Скачайте и установите [Studio 3T](https://studio3t.com/).
* Сведения о [строке подключения](connect-mongodb-account.md) учетной записи Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Создание подключения в Studio 3T

Чтобы добавить учетную запись Azure Cosmos в Диспетчер соединений Studio 3T, выполните следующие действия.

1. Получите сведения о подключении для учетной записи API Azure Cosmos DB для MongoDB, следуя инструкциям в статье [Подключение приложения MongoDB к Azure Cosmos DB](connect-mongodb-account.md) .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Снимок экрана со страницей строки подключения":::

2. Щелкните **Connect** (Подключиться), чтобы открыть диспетчер подключений, и нажмите кнопку **New Connection** (Новое подключение).

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Снимок экрана диспетчера соединений 3T Studio":::
3. В окне **новое подключение** на вкладке **сервер** введите узел (FQDN) учетной записи Azure Cosmos и порта.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Снимок экрана: вкладка "сервер диспетчера подключений Studio 3T"":::
4. В окне **New Connection** (Новое подключение) на вкладке **Authentication** (Аутентификация) выберите режим аутентификации **Basic (MONGODB-CR or SCARM-SHA-1)** (Базовая (MONGODB CR или SCARM-SHA-1)), а также введите имя пользователя и пароль.  Подтвердите базу данных по умолчанию для проверки подлинности (admin) или укажите другое значение.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Снимок экрана с вкладкой проверки подлинности диспетчера подключений Studio 3T":::
5. В окне **New Connection** (Новое подключение) на вкладке **SSL** установите флажок **Use SSL protocol to connect** (Использовать для подключения протокол SSL) и переключатель **Accept server self-signed SSL certificates** (Принимать самозаверяющие SSL-сертификаты сервера).

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Снимок экрана с вкладкой SSL 3T Connection Manager":::
6. Нажмите кнопку **Test Connection** (Проверить подключение), чтобы проверить сведения о подключении. Затем нажмите кнопку **ОК**, чтобы вернуться в окно "New Connection" (Новое подключение), а затем нажмите кнопку **Save** (Сохранить).

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Снимок экрана: окно "Проверка подключения" 3T Studio":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Использование Studio 3T для создания базы данных, коллекции и документов
Чтобы создать базу данных, коллекцию и документы с помощью Studio 3T, выполните следующие действия.

1. В **диспетчере подключений** выделите нужное подключение и щелкните **Connect** (Подключиться).

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Снимок экрана диспетчера соединений 3T Studio":::
2. Щелкните узел правой кнопкой мыши и выберите **Add Database** (Добавить базу данных).  Укажите имя базы данных и нажмите кнопку **ОК**.

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Снимок экрана с параметром "добавить базу данных" в Studio 3T":::
3. Щелкните правой кнопкой мыши базу данных и выберите **Add Collection** (Добавить коллекцию).  Укажите имя коллекции и нажмите кнопку **Создать**.

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Снимок экрана с параметром "добавить коллекцию" в Studio 3T":::
4. Щелкните пункт меню **Collection** (Коллекция), затем щелкните **Add Document** (Добавить документ).

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Снимок экрана: пункт меню "добавить документ" в 3Tе Studio":::
5. В диалоговом окне "Добавление документа" вставьте следующий текст и щелкните **Добавить документ**.

    ```json
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
    ```
    
6. Добавьте еще один документ со следующим содержимым:

    ```json
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
    ```

7. Выполните пробный запрос. Например, попробуйте найти семьи с фамилией Andersen и вернуть для них поля parents и state.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Снимок экрана результатов запроса Mongo Chef":::

## <a name="next-steps"></a>Дальнейшие шаги

- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.
