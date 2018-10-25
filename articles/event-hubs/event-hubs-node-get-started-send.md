---
title: Отправка событий в Центры событий Azure с помощью Node.js | Документация Майкрософт
description: Приступите к отправке событий в Центры событий с помощью Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 413f36a12dee135cc1a7dc99a34d8b7b2be6c46f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801069"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Отправка событий в Центры событий Azure с помощью Node.js

Концентраторы событий Azure — это высокомасштабируемая система управления событиями, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и другими системами. После сбора событий в концентратор событий вы можете получать и обрабатывать события с помощью внутрипроцессных обработчиков или перенаправлять их в другие аналитические системы.

Общие сведения о службе "Центры событий" см. в [этой статье](event-hubs-about.md).

В этом руководстве представлены инструкции по отправке событий в концентратор событий из приложения, написанного на Node.js. Чтобы получать события, используя пакет размещения обработчика событий Node.js, ознакомьтесь с [соответствующей статьей о получении](event-hubs-node-get-started-receive.md).

Код из этого краткого руководства доступен [на сайте GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). 

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

- Node.js версии 8.x или более поздней. Скачайте последнюю версию LTS с сайта [https://nodejs.org](https://nodejs.org).
- Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][], прежде чем начинать работу.
- Visual Studio Code (рекомендуется) или любой другой интерфейс IDE.

## <a name="create-a-namespace-and-event-hub"></a>Создание пространства имен и концентратора событий
Первым шагом является создание пространства имен Центров событий с помощью портала Azure. Если у вас нет таких сущностей, создайте их, следуя инструкциям в статье [Создание пространства имен Центров событий и концентратора событий с помощью портала Azure](event-hubs-create.md).

## <a name="clone-the-sample-git-repository"></a>Клонирование примера репозитория Git
Клонируйте пример репозитория Git из [Github](https://github.com/Azure/azure-event-hubs-node) на компьютере. 

## <a name="install-nodejs-package"></a>Установка пакета Node.js
Установите пакет Node.js для Центров событий Azure на компьютере. 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Клонирование репозитория Git
Скачайте или клонируйте [пример](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) с GitHub. 

## <a name="send-events"></a>Отправка событий
Клонированный пакет SDK содержит несколько примеров, которые показывают, как отправлять события в концентратор событий с помощью Node.js. В этом кратком руководстве используется пример **simpleSender.js**. Чтобы увидеть получение событий, откройте другой терминал и получите события с помощью [примера получения](event-hubs-node-get-started-receive.md).

1. Откройте проект в Visual Studio Code. 
2. Создайте файл с именем **.env** в папке **client**. Скопируйте и вставьте в него пример переменных среды из **sample.env** в корневой папке.
3. Настройте строку подключения, имя концентратора событий и конечную точку хранилища. Вы можете скопировать строку подключения для концентратора событий из поля **Строка подключения — первичный ключ** в разделе **RootManageSharedAccessKey** на странице Центра событий на портале Azure. Подробные сведения см. в [этом разделе](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace).
4. В интерфейсе командной строки Azure перейдите по пути к папке **client**. Установите пакеты узла и создайте проект, выполнив следующие команды:

    ```nodejs
    npm i
    npm run build
    ```
5. Запустите отправку событий, выполнив следующую команду: 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Просмотр примера кода 
Ниже приведен пример кода для отправки событий в концентратор событий с помощью Node.js. Вы можете вручную создать файл sampleSender.js и запустить его, чтобы отправлять события в концентратор событий. 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
}

main().catch((err) => {
    console.log(err);
});

```

Прежде чем выполнять этот скрипт, не забудьте задать переменные среды. Вы можете настроить это в командной строке, как показано в следующем примере, или использовать [пакет dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Центрах событий см. в следующих ресурсах:

* [Получение событий с помощью Node.js](event-hubs-node-get-started-receive.md)
* [Примеры на GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)

<!-- Links -->
[создайте бесплатную учетную запись Azure]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
