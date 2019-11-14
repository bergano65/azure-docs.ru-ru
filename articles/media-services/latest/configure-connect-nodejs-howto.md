---
title: Подключение к API служб мультимедиа Azure v3 — Node. js
description: Узнайте, как подключиться к API служб мультимедиа v3 с помощью Node. js.
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
ms.openlocfilehash: 5361a820da0caa336ccc95ae657407889443268c
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048420"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Подключение к службам мультимедиа v3 API-Node. js

В этой статье показано, как подключиться к пакету SDK для Node. js служб мультимедиа Azure с помощью метода входа субъекта-службы.

## <a name="prerequisites"></a>предварительным требованиям

- Установите [Node.js](https://nodejs.org/en/download/).
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи служб мультимедиа.

> [!IMPORTANT]
> Проверьте [соглашения об именовании](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Создание Package. JSON

1. Создайте файл Package. JSON с помощью любимого редактора.
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

Должны быть указаны следующие пакеты:

|Package|ОПИСАНИЕ|
|---|---|
|`azure-arm-mediaservices`|Пакет SDK служб мультимедиа Azure. <br/>Чтобы убедиться, что используется последний пакет служб мультимедиа Azure, установите флажок [NPM Install Azure-ARM-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Пакет SDK для службы хранилища. Используется при отправке файлов в активы.|
|`ms-rest-azure`| Используется для входа в систему.|

Вы можете выполнить следующую команду, чтобы убедиться, что используется последний пакет:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Подключение к клиенту Node. js

1. Создайте JS-файл с помощью любимого редактора.
1. Откройте файл и вставьте в него следующий код.
1. Задайте значения в разделе "Конфигурация конечной точки" для значений, полученных из [API доступа](access-api-cli-how-to.md).

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

Откройте окно командной строки. Перейдите к каталогу примера и выполните следующие команды:

```
npm install 
node index.js
```

## <a name="see-also"></a>См. также

- [Основные понятия служб мультимедиа Azure](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Дополнительная информация

Изучите справочную документацию [Node.js](/javascript/api/overview/azure/mediaservices/management) Служб мультимедиа и ознакомьтесь с [примерами](https://github.com/Azure-Samples/media-services-v3-node-tutorials), показывающими как использовать API Служб мультимедиа с Node.js.

