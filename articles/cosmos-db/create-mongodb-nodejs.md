---
title: Подключение приложения MongoDB на Node.js к Azure Cosmos DB
description: В этом кратком руководстве показано, как подключить существующее приложение MongoDB, написанное на Node.js, к Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/06/2018
ms.openlocfilehash: 99f6de4d0441594c09e5e8c29834b90aeb1d43a6
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586484"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Краткое руководство. Перенос имеющегося веб-приложения MongoDB на Node.js в Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ-значение" и графовые базы данных, используя преимущества глобального распределения и горизонтального масштабирования Cosmos DB. 

В этом кратком руководстве описывается, как использовать имеющееся приложение MongoDB, написанное на Node.js, а также как подключить его к базе данных Cosmos, поддерживающей клиента MongoDB. Другими словами, приложению понятно, что данные хранятся в базе данных Cosmos.

В итоге вы получите приложение MEAN (MongoDB, Express, AngularJS и Node.js), выполняющееся в [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

![Приложение MEAN.js, которое запущено в службе приложений Azure](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Предварительные требования 
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Кроме Azure CLI, вам нужно локально установить [Node.js](https://nodejs.org/) и [Git](https://www.git-scm.com/downloads) для выполнения команд `npm` и `git`.

У вас должен быть опыт работы с Node.js. В целом разработка приложений Node.js в этом кратком руководстве не рассматривается.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Затем выполните следующие команды, чтобы клонировать репозиторий с примером. Этот репозиторий с примером содержит приложение по умолчанию [MEAN.js](https://meanjs.org/).

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
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Выполнение приложения

Установите необходимые пакеты и запустите приложение.

```bash
cd mean
npm install
npm start
```
Приложение попытается подключиться к источнику MongoDB, что ему не удастся. Выйдите из приложения, если в выходных данных появится ошибка [MongoError: connect ECONNREFUSED 127.0.0.1:27017].

## <a name="log-in-to-azure"></a>Вход в Azure

Если вы используете установленную версию Azure CLI, войдите в подписку Azure с помощью команды [az login](/cli/azure/reference-index#az-login) и следуйте инструкциям на экране. Этот шаг можно пропустить, если вы используете Azure Cloud Shell.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Добавление модуля Azure Cosmos DB

Если вы используете установленную версию Azure CLI, проверьте наличие компонента `cosmosdb`, выполнив команду `az`. Если компонент `cosmosdb` включен в список базовых команд, перейдите к следующей команде. Этот шаг можно пропустить, если вы используете Azure Cloud Shell.

Если компонента `cosmosdb` нет в списке базовых команд, переустановите [Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими. 

В следующем примере показано создание группы ресурсов в регионе "Западная Европа". Выберите уникальное имя для группы ресурсов.

Если вы используете Azure Cloud Shell, щелкните **Попробовать**, войдите в систему, следуя указаниям на экране, а затем скопируйте команду в командную строку.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Создайте учетную запись Cosmos с помощью команды [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

В следующей команде замените `<cosmosdb-name>` уникальным именем своей базы данных Cosmos везде, где встречается этот заполнитель. Это уникальное имя будет использоваться как часть конечной точки Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`), поэтому оно должно быть уникальным для всех учетных записей Cosmos в Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Параметр `--kind MongoDB` разрешает клиентские подключения MongoDB.

После создания учетной записи Azure Cosmos DB в Azure CLI отображаются сведения, схожие с теми, которые приведены ниже. 

> [!NOTE]
> В этом примере JSON по умолчанию используется как формат выходных данных Azure CLI. Чтобы использовать другой формат выходных данных, ознакомьтесь со статьей [Форматы выходных данных для команд Azure CLI](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Подключение приложения Node.js к базе данных

На этом шаге вы подключите пример приложения MEAN.js к только что созданной базе данных Cosmos. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Настройка строки подключения в приложении Node.js

В репозитории MEAN.js откройте `config/env/local-development.js`.

Замените содержимое этого файла приведенным ниже кодом. Замените также два заполнителя `<cosmosdb-name>` на имя учетной записи Cosmos.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Получение ключа

Чтобы подключиться к базе данных Cosmos, вам понадобится ключ базы данных. Чтобы получить первичный ключ, выполните команду [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys).

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

В Azure CLI отображаются сведения, подобные следующим: 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Скопируйте значение `primaryMasterKey`. Вставить это над `<primary_master_key>` в `local-development.js`.

Сохраните изменения.

### <a name="run-the-application-again"></a>Повторный запуск приложения

Еще раз запустите `npm start`. 

```bash
npm start
```

Должно появиться сообщение консоли, что среда разработки подготовлена к работе. 

Откройте браузер и перейдите по адресу `http://localhost:3000`. Щелкните **регистрацию** в верхней области меню и создайте двух фиктивных пользователей. 

В примере приложения MEAN.js данные пользователя хранятся в базе данных. Если все получилось и вы вошли в приложение MEAN.js под созданным пользователем, это означает, что подключение Azure Cosmos DB работает. 

![MEAN.js успешно подключается к базе данных MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Просмотр данных в обозревателе данных

Данные, хранимые в базе данных Cosmos, доступны для просмотра и запроса на портале Azure.

Чтобы просмотреть данные пользователя, созданные на предыдущем шаге, запросить их и начать с ними работать, войдите на [портал Azure](https://portal.azure.com) с помощью своего браузера.

В поле поиска в верхней области введите Azure Cosmos DB. Когда откроется колонка учетной записи Cosmos, выберите свою учетную запись Cosmos. В левой области навигации щелкните обозреватель данных. Разверните свою коллекцию на панели коллекций. Вы сможете увидеть документы в коллекции, запросить данные и даже создать и запустить хранимые процедуры, триггеры и определенные пользователем функции. 

![Обозреватель данных на портале Azure](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Развертывание приложения Node.js в Azure

На этом шаге вы развернете приложение Node.js в Cosmos DB.

Заметьте, что файл конфигурации, измененный ранее, понадобится для среды разработки (`/config/env/local-development.js`). Когда вы развернете приложение в службу приложений, оно запустится в рабочей среде по умолчанию. Таким образом, вам необходимо внести те же изменения в соответствующий файл конфигурации.

В репозитории MEAN.js откройте `config/env/production.js`.

В объекте `db` замените значение `uri`, как показано в следующем примере. Обязательно замените заполнители на фактические значения, как и в прошлый раз.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Параметр `ssl=true` важен, так как для [Cosmos DB требуется протокол SSL](connect-mongodb-account.md#connection-string-requirements). 
>
>

В окне терминала зафиксируйте все изменения в Git. Вы можете скопировать обе команды, чтобы выполнить их вместе.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как создать учетную запись и коллекцию Cosmos и запустить консольное приложение. Теперь вы можете импортировать дополнительные данные в базу данных Cosmos. 

> [!div class="nextstepaction"]
> [Перенос данных MongoDB в Azure Cosmos DB](mongodb-migrate.md)
