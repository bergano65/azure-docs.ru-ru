---
title: Общий доступ к ресурсам независимо от источника (CORS) в Azure Cosmos DB
description: В этой статье описано, как настроить общий доступ к ресурсам независимо от источника (CORS) в Azure Cosmos DB с помощью портала Azure и шаблонов Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 1269c4c2405e9b906b63c8a29c0de1ac217da1d7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241891"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Настройка общего доступа к ресурсам независимо от источника (CORS) 

Общий доступ к ресурсам независимо от источника (CORS) — функция HTTP, которая позволяет веб-приложению, работающему в одном домене, обращаться к ресурсам другого домена. Веб-браузеры реализуют ограничение безопасности, известное как политика одного источника, которая не позволяет веб-странице вызывать API-интерфейсы в другом домене. Тем не менее CORS обеспечивает безопасный способ разрешить исходному домену вызывать API-интерфейсы в другом домене. Core API SQL в Azure Cosmos DB теперь поддерживает общий доступ к ресурсам независимо от источника (CORS) с помощью заголовка allowedOrigins. При включении поддержки CORS для учетной записи Azure Cosmos только прошедшие проверку подлинности запросы оцениваются, чтобы определить, разрешены ли они в соответствии с правилами, которые вы указали.

Вы можете настроить общий доступ независимо от источника (CORS) на портале Azure или на основе шаблона Azure Resource Manager. Core API SQL в Azure Cosmos DB поддерживает библиотеку JavaScript, которая работает в Node.js и средах на основе браузера. Теперь для этой библиотеки доступны преимущества поддержки CORS при использовании режима шлюза. Для использования этой функции не нужно ничего настраивать на стороне клиента. С поддержкой CORS ресурсы из браузера могут напрямую обращаться к Azure Cosmos DB через [библиотеку JavaScript](https://www.npmjs.com/package/@azure/cosmos) или непосредственно из [REST API](https://docs.microsoft.com/rest/api/cosmos-db/) для простых операций. 

## <a name="enable-cors-support-from-azure-portal"></a>Включение поддержки CORS на портале Azure

Чтобы включить общий доступ к ресурсам независимо от источника с помощью портала Azure, следуйте инструкциям ниже:

1. Перейдите к своей учетной записи Azure Cosmos DB. Откройте колонку **CORS**.

2. Укажите через запятую список источников, которые могут совершать вызовы независимо от источника в учетную запись Cosmos DB. Например `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Вы также можете использовать подстановочный знак "\*", чтобы разрешить использование всех источников, и выбрать **Отправить**. 

   > [!NOTE]
   > В настоящее время в имени домена нельзя использовать подстановочные знаки. Например, формат `https://*.mydomain.net` еще не поддерживается. 
   
   ![Включение общего доступа к ресурсам независимо от источника с помощью портала Azure](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Включение поддержки CORS из шаблона Resource Manager

Чтобы включить CORS с помощью шаблона Resource Manager, добавьте раздел cors со свойством allowedOrigins в любой имеющийся шаблон. Приведенный ниже код JSON является примером шаблона, который создает учетную запись Azure Cosmos с включенной поддержкой CORS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Использование библиотеки Azure Cosmos DB JavaScript в браузере

Сейчас в библиотеке Azure Cosmos DB JavaScript доступна только библиотека версии CommonJS, которая поставляется в составе пакета. Чтобы использовать эту библиотеку из браузера, необходимо использовать такое средство, как Rollup или Webpack для создания Библиотеки, совместимой с браузером. Для некоторых библиотек Node.js нужны макеты браузера. Ниже приведен пример файла конфигурации webpack, который содержит необходимые настройки макетов.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Вот [пример кода](https://github.com/christopheranderson/cosmos-browser-sample), в котором используется TypeScript и Webpack с библиотекой пакета SDK Azure Cosmos DB для JavaScript для создания приложения Todo, которое отправляет обновления в режиме реального времени при создании элементов.
Рекомендуется не использовать первичный ключ для взаимодействия с Azure Cosmos DB из браузера. Вместо этого используйте маркеры ресурсов для обмена данными. Дополнительные сведения о маркерах ресурсов см. в разделе [Защита доступа к Azure Cosmos DB](secure-access-to-data.md#resource-tokens).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о других способах защиты вашей учетной записи Azure Cosmos см. в следующих статьях:

* [Настройка брандмауэра IP-адресов для учетной записи Azure Cosmos DB](how-to-configure-firewall.md)

* [Настройка доступа на основе подсети и виртуальной сети для учетной записи Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
    

