---
title: Параметры в шаблонах
description: Описывает, как определить параметры в шаблоне Azure Resource Manager (шаблон ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 929d871bc0087d4fda585773b349dee4e0945c7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934719"
---
# <a name="parameters-in-arm-templates"></a>Параметры в шаблонах ARM

В этой статье описывается определение и использование параметров в шаблоне Azure Resource Manager (шаблон ARM). Предоставляя различные значения параметров, можно повторно использовать шаблон для разных сред.

Диспетчер ресурсов разрешает значения параметров перед началом операций развертывания. Везде, где в шаблоне используется параметр, диспетчер ресурсов заменяет его разрешенным значением.

Для каждого параметра необходимо задать один из [типов данных](template-syntax.md#data-types).

## <a name="define-parameter"></a>Определить параметр

Следующий пример демонстрирует определение простого параметра. Он определяет параметр с именем `storageSKU` . Параметр является строковым значением и принимает только значения, допустимые для предполагаемого использования. Параметр использует значение по умолчанию, если во время развертывания не указано значение.

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

В шаблоне ссылка на значение параметра осуществляется с помощью функции [Parameters](template-functions-deployment.md#parameters) . В следующем примере значение параметра используется для задания номера SKU для учетной записи хранения.

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

Нельзя использовать функцию [Reference](template-functions-resource.md#reference) или любую из функций [List](template-functions-resource.md#list) в разделе Parameters. Эти функции получают состояние среды выполнения ресурса и не могут быть выполнены перед развертыванием при разрешении параметров.

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
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
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
|[Объект Parameter](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Демонстрирует использование объекта в качестве параметра. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о доступных свойствах параметров см. в разделе Общие сведения о [структуре и синтаксисе шаблонов ARM](template-syntax.md).
* Дополнительные сведения о передаче значений параметров в виде файла см. в разделе [Create диспетчер ресурсов Parameter File](parameter-files.md).
* Рекомендации по созданию параметров см. в разделе рекомендации [— Параметры](template-best-practices.md#parameters).
