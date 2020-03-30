---
title: Автоматизация развертывания ресурса функции для Azure
description: Узнайте, как создать шаблон Azure Resource Manager, позволяющий развертывать приложения-функции.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 48d98d6fef896f9288be88824a62fa1c8179217f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276898"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Автоматизация развертывания ресурсов приложения-функции для службы "Функции Azure"

Для развертывания приложения-функции можно использовать шаблон Azure Resource Manager. В этой статье рассматриваются необходимые для этого ресурсы и параметры. В зависимости от [триггеров и привязок](functions-triggers-bindings.md) в приложении-функции может потребоваться развернуть дополнительные ресурсы.

Для получения дополнительной информации [Authoring Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md)о создании шаблонов см.

Примеры шаблонов см. в следующих статьях:
- [Function app on Consumption plan] (Приложение-функция в плане потребления)
- [Function app on Azure App Service plan] (Приложение-функция в плане службы приложений Azure).

## <a name="required-resources"></a>Необходимые ресурсы

Развертывание функций Azure обычно состоит из следующих ресурсов:

| Ресурс                                                                           | Требование | Ссылка на синтаксис и свойства                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Приложение-функция.                                                                     | Обязательно    | [Microsoft.Web/сайты](/azure/templates/microsoft.web/sites)                             |   |
| Учетная запись [хранения Azure](../storage/index.yml)                                   | Обязательно    | [Учетные записи Microsoft.Storage/Storage](/azure/templates/microsoft.storage/storageaccounts) |   |
| Компонент [исследования приложений](../azure-monitor/app/app-insights-overview.md) | Необязательный    | [Microsoft.Insights/компоненты](/azure/templates/microsoft.insights/components)         |   |
| [План хостинга](./functions-scale.md)                                             | Дополнительно<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup> План хостинга требуется только при выборе приложения функции в [плане Premium](./functions-premium-plan.md) (в предварительном просмотре) или в [плане службы app.](../app-service/overview-hosting-plans.md)

> [!TIP]
> Хотя это и не требуется, настоятельно рекомендуется настроить Application Insights для вашего приложения.

<a name="storage"></a>
### <a name="storage-account"></a>Учетная запись хранения

