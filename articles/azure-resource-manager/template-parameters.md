---
title: Параметры в шаблонах Azure Resource Manager
description: Описывает, как определить параметры в шаблоне Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4947b00d6fad5007751cd97d43ad6aca8d775330
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383267"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Параметры в шаблонах Azure Resource Manager

В этой статье описывается определение и использование параметров в шаблоне Azure Resource Manager. Предоставляя различные значения параметров, можно повторно использовать шаблон для разных сред.

Диспетчер ресурсов разрешает значения параметров перед началом операций развертывания. Везде, где в шаблоне используется параметр, диспетчер ресурсов заменяет его разрешенным значением.

## <a name="define-parameter"></a>Определить параметр

Следующий пример демонстрирует определение простого параметра. Он определяет параметр с именем **сторажеску**. Параметр является строковым значением и принимает только значения, допустимые для предполагаемого использования. Параметр использует значение по умолчанию, если во время развертывания не указано значение.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Использовать параметр

В шаблоне ссылка на значение параметра осуществляется с помощью функции [Parameters](resource-group-template-functions-deployment.md#parameters) . В следующем примере значение параметра используется для задания номера SKU для учетной записи хранения.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Функции шаблонов

Чтобы определить значение по умолчанию для параметра, можно использовать большинство функций шаблонов. Также для создания значения по умолчанию можно использовать значение другого параметра. В следующем шаблоне показано использование функций в значении по умолчанию. Если для сайта не указано имя, оно создает уникальное строковое значение и добавляет его к **сайту**. Если для плана узла не указано имя, оно принимает значение для сайта и добавляет **-Plan**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Нельзя использовать функцию [Reference](resource-group-template-functions-resource.md#reference) или любую из функций [List](resource-group-template-functions-resource.md#list) в разделе Parameters. Эти функции получают состояние среды выполнения ресурса и не могут быть выполнены перед развертыванием при разрешении параметров.

## <a name="objects-as-parameters"></a>Объекты в качестве параметров

Чтобы структурировать связанные друг с другом значения, их можно передать в виде объекта. Что не менее важно, этот подход сокращает число параметров в шаблоне.

В следующем примере показан параметр, который является объектом. Значение по умолчанию показывает ожидаемые свойства объекта.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Для ссылки на свойства объекта используется оператор «точка».

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Образцы шаблонов

В следующих примерах демонстрируются сценарии использования параметров.

|Шаблон  |Описание  |
|---------|---------|
|[Использование параметров с функциями для определения значений по умолчанию](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Демонстрирует, как можно применить функции шаблонов при определении значений по умолчанию для параметров. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |
|[Объект параметров](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Демонстрирует использование объекта в качестве параметра. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |


## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о доступных свойствах параметров см. в разделе Общие сведения о [структуре и синтаксисе шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Дополнительные сведения о передаче значений параметров в виде файла см. в разделе [Create диспетчер ресурсов Parameter File](resource-manager-parameter-files.md).
* Рекомендации по созданию параметров см. в разделе рекомендации [— Параметры](template-best-practices.md#parameters).
