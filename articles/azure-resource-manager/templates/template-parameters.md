---
title: Параметры в шаблонах
description: Описывает, как определить параметры в шаблоне управления ресурсами Azure.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122429"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Параметры в шаблонах управления ресурсами Azure

В этой статье описывается, как определить и использовать параметры в шаблоне Azure Resource Manager. Предоставляя различные значения для параметров, можно повторно использовать шаблон для различных сред.

Диспетчер ресурсов решает значения параметров перед началом операций развертывания. Везде, где параметр используется в шаблоне, менеджер ресурсов заменяет его на разрешенное значение.

## <a name="define-parameter"></a>Определение параметра

Следующий пример демонстрирует определение простого параметра. Он определяет параметр, названный **storageSKU.** Параметр является значением строки и принимает только значения, действительные для его целевого использования. Параметр использует значение по умолчанию, когда во время развертывания значение не предусмотрено.

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

## <a name="use-parameter"></a>Использование параметра

В шаблоне вы ссылаетесь на значение параметра, используя функцию [параметров.](template-functions-deployment.md#parameters) В следующем примере значение параметра используется для установки SKU для учетной записи хранилища.

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

Чтобы определить значение по умолчанию для параметра, можно использовать большинство функций шаблонов. Также для создания значения по умолчанию можно использовать значение другого параметра. Следующий шаблон демонстрирует использование функций в значении по умолчанию. Когда имя не предусмотрено для сайта, он создает уникальное значение строки и придает его **к сайту.** Когда имя не предусмотрено для плана хоста, оно принимает значение для сайта и придаткивает **-план**.

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

Вы не можете использовать [функцию ссылки](template-functions-resource.md#reference) или любую из функций [списка](template-functions-resource.md#list) в разделе параметров. Эти функции получают состояние времени выполнения ресурса и не могут быть выполнены до развертывания при разбавении параметров.

## <a name="objects-as-parameters"></a>Объекты в качестве параметров

Чтобы структурировать связанные друг с другом значения, их можно передать в виде объекта. Что не менее важно, этот подход сокращает число параметров в шаблоне.

В следующем примере показан параметр, который является объектом. Значение по умолчанию отображает ожидаемые свойства объекта.

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

Вы ссылаетесь на свойства объекта с помощью точечного оператора.

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

Следующие примеры демонстрируют сценарии использования параметров.

|Шаблон  |Описание  |
|---------|---------|
|[Использование параметров с функциями для определения значений по умолчанию](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Демонстрирует, как можно применить функции шаблонов при определении значений по умолчанию для параметров. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |
|[объект параметра](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Демонстрирует использование объекта в качестве параметра. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |


## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать о доступных свойствах для параметров, [см. Понимание структуры и синтаксиса шаблонов Azure Resource Manager.](template-syntax.md)
* Чтобы узнать о прохождении значений параметров в файле, [см.](parameter-files.md)
* Рекомендации по созданию [Best practices - parameters](template-best-practices.md#parameters)параметров см.
