---
title: Подключение к API служб мультимедиа Azure v3 — Node.js
description: В этой статье показано, как подключиться к API служб мультимедиа v3 с помощью Node.js.
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
ms.openlocfilehash: 1ac82687cb28344f682c58a132f04d71184d5d74
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001202"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Подключение к API служб мультимедиа v3 — Node.js

В этой статье показано, как подключиться к пакету SDK для служб мультимедиа Azure версии 3 node.js с помощью метода входа субъекта-службы.

## <a name="prerequisites"></a>Обязательные условия

- Установите [Node.js](https://nodejs.org/en/download/).
- [Создание учетной записи Служб мультимедиа](./create-account-howto.md). Обязательно запомните имя группы ресурсов и имя учетной записи служб мультимедиа.

> [!IMPORTANT]
> Проверьте [соглашения об именовании](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Создание package.js

1. Создайте package.jsв файле с помощью любимого редактора.
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

|Пакет|Описание|
|---|---|
|`azure-arm-mediaservices`|Пакет SDK служб мультимедиа Azure. <br/>Чтобы убедиться, что используется последний пакет служб мультимедиа Azure, установите флажок [NPM Install Azure-ARM-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Пакет SDK для службы хранилища. Используется при отправке файлов в активы.|
|`ms-rest-azure`| Используется для входа в систему.|

Вы можете выполнить следующую команду, чтобы убедиться, что используется последний пакет:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Подключение к Node.js клиенту

1. Создайте JS-файл с помощью любимого редактора.
1. Откройте файл и вставьте в него следующий код.
1. Задайте значения в разделе "Конфигурация конечной точки" для значений, полученных из [API доступа](./access-api-howto.md).

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

## <a name="run-your-app"></a>Запустите приложение.

Откройте командную строку. Перейдите к каталогу примера и выполните следующие команды:

```
npm install 
node index.js
```

## <a name="see-also"></a>См. также статью

- [Основные понятия служб мультимедиа Azure](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Дальнейшие действия

Изучите справочную документацию [Node.js](/javascript/api/overview/azure/mediaservices/management) Служб мультимедиа и ознакомьтесь с [примерами](https://github.com/Azure-Samples/media-services-v3-node-tutorials), показывающими как использовать API Служб мультимедиа с Node.js.
