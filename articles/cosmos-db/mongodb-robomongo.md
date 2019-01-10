---
title: Подключение к Azure Cosmos DB с помощью Robo 3T
description: Сведения о подключении к Azure Cosmos DB с помощью Robo 3T и API Azure Cosmos DB для MongoDB
keywords: Robomongo
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: af601075b8cc774654a9f2dbf83193bf9701166b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789455"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Использование Robo 3T с API Azure Cosmos DB для MongoDB

Чтобы подключиться к учетной записи Cosmos с помощью Robo 3T, сделайте следующее.

* Загрузите и установите [Robo 3T](https://robomongo.org/).
* Получите сведений о [строке подключения](connect-mongodb-account.md) Cosmos DB.

## <a name="connect-using-robo-3t"></a>Подключение с помощью Robo 3T
Чтобы добавить в диспетчер подключений Robo 3T учетную запись Cosmos, сделайте следующее.

1. Получите сведения о подключении для учетной записи Cosmos, настроенной с помощью API Azure Cosmos DB для MongoDB, следуя инструкциям [здесь](connect-mongodb-account.md).

    ![Снимок экрана, колонка строки подключения](./media/mongodb-robomongo/connectionstringblade.png)
2. Запустите *Robomongo.exe*.

3. Нажмите кнопку подключения под меню **File** (Файл) для управления подключениями. Щелкните **Create** (Создать) в окне **MongoDB Connections** (Подключения MongoDB). Откроется окно **Connection Settings** (Параметры подключения).

4. В окне **Connection Settings** (Параметры подключения) выберите имя. Затем найдите **узел** и **порт**, указанные в сведениях о подключении, полученных на шаге 1, и введите их в полях **Address** (Адрес) и **Port** (Порт) соответственно.

    ![Снимок экрана окна управления подключениями Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. На вкладке **Authentication** (Аутентификация) установите флажок **Perform authentication** (Выполнять аутентификацию). Затем введите базу данных (по умолчанию — *Admin*), **имя пользователя** и **пароль**.
**Имя пользователя** и **пароль** можно найти в данных подключения, полученных на шаге 1.

    ![Снимок экрана вкладки аутентификации Robomongo](./media/mongodb-robomongo/authentication.png)
6. На вкладке **SSL** установите флажок **Use SSL protocol** (Использовать протокол SSL), затем измените значение параметра **Authentication Method** (Метод аутентификации) на **Self-signed Certificate** (Самозаверяющий сертификат).

    ![Снимок экрана вкладки SSL Robomongo](./media/mongodb-robomongo/SSL.png)
7. Наконец, нажмите кнопку **Test** (Проверить), чтобы проверить возможность подключения, затем нажмите кнопку **Save** (Сохранить).

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [использовать Studio 3T](mongodb-mongochef.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.