Учетная запись хранения Azure — обязательный ресурс для приложения-функции. Необходима учетная запись общего назначения, поддерживающая большие двоичные объекты, таблицы, очереди и файлы. Дополнительные сведения см. в разделе [Требования к учетной записи хранения](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
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

Приложение Insights рекомендуется для мониторинга ваших функциональных приложений. Ресурс Application Insights определяется с типом **Microsoft.Insights/компонентами** и видом **веб:**

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
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Кроме того, ключ приборов должен быть предоставлен приложению функции с помощью настройки `APPINSIGHTS_INSTRUMENTATIONKEY` приложения. Это свойство указывается в коллекции `appSettings` в объекте: `siteConfig`

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>План размещения

Определение плана хостинга варьируется и может быть одним из следующих:
* [План потребления](#consumption) (по умолчанию)
* [Премиум-план](#premium) (в предварительном просмотре)
* [План обслуживания приложений](#app-service-plan)

### <a name="function-app"></a>Приложение-функция

Ресурс приложения функции определяется с помощью ресурса типа **Microsoft.Web/sites** и доброго **функционера:**

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
}
```

> [!IMPORTANT]
> Если вы четко определяете план хостинга, в массиве в зависимости от этого потребуется дополнительный элемент:`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Функциональное приложение должно включать в себя следующие настройки приложения:

| Имя параметра                 | Описание                                                                               | Примеры значений                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Строка подключения к учетной записи хранилища, которую использует время выполнения функций для внутренней очереди | Посмотреть [учетную запись хранилища](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Версия времени выполнения функций Azure                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Стек языка, который будет использоваться для функций в этом приложении                                   | `dotnet`, `node`, `java`, `python` или `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Только при использовании `node` стек языка, указывает версию для использования              | `10.14.1`                             |

Эти свойства указаны `appSettings` в `siteConfig` коллекции в свойстве:

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

## <a name="deploy-on-consumption-plan"></a>Развертывание по плану потребления

План потребления автоматически распределяет вычислительную мощность при запуске кода, масштабируется по мере необходимости для обработки нагрузки, а затем масштабируется, когда код не работает. Вам не придется платить за простоя VMs, и вам не придется резервировать емкость заранее. Дополнительные сведения см. в статье [Масштабирование и размещение Функций Azure](functions-scale.md#consumption-plan).

Образец шаблона диспетчера ресурсов Azure см. на странице [Function app on Consumption plan] (План потребления приложения-функции)

### <a name="create-a-consumption-plan"></a>Создание плана потребления

План потребления не нуждается в определении. Один из них будет автоматически создаваться или выбираться на основе региона при создании самого ресурса приложения функции.

План потребления — это особый тип ресурса «серверфарм». Для Windows можно указать его, используя `Dynamic` значение для `computeMode` свойств: `sku`

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> План потребления не может быть четко определен для Linux. Он будет создан автоматически.

Если вы четко определите свой план потребления, `serverFarmId` вам нужно будет установить свойство в приложении, чтобы оно указало на идентификатор ресурса плана. Вы должны убедиться, что `dependsOn` приложение функции имеет настройки для плана, а также.

### <a name="create-a-function-app"></a>Создание приложения-функции

#### <a name="windows"></a>Windows

В Windows план потребления требует двух дополнительных настроек в конфигурации сайта: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` и `WEBSITE_CONTENTSHARE`: Эти свойства настраивают учетную запись хранения и путь к файлам кода приложения-функции и конфигурации.

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

На Linux, функция приложение `kind` должно `functionapp,linux`иметь свой набор, `reserved` и `true`он должен иметь свойство установить на:

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

## <a name="deploy-on-premium-plan"></a>Развертывание по премиум-плану

Премиум-план предлагает такое же масштабирование, как и план потребления, но включает выделенные ресурсы и дополнительные возможности. Чтобы узнать больше, смотрите [премиум-план Функции Azure.](./functions-premium-plan.md)

### <a name="create-a-premium-plan"></a>Создание плана "Премиум"

Премиум-план — это особый тип ресурса «серверфарм». Вы можете указать `EP1`его `EP2`с `EP3` помощью либо , или для значения `Name` свойства в `sku` [объекте описания.](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Создание приложения-функции

Функциональное приложение в плане Premium `serverFarmId` должно иметь набор свойств в идентификатор ресурса плана, созданного ранее. Кроме того, план Premium требует двух дополнительных `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` настроек в конфигурации сайта: и `WEBSITE_CONTENTSHARE`. Эти свойства настраивают учетную запись хранения и путь к файлам кода приложения-функции и конфигурации.

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

## <a name="deploy-on-app-service-plan"></a>Развертывание в плане службы приложений

В плане службы приложений ваши приложения-функции запускаются на выделенных виртуальных машинах на Basic, Standard и Premium SKU аналогично веб-приложениям. Дополнительную информацию о том, как действует план службы приложений, см. в статье [Подробный обзор планов службы приложений Azure](../app-service/overview-hosting-plans.md).

Образец шаблона Azure Resource Manager см. на странице [Function app on Azure App Service plan] (Приложение-функция в плане службы приложений Azure).

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

План службы приложений определяется ресурсом "serverfarm".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Чтобы запустить приложение на Linux, необходимо `kind` `Linux`также установить:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Создание приложения-функции

Функциональное приложение в плане Службы `serverFarmId` App должно иметь набор свойств в идентификаторе ресурсов плана, созданного ранее.

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

Linux приложения должны `linuxFxVersion` также `siteConfig`включать свойство под . Если вы просто развертываете код, значение для этого определяется желаемым стеком времени выполнения:

| Стек            | Пример значения                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
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

При [развертывании пользовательского изображения контейнера](./functions-create-function-linux-custom-image.md)необходимо `linuxFxVersion` указать его с конфигурацией, которая позволяет вытащить изображение, как в [Web App для контейнеров.](/azure/app-service/containers) Кроме того, `false`установите, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` так как содержимое приложения предоставляется в самом контейнере:

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

Приложение-функция содержит много дочерних ресурсов, которые можно использовать при развертывании, в том числе параметры приложения и параметры системы управления версиями. Вы также можете удалить ресурс **«Исходный элемент** управления» и вместо этого использовать другой [вариант развертывания.](functions-continuous-deployment.md)

> [!IMPORTANT]
> Чтобы с помощью Azure Resource Manager успешно развернуть приложение, важно понимать, каким образом ресурсы развертываются в Azure. В следующем примере конфигурации верхнего уровня применяются с помощью **siteConfig**. Их важно задать на верхнем уровне, так как эти конфигурации передают сведения в среду выполнения функций и механизм развертывания. Сведения верхнего уровня требуются перед применением дочернего ресурса **sourcecontrols/web**. Хотя эти параметры можно настроить в дочернем ресурсе **config/appSettings**, в некоторых сценариях приложение-функцию требуется развернуть *до применения * **config/appSettings**. В таких случаях, например в [Logic Apps](../logic-apps/index.yml), функции зависят от другого ресурса.

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
> Этот шаблон использует значение настроек приложения [Project,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) которое устанавливает базовый каталог, в котором движок развертывания функций (Kudu) ищет развертываемый код. В нашем репозитории функции находятся во вложенной папке папки **src**. Таким образом, в предыдущем примере мы задаем для параметров приложения значение `src`. Если ваши функции находятся в корневой папке репозитория, или если развертывание выполняется не из системы управления версиями, то это значение параметров приложения можно удалить.

## <a name="deploy-your-template"></a>Развертывание шаблона

Для развертывания шаблона можно использовать любой из следующих способов:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Лазурный CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Портал Azure](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

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

### <a name="deploy-using-powershell"></a>Развертывание с помощью PowerShell

Следующие команды PowerShell создают группу ресурсов и развертывают шаблон, который создает функциональное приложение с необходимыми ресурсами. Для локального запуска необходимо установить [Azure PowerShell.](/powershell/azure/install-az-ps) Беги, [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) чтобы войти.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Чтобы проверить это развертывание, можно использовать [шаблон, подобный этому,](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) который создает функциональное приложение на Windows в плане потребления. Замените `<function-app-name>` уникальное имя для приложения функции.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке и настройке Функций Azure:

* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Как настроить настройки приложения функции Azure](functions-how-to-use-azure-function-app-settings.md)
* [Создание функции Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Function app on Consumption plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json (Приложение-функция в плане потребления)
[Function app on Azure App Service plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json (Приложение-функция в плане службы приложений Azure).
