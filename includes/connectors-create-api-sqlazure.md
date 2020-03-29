---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789208"
---
* Если вы используете Базу данных SQL Azure, следуйте инструкциям в разделе [Подключение к базе данных](#connect-azure-sql-db).

* Если вы используете SQL Server, следуйте инструкциям в разделе [Подключение к SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Подключение к Базе данных SQL Azure

Когда спусковой крючок или действие s'L подсказывает вам информацию о подключении, выполните следующие действия, которые работают как для триггеров, так и для действий.

1. Для **имени соединения**создайте имя для подключения.

1. Под **именем сервера S'L**выберите сервер Azure S'L. При появляются имена **данных базы данных,** выберите базу данных. Предоставьте имя пользователя и пароль для вашего сервера Azure S'L.

   Вы также можете найти эту информацию либо на портале Azure в соответствии с свойствами базы данных S'L, либо в строке подключения:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Создание подключения к базе данных Azure S'L](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Когда все будет готово, выберите **Создать**.

1. После создания соединения продолжайте [добавлять триггер s'L](#add-sql-trigger) или [добавлять действие S'L.](#add-sql-action)

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Подключение к SQL Server

Когда спусковой крючок или действие s'L подсказывает вам информацию о подключении, выполните следующие действия, которые работают как для триггеров, так и для действий. Для сценариев, требующих установки [локального шлюза данных](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) на локальном компьютере и [создания ресурса шлюза данных Azure,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)убедитесь, что вы выполните эти требования в первую очередь. В противном случае ресурс шлюза не будет отображаться в списке шлюзов при создании соединения.

Кроме того, для использования аутентификации Windows с разъемом сервера S'L в [среде интеграционных служб (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)используйте версию разъема, не являющиеся ISE, и шлюз данных. Версия с маркировкой ISE не поддерживает аутентификацию Windows.

1. Для **имени соединения**создайте имя для подключения.

1. В триггере или действии выберите **Подключаться с помощью локального шлюза данных**, чтобы отобразить параметры SQL Server.

1. Для названия сервера и **имени базы данных S'L**укажите адрес для вашего сервера s-L и имя для вашей базы данных. **SQL server Name** Для **имени пользователя** и **пароля,** укажите имя пользователя и пароль для вашего сервера.

   Вы также можете найти эту информацию в строке соединения:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Создание подключения к серверу S'L](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Если ваш сервер S'L использует Windows или Базовую аутентификацию, выберите **тип аутентификации.**

1. Под **Gateways**выберите подписку Azure, связанную с ранее созданным шлюзом данных, и выберите имя для шлюза данных.

   Если этот шлюз не отображается в списке, проверьте, правильно ли вы [настроили шлюз](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Завершено создание завершенного подключения сервера S'L](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Когда все будет готово, выберите **Создать**.

1. После создания подключения перейдите к [добавлению триггера SQL](#add-sql-trigger) или [добавлению действия SQL](#add-sql-action).
