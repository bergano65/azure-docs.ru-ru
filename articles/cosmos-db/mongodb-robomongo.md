---
title: Использование Robomongo с Azure Cosmos DB
description: 'Узнайте, как использовать учетную запись "Azure Cosmos DB: API для MongoDB"'
keywords: Robomongo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: 78f0158c9a80a60717b81b4788531c7efd979111
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863811"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Использование Robomongo с учетной записью "Azure Cosmos DB: API для MongoDB"
Чтобы подключиться к учетной записи "Azure Cosmos DB: API для MongoDB" с помощью Robomongo, сделайте следующее:

* Скачайте и установите [Robomongo](https://robomongo.org/).
* Получите сведения о [строке подключения](connect-mongodb-account.md) для учетной записи "Azure Cosmos DB: API для MongoDB".

## <a name="connect-using-robomongo"></a>Подключение с использованием Robomongo
Чтобы добавить учетную запись "Azure Cosmos DB: API для MongoDB" к подключениям Robomongo MongoDB, сделайте следующее.

1. Получите сведения о подключении учетной записи "Azure Cosmos DB: API для MongoDB", следуя [этим инструкциям](connect-mongodb-account.md).

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
* См. [примеры](mongodb-samples.md) Azure Cosmos DB: API для MongoDB
