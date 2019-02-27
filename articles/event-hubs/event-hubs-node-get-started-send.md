---
title: Отправка событий в Центры событий Azure с помощью Node.js | Документация Майкрософт
description: В статье описано, как создать приложение Node.js, которое отправляет сообщения в Центры событий Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447724"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Отправка событий в Центры событий Azure с помощью Node.js

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом руководстве представлены инструкции по отправке событий в концентратор событий из приложения, написанного на Node.js.

> [!NOTE]
> Вы можете скачать это краткое руководство в качестве примера с сайта [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), заменить строки `EventHubConnectionString` и `EventHubName` значениями для своего концентратора событий и выполнить этот пример. Или следуйте инструкциям из этого руководства, чтобы создать собственное решение.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

- Node.js версии 8.x или более поздней. Скачайте последнюю версию LTS с сайта [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (рекомендуется) или любой другой интерфейс IDE.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Создание пространства имен Центров событий и концентратора событий
Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа Центров событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните процедуру, описанную в [этой статье](event-hubs-create.md), а затем перейдите к следующим шагам в этом руководстве.

Получите строку подключения для пространства имен концентратора событий, следуя [этим инструкциям](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Строка подключения понадобится нам позже.

## <a name="clone-the-sample-git-repository"></a>Клонирование примера репозитория Git
Клонируйте пример репозитория Git из [GitHub](https://github.com/Azure/azure-event-hubs-node) на компьютере. 

## <a name="install-nodejs-package"></a>Установка пакета Node.js
Установите пакет Node.js для Центров событий Azure на компьютере. 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Клонирование репозитория Git
Скачайте или клонируйте [пример](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) с GitHub. 

## <a name="send-events"></a>Отправка событий
Клонированный пакет SDK содержит несколько примеров, которые показывают, как отправлять события в концентратор событий с помощью Node.js. В этом кратком руководстве используется пример **simpleSender.js**. Чтобы увидеть получение событий, откройте другой терминал и получите события с помощью [примера получения](event-hubs-node-get-started-receive.md).

1. Откройте проект в Visual Studio Code. 
2. Создайте файл с именем **.env** в папке **client**. Скопируйте и вставьте в него пример переменных среды из **sample.env** в корневой папке.
3. Настройте строку подключения, имя концентратора событий и конечную точку хранилища. Инструкции по получению строки подключения для концентратора событий см. в разделе [Создание пространства имен в Центрах событий](event-hubs-create.md#create-an-event-hubs-namespace).
4. В интерфейсе командной строки Azure перейдите по пути к папке **client**. Установите пакеты узла и создайте проект, выполнив следующие команды:

    ```shell
    npm i
    npm run build
    ```
5. Запустите отправку событий, выполнив следующую команду: 

    ```shell
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Просмотр примера кода 
Просмотрите пример кода в файле simpleSender.js, чтобы отправить события в концентратор событий.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Прежде чем выполнять этот скрипт, не забудьте задать переменные среды. Вы можете настроить их в командной строке, как показано в следующем примере, или использовать [пакет dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>Дополнительная информация
В рамках работы с этим кратким руководством вы отправляли сообщения в концентратор событий с помощью Node.js. Чтобы узнать, как получать события из концентратора событий с помощью Node.js, см. статью [Получение событий от Центров событий Azure с помощью Node.js](event-hubs-node-get-started-receive.md).

Просмотрите другие примеры Node.js для Центров событий на сайте [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
