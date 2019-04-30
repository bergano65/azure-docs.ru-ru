---
title: Автоматизация развертывания ресурсов приложения-функции для службы "Функции Azure" | Документация Майкрософт
description: Узнайте, как создать шаблон Azure Resource Manager, позволяющий развертывать приложения-функции.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: функции Azure, функции, независимая от сервера архитектура, инфраструктура как код, Azure Resource Manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 5d028768c062ef7df74d48f83ccc4e27a506f1ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737063"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Автоматизация развертывания ресурсов приложения-функции для службы "Функции Azure"

Для развертывания приложения-функции можно использовать шаблон Azure Resource Manager. В этой статье рассматриваются необходимые для этого ресурсы и параметры. В зависимости от [триггеров и привязок](functions-triggers-bindings.md) в приложении-функции может потребоваться развернуть дополнительные ресурсы.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Примеры шаблонов см. в следующих статьях:
- [Function app on Consumption plan] (Приложение-функция в плане потребления);
- [Function app on Azure App Service plan] (Приложение-функция в плане службы приложений Azure).

> [!NOTE]
> План "премиум" для размещения функций Azure доступна в предварительной версии. Дополнительные сведения см. в разделе [план "премиум" функции Azure](functions-premium-plan.md).

## <a name="required-resources"></a>Необходимые ресурсы

При развертывании функции Azure обычно состоит из этих ресурсов:

| Resource                                                                           | Требование | Синтаксис и свойства ссылки                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Приложение-функция.                                                                     | Обязательно для заполнения    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Учетная запись хранения Azure.](../storage/index.yml)                                   | Обязательно для заполнения    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) компонента | Необязательно    | [Microsoft.Insights/components.](/azure/templates/microsoft.insights/components)         |   |
| Объект [план размещения](./functions-scale.md)                                             | Необязательный<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>план размещения является только требуется, если вы выбрали для запуска приложения-функции [план "премиум"](./functions-premium-plan.md) (в предварительной версии) или на [план службы приложений](../app-service/overview-hosting-plans.md).

> [!TIP]
> Не обязательно, настоятельно рекомендуется настроить Application Insights для своего приложения.

<a name="storage"></a>
### <a name="storage-account"></a>Учетная запись хранения

