---
title: включение файла
description: включение файла
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: d60d7727e0674298fa6da7e7330221318da23efd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161620"
---
* Если вы используете Базу данных SQL Azure, следуйте инструкциям в разделе [Подключение к базе данных](#connect-azure-sql-db).

* Если вы используете SQL Server, следуйте инструкциям в разделе [Подключение к SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Подключение к Базе данных SQL Azure

Когда триггер или действие SQL запрашивает сведения о подключении, выполните следующие действия, которые работают как для триггеров, так и для действий.

1. В качестве **имени подключения**создайте имя для подключения.

1. В разделе **SQL Server имя**выберите свой сервер SQL Azure. Когда отобразится список **имя базы данных SQL** , выберите свою базу данных. Укажите имя пользователя и пароль для сервера Azure SQL Server.

   Эти сведения можно также найти либо в портал Azure в свойствах базы данных SQL, либо в строке подключения:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Создание подключения к базе данных SQL Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Когда все будет готово, выберите **Создать**.

1. После создания подключения продолжите [Добавление триггера SQL](#add-sql-trigger) или [добавьте действие SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Подключение к SQL Server

Когда триггер или действие SQL запрашивает сведения о подключении, выполните следующие действия, которые работают как для триггеров, так и для действий. Однако прежде чем начать, убедитесь, что вы уже [настроили локальный шлюз данных](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). В противном случае шлюз не будет отображаться в списке шлюзов при создании подключения.

1. В качестве **имени подключения**создайте имя для подключения.

1. В триггере или действии выберите **Подключаться с помощью локального шлюза данных**, чтобы отобразить параметры SQL Server.

1. В поле имя **SQL Server** и **имя базы данных SQL**укажите адрес SQL Server и имя базы данных. Для **имени пользователя и** **пароля**укажите имя пользователя и пароль для сервера.

   Эти сведения также можно найти в строке подключения:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Создание подключения к SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Если в SQL Server используется проверка подлинности Windows или обычная, выберите **тип проверки подлинности**.

1. В разделе **шлюзы**выберите подписку Azure, связанную с ранее созданным локальным шлюзом данных, и выберите имя для локального шлюза данных.

   Если этот шлюз не отображается в списке, проверьте, правильно ли вы [настроили шлюз](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Создание подключения SQL Server завершено](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Когда все будет готово, выберите **Создать**.

1. После создания подключения перейдите к [добавлению триггера SQL](#add-sql-trigger) или [добавлению действия SQL](#add-sql-action).
