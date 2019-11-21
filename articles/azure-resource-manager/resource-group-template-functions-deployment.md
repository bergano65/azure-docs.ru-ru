---
title: Template functions - deployment
description: Описывает функции, используемые в шаблоне Azure Resource Manager для получения сведений о развертывании.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: a255cea128241465788f21013eb0522a29f5bd9e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230235"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Функции развертывания для шаблонов Azure Resource Manager 

Диспетчер ресурсов предоставляет следующие функции для получения значений из разделов шаблонов и значений, связанных с развертыванием:

* [deployment](#deployment)
* [environment](#environment)
* [parameters](#parameters)
* [variables](#variables)

Сведения о получении значений из ресурсов, групп ресурсов или подписки см. в разделе [Функции для работы с ресурсами](resource-group-template-functions-resource.md).

## <a name="deployment"></a>Развертывание

`deployment()`

Возвращает сведения о текущей операции развертывания.

### <a name="return-value"></a>Возвращаемое значение

Эта функция возвращает объект, который передается во время развертывания. Свойства в возвращаемом объекте зависят от того, передается ли объект развертывания в виде ссылки или встроенного объекта. Когда объект развертывания передается встроенным (например, при использовании параметра **-TemplateFile** в Azure PowerShell для определения локального файла), формат возвращаемого объекта будет таким:

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
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Когда объект передается в виде ссылки, как, например, при использовании параметра **-TemplateUri** для указания на удаленный объект, объект возвращается в следующем формате. 

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
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

При [развертывании в подписку Azure](deploy-to-subscription.md) возвращаемый объект содержит свойство `location`, а не группу ресурсов. Свойство расположения включается при развертывании локального или внешнего шаблона.

### <a name="remarks"></a>Примечания

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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
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
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Если используется шаблон уровня подписки, в котором применяется функция развертывания, ознакомьтесь с [этой функцией](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Она развертывается с помощью команды `az deployment create` или `New-AzDeployment`.

## <a name="environment"></a>Среда

`environment()`

Returns information about the Azure environment used for deployment.

### <a name="return-value"></a>Возвращаемое значение

This function returns properties for the current Azure environment.

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

The following example template returns the environment object.

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

The preceding example returns the following object when deployed to global Azure:

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

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Возвращает значение параметра. Указанное имя параметра должно быть определено в разделе параметров шаблона.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_параметра |ДА |string |Имя параметра, который требуется вернуть. |

### <a name="return-value"></a>Возвращаемое значение

Значение указанного параметра.

### <a name="remarks"></a>Примечания

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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Name | Тип | Value |
| ---- | ---- | ----- |
| stringOutput | Строка | вариант 1 |
| intOutput | Int | 1 |
| objectOutput | Объекты | {"one": "a", "two": "b"} |
| arrayOutput | Массив, | [1, 2, 3] |
| crossOutput | Строка | вариант 1 |

For more information about using parameters, see [Parameters in Azure Resource Manager template](template-parameters.md).

## <a name="variables"></a>variables

`variables(variableName)`

Возвращает значение переменной. Указанное имя переменной должно быть определено в разделе переменных шаблона.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | Тип | Описание |
|:--- |:--- |:--- |:--- |
| variableName |ДА |Строка |Имя переменной, которую необходимо вернуть. |

### <a name="return-value"></a>Возвращаемое значение

Значение указанной переменной.

### <a name="remarks"></a>Примечания

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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Name | Тип | Value |
| ---- | ---- | ----- |
| exampleOutput1 | Строка | myVariable |
| exampleOutput2 | Массив, | [1, 2, 3, 4] |
| exampleOutput3 | Строка | myVariable |
| exampleOutput4 |  Объекты | {"property1": "value1", "property2": "value2"} |

For more information about using variables, see [Variables in Azure Resource Manager template](template-variables.md).

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Функции развертывания для шаблонов Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).