Учетная запись хранения Azure — обязательный ресурс для приложения-функции. Необходима учетная запись общего назначения, поддерживающая большие двоичные объекты, таблицы, очереди и файлы. Дополнительные сведения см. в разделе [Требования к учетной записи хранения](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Кроме того, необходимо указать свойство `AzureWebJobsStorage` как параметр приложения в конфигурации сайта. Если приложение-функция не использует Application Insights для мониторинга, в нем также следует также указать `AzureWebJobsDashboard` как параметр приложения.

Чтобы создать внутренние очереди, среда выполнения службы "Функции Azure" использует строку подключения `AzureWebJobsStorage`.  Если служба Application Insights не включена, среда выполнения использует строку подключения `AzureWebJobsDashboard` для входа в хранилище таблиц Azure и отображения данных на вкладке **мониторинга** на портале.

Эти свойства задаются в коллекции `appSettings` в объекте `siteConfig`:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Рекомендуется использовать Application Insights для мониторинга приложения-функции. Ресурс Application Insights определен с типом **Microsoft.Insights/components** и вид **web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

Кроме того, его необходимо предоставить в приложение функцию с помощью `APPINSIGHTS_INSTRUMENTATIONKEY` параметр приложения. Это свойство указано в `appSettings` коллекции в `siteConfig` объекта:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>План размещения

Определение плана размещения зависит и может принимать одно из следующих:
* [План потребления](#consumption) (по умолчанию)
* [План "премиум"](#premium) (в предварительной версии)
* [План обслуживания приложения](#app-service-plan)

### <a name="function-app"></a>Приложение функций

Ресурс приложения-функции определяется с помощью ресурса типа **Microsoft.Web/sites** и вид **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> Если можно точно определить план размещения, потребуется еще один элемент в массиве dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Приложения-функции необходимо включить эти параметры приложения:

| Имя параметра                 | Описание                                                                               | Примеры значений                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Строка подключения для хранилища учетной записи, среда выполнения функций для внутренних очередей | См. в разделе [учетной записи хранения](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Версия среды выполнения функций Azure                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Стек языков для функции в этом приложении                                   | `dotnet`, `node`, `java`, или `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Требуется, только если с помощью `node` стек языков, который указывает используемую версию              | `10.14.1`                             |

Эти свойства задаются в `appSettings` коллекции в `siteConfig` свойство:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Развертывание на план потребления

План потребления автоматически выделяет вычислительные ресурсы в процессе выполнения кода, масштабируя их в соответствии с нагрузкой и уменьшая, когда код не выполняется. Не нужно платить за бездействующие виртуальные машины и заранее резервировать ресурсы не нужно. Дополнительные сведения см. в разделе [масштабирование и размещение функций Azure](functions-scale.md#consumption-plan).

Образец шаблона диспетчера ресурсов Azure см. на странице [Function app on Consumption plan] (План потребления приложения-функции)

### <a name="create-a-consumption-plan"></a>Создание плана потребления

План потребления не должны быть определены. Один будет автоматически создана или выбрать для каждого региона, при создании самого ресурса приложения-функции.

План потребления — это специальный тип ресурса «ферма серверов». Для Windows, это можно сделать с помощью `Dynamic` значение `computeMode` и `sku` свойства:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

> [!NOTE]
> Нельзя явно определить план потребления для Linux. Он будет создан автоматически.

Если вы явным образом определять плана потребления, вам потребуется задать `serverFarmId` свойства в приложении, чтобы он указывал на идентификатор ресурса плана. Следует убедиться, что у них `dependsOn` параметр для плана также.

### <a name="create-a-function-app"></a>Создание приложения-функции

#### <a name="windows"></a> Windows

В Windows, плана потребления следует выполнить две дополнительные настройки в конфигурации сайта: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` и `WEBSITE_CONTENTSHARE`. Эти свойства настраивают учетную запись хранения и путь к файлам кода приложения-функции и конфигурации.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

В Linux, приложения-функции необходимо быть его `kind` присвоено `functionapp,linux`, и он должен иметь `reserved` свойству присвоено `true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Развертывание на план "премиум"

План "премиум" обеспечивает такой же принцип масштабирования плана потребления, но включает выделенные ресурсы и дополнительные возможности. Дополнительные сведения см. в разделе [функции уровня "премиум" план Azure (Предварительная версия)](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Создать план "премиум"

План "премиум" — это специальный тип ресурса «ферма серверов». Это можно сделать с помощью `EP1`, `EP2`, или `EP3` для `sku` значение свойства.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Создание приложения-функции

Приложения-функции на план "премиум" должен быть `serverFarmId` свойство присвоено идентификатор ресурса план, созданный ранее. Кроме того, план "премиум" требуется две дополнительные настройки в конфигурации сайта: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` и `WEBSITE_CONTENTSHARE`. Эти свойства настраивают учетную запись хранения и путь к файлам кода приложения-функции и конфигурации.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>Развертывание на план службы приложений

В плане службы приложений ваши приложения-функции запускаются на выделенных виртуальных машинах на Basic, Standard и Premium SKU аналогично веб-приложениям. Дополнительную информацию о том, как действует план службы приложений, см. в статье [Подробный обзор планов службы приложений Azure](../app-service/overview-hosting-plans.md).

Образец шаблона Azure Resource Manager см. на странице [Function app on Azure App Service plan] (Приложение-функция в плане службы приложений Azure).

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

План службы приложений определяется ресурса «ферма серверов».

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

Чтобы запустить приложение на платформе Linux, необходимо также задать `kind` для `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Создание приложения-функции 

Приложения-функции в плане службы приложений должен иметь `serverFarmId` свойство присвоено идентификатор ресурса план, созданный ранее.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Приложения Linux также должны содержать `linuxFxVersion` свойства в разделе `siteConfig`. Если развертывается код, значение для этого определяется вашей среды стека:

| Стек            | Пример значения                                         |
|------------------|-------------------------------------------------------|
| Python (предварительная версия) | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Если вы являетесь [развертывание настраиваемого образа контейнера](./functions-create-function-linux-custom-image.md), необходимо указать его с `linuxFxVersion` и включает в себя конфигурацию, которая позволяет получить, как в образе [веб-приложения для контейнеров](/azure/app-service/containers). Кроме того, задайте `WEBSITES_ENABLE_APP_SERVICE_STORAGE` для `false`, поскольку содержимое приложения предоставляется в сам контейнер:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Настройка развертывания

Приложение-функция содержит много дочерних ресурсов, которые можно использовать при развертывании, в том числе параметры приложения и параметры системы управления версиями. Вы можете также удалить дочерний ресурс **sourcecontrols** и выбрать другой [вариант развертывания](functions-continuous-deployment.md).

> [!IMPORTANT]
> Чтобы с помощью Azure Resource Manager успешно развернуть приложение, важно понимать, каким образом ресурсы развертываются в Azure. В следующем примере конфигурации верхнего уровня применяются с помощью **siteConfig**. Их важно задать на верхнем уровне, так как эти конфигурации передают сведения в среду выполнения функций и механизм развертывания. Сведения верхнего уровня требуются перед применением дочернего ресурса **sourcecontrols/web**. Хотя эти параметры можно настроить в дочернем ресурсе **config/appSettings**, в некоторых сценариях приложение-функцию требуется развернуть *до* применения **config/appSettings**. В таких случаях, например в [Logic Apps](../logic-apps/index.yml), функции зависят от другого ресурса.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> В этом шаблоне используется значение параметров приложения [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), задающее базовый каталог, в котором подсистема развертывания функций (Kudu) ищет развертываемый код. В нашем репозитории функции находятся во вложенной папке папки **src**. Таким образом, в предыдущем примере мы задаем для параметров приложения значение `src`. Если ваши функции находятся в корневой папке репозитория, или если развертывание выполняется не из системы управления версиями, то это значение параметров приложения можно удалить.

## <a name="deploy-your-template"></a>Развертывание шаблона

Для развертывания шаблона можно использовать любой из следующих способов:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Интерфейс командной строки Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [портал Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Кнопка "Развертывание в Azure"

Замените ```<url-encoded-path-to-azuredeploy-json>``` версией необработанного пути к файлу `azuredeploy.json` на сайте GitHub, указав его в формате [URL-адреса](https://www.bing.com/search?q=url+encode).

Ниже приведен пример использования разметки:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Ниже приведен пример использования HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке и настройке Функций Azure:

* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Управление приложением-функцией на портале Azure](functions-how-to-use-azure-function-app-settings.md)
* [Создание первой функции Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Function app on Consumption plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json (Приложение-функция в плане потребления)
[Function app on Azure App Service plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json (Приложение-функция в плане службы приложений Azure)
