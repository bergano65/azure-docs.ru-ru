---
title: Функции шаблонов — развертывание
description: Описывает функции, используемые в шаблоне Azure Resource Manager для получения сведений о развертывании.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: e8240c05cba82d5563c4b327ecbc65a9c358720f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677820"
---
# <a name="deployment-functions-for-arm-templates"></a>Функции развертывания для шаблонов ARM

Диспетчер ресурсов предоставляет следующие функции для получения значений, связанных с текущим развертыванием шаблона Azure Resource Manager (ARM):

* [развертывания](#deployment)
* [PXE](#environment)
* [parameters](#parameters)
* [variables](#variables)

Сведения о получении значений из ресурсов, групп ресурсов или подписки см. в разделе [Функции для работы с ресурсами](template-functions-resource.md).

## <a name="deployment"></a>развертывание

`deployment()`

Возвращает сведения о текущей операции развертывания.

### <a name="return-value"></a>Возвращаемое значение

Эта функция возвращает объект, который передается во время развертывания. Свойства возвращаемого объекта различаются в зависимости от того, что вы используете:

* Развертывание шаблона, который является локальным файлом, или развертывание шаблона, который является удаленным файлом, доступ к которому осуществляется через универсальный код ресурса (URI).
* развертывание в группе ресурсов или развертывание в одной из других областей ([Подписка Azure](deploy-to-subscription.md), [Группа управления](deploy-to-management-group.md)или [клиент](deploy-to-tenant.md)).

При развертывании локального шаблона в группе ресурсов: функция возвращает следующий формат:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

При развертывании удаленного шаблона в группе ресурсов: функция возвращает следующий формат:

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

При развертывании в подписке Azure, группе управления или клиенте возвращаемый объект содержит `location` свойство. Свойство расположения включается при развертывании локального или внешнего шаблона. Формат будет следующим:

```json
{
    "name": "",
    "location": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>Комментарии

Вы можете использовать deployment() для ссылки на другой шаблон в зависимости от URI родительского шаблона.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Если вы повторно развертываете шаблон из журнала развертывания на портале, шаблон развернется в виде локального файла. Свойство `templateLink` не возвращается в функции развертывания. Если в шаблоне применяется `templateLink` для ссылки на другой шаблон, не используйте портал для повторного развертывания. Вместо этого используйте команды, с помощью которых вы развернули исходный шаблон.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) возвращается объект развертывания:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "deploymentOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

В предыдущем примере возвращается следующий объект:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>Среда

`environment()`

Возвращает сведения о среде Azure, используемой для развертывания.

### <a name="return-value"></a>Возвращаемое значение

Эта функция возвращает свойства для текущей среды Azure. В следующем примере показаны свойства для глобального Azure. Облака независимых могут возвращать несколько разных свойств.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Пример

В следующем примере шаблона возвращается объект среды.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

Предыдущий пример возвращает следующий объект при развертывании в глобальном Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>параметры

`parameters(parameterName)`

Возвращает значение параметра. Указанное имя параметра должно быть определено в разделе параметров шаблона.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание |
|:--- |:--- |:--- |:--- |
| parameterName |Да |строка |Имя параметра, который требуется вернуть. |

### <a name="return-value"></a>Возвращаемое значение

Значение указанного параметра.

### <a name="remarks"></a>Комментарии

Как правило, параметры используются, чтобы задать значения ресурсов. В следующем примере значению параметра задается имя веб-сайта, переданное во время развертывания.

```json
"parameters": {
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) показано упрощенное использование функции parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Type | Значение |
| ---- | ---- | ----- |
| stringOutput | Строка | вариант 1 |
| intOutput | Int | 1 |
| objectOutput | Объект | {"one": "a", "two": "b"} |
| arrayOutput | Массив | [1, 2, 3] |
| crossOutput | Строка | вариант 1 |

Дополнительные сведения об использовании параметров см. [в разделе Параметры в Azure Resource Manager шаблоне](template-parameters.md).

## <a name="variables"></a>variables

`variables(variableName)`

Возвращает значение переменной. Указанное имя переменной должно быть определено в разделе переменных шаблона.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| variableName |Да |Строка |Имя переменной, которую необходимо вернуть. |

### <a name="return-value"></a>Возвращаемое значение

Значение указанной переменной.

### <a name="remarks"></a>Комментарии

Как правило, переменные используются, чтобы упростить шаблон за счет создания сложных значений (единожды). В примере ниже создается уникальное имя для учетной записи хранения.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Пример

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) возвращает разные значения переменных.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
          }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Type | Значение |
| ---- | ---- | ----- |
| exampleOutput1 | Строка | myVariable |
| exampleOutput2 | Массив | [1, 2, 3, 4] |
| exampleOutput3 | Строка | myVariable |
| exampleOutput4 |  Объект | {"property1": "value1", "property2": "value2"} |

Дополнительные сведения об использовании переменных см. [в разделе переменные в шаблоне Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Дальнейшие шаги

* Описание разделов в шаблоне Azure Resource Manager см. [в разделе Общие сведения о структуре и синтаксисе шаблонов ARM](template-syntax.md).
