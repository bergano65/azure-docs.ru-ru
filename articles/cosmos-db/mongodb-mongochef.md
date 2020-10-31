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
ms.openlocfilehash: a02aaadf8c774557eb182acf041b6f19337a0de8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096523"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Подключение к учетной записи Azure Cosmos с помощью 3T Studio
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Чтобы подключиться к API Azure Cosmos DB для MongoDB с помощью 3T Studio, необходимо выполнить следующие действия.

* Скачайте и установите [Studio 3T](https://studio3t.com/).
* Сведения о [строке подключения](connect-mongodb-account.md) учетной записи Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Создание подключения в Studio 3T

Чтобы добавить учетную запись Azure Cosmos в Диспетчер соединений Studio 3T, выполните следующие действия.

1. Получите сведения о подключении для учетной записи API Azure Cosmos DB для MongoDB, следуя инструкциям в статье [Подключение приложения MongoDB к Azure Cosmos DB](connect-mongodb-account.md) .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Снимок экрана со страницей строки подключения":::

2. Щелкните **Connect** (Подключиться), чтобы открыть диспетчер подключений, и нажмите кнопку **New Connection** (Новое подключение).

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Снимок экрана со страницей строки подключения":::
3. В окне **новое подключение** на вкладке **сервер** введите узел (FQDN) учетной записи Azure Cosmos и порта.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Снимок экрана со страницей строки подключения":::
4. В окне **New Connection** (Новое подключение) на вкладке **Authentication** (Аутентификация) выберите режим аутентификации **Basic (MONGODB-CR or SCARM-SHA-1)** (Базовая (MONGODB CR или SCARM-SHA-1)), а также введите имя пользователя и пароль.  Подтвердите базу данных по умолчанию для проверки подлинности (admin) или укажите другое значение.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Снимок экрана со страницей строки подключения":::
5. В окне **New Connection** (Новое подключение) на вкладке **SSL** установите флажок **Use SSL protocol to connect** (Использовать для подключения протокол SSL) и переключатель **Accept server self-signed SSL certificates** (Принимать самозаверяющие SSL-сертификаты сервера).

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Снимок экрана со страницей строки подключения" (Новое подключение), а затем нажмите кнопку **Save** (Сохранить).

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Снимок экрана со страницей строки подключения":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Использование Studio 3T для создания базы данных, коллекции и документов
Чтобы создать базу данных, коллекцию и документы с помощью Studio 3T, выполните следующие действия.

1. В **диспетчере подключений** выделите нужное подключение и щелкните **Connect** (Подключиться).

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Снимок экрана со страницей строки подключения":::
2. Щелкните узел правой кнопкой мыши и выберите **Add Database** (Добавить базу данных).  Укажите имя базы данных и нажмите кнопку **ОК** .

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Снимок экрана со страницей строки подключения":::
3. Щелкните правой кнопкой мыши базу данных и выберите **Add Collection** (Добавить коллекцию).  Укажите имя коллекции и нажмите кнопку **Создать** .

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Снимок экрана со страницей строки подключения":::
4. Щелкните пункт меню **Collection** (Коллекция), затем щелкните **Add Document** (Добавить документ).

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Снимок экрана со страницей строки подключения" },
        "isRegistered": false
    }
    ```

7. Выполните пробный запрос. Например, попробуйте найти семьи с фамилией Andersen и вернуть для них поля parents и state.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Снимок экрана со страницей строки подключения":::

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.
