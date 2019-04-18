---
title: Подключение к API v3 служб мультимедиа Azure — Node.js
description: Узнайте, как подключиться к API служб мультимедиа версии 3 с помощью Node.js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 40880a2c28ce28a671930ef8837082247e61e24b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495094"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Подключение к API v3 служб мультимедиа - Node.js

В этой статье показано, как подключиться к SDK, с помощью субъекта-службы входа, метод v3-node.js для служб мультимедиа Azure.

## <a name="prerequisites"></a>Технические условия

- Установите [Node.js](https://nodejs.org/en/download/).
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи служб мультимедиа.

## <a name="create-packagejson"></a>Создайте файл package.json

1. Создайте файл package.json, используя предпочитаемый редактор.
1. Откройте файл и вставьте следующий код:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Необходимо указать следующие пакеты:

|Package|ОПИСАНИЕ|
|---|---|
|`azure-arm-mediaservices`|Пакет SDK служб мультимедиа Azure. <br/>Чтобы убедиться, что вы используете последнюю версию пакета служб мультимедиа Azure, проверьте [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Пакет SDK для хранилища. Используется при отправке файлов в активы.|
|`ms-rest-azure`| Используются для входа.|

Можно выполнить следующую команду, чтобы убедиться, что вы используете последнюю версию пакета:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Подключиться к клиенту Node.js

1. Создайте файл с расширением .js в предпочитаемом редакторе.
1. Откройте файл и вставьте в него следующий код.
1. Задайте значения в разделе «config конечной точки» значения, полученного от [доступа к интерфейсам API](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Запуск приложения для Android

Откройте окно командной строки. Перейдите в каталог образцов и выполните следующие команды:

```
npm install 
node index.js
```

## <a name="see-also"></a>См. также

- [Основные понятия служб мультимедиа Azure](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Дальнейшие действия

Изучите справочную документацию [Node.js](https://aka.ms/ams-v3-nodejs-ref) Служб мультимедиа и ознакомьтесь с [примерами](https://github.com/Azure-Samples/media-services-v3-node-tutorials), показывающими как использовать API Служб мультимедиа с Node.js.

