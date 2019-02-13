---
title: Раздел параметров в шаблонах Azure Resource Manager | Документация Майкрософт
description: Здесь описан раздел параметров в шаблонах Azure Resource Manager, использующий декларативный синтаксис JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2019
ms.author: tomfitz
ms.openlocfilehash: dc817302ab39d12ccd1d1a20d4dd72f94352c796
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695678"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Раздел параметров в шаблонах Azure Resource Manager
В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Задавать параметры в шаблоне не обязательно, однако без них шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

Существует ограничение в 256 параметров на один шаблон. Вы можете сократить число параметров, создавая объекты с несколькими свойствами. Этот метод описан далее в этой статье.

## <a name="define-and-use-a-parameter"></a>Определение и применение параметра

Следующий пример демонстрирует определение простого параметра. Здесь указывается имя параметра и тип его значения (строковое значение). Параметр принимает только те значения, которые имеют смысл для его назначения. Также указано значение по умолчанию, которое используется, если во время развертывания значение параметра не указано. И наконец, в определении параметра есть описание его назначения. 

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

Ссылка на значение параметра указывается в шаблоне следующим образом:

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

## <a name="available-properties"></a>Доступные свойства

В предыдущем примере представлена только часть свойств, которые можно использовать в разделе параметров. Вот полный список доступных свойств:

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Имя элемента | Обязательно | ОПИСАНИЕ |
|:--- |:--- |:--- |
| имя_параметра |Yes |Имя параметра. Должно быть допустимым идентификатором JavaScript. |
| Тип |Yes |Тип значения параметра. Допустимые типы и значения: **string**, **securestring**, **int**, **bool**, **object**, **secureObject** и **array**. |
| defaultValue |Нет  |Значение параметра, используемое по умолчанию, если пользователь не задал иное значение. |
| allowedValues |Нет  |Массив допустимых значений параметра, по которому сверяются правильные значения. |
| minValue |Нет  |Минимальное значение для параметров типа int. Это включающее значение. |
| maxValue |Нет  |Максимальное значение для параметров типа int. Это включающее значение. |
| minLength |Нет  |Минимальная длина (включительно) параметров типа string, secure string и array. |
| maxLength |Нет  |Максимальная длина (включительно) параметров типа string, secure string и array. |
| description |Нет  |Описание параметра, отображаемого для пользователей на портале. Дополнительные сведения см. в разделе [комментариев в шаблонах](resource-group-authoring-templates.md#comments). |

## <a name="template-functions-with-parameters"></a>Использование функций шаблонов с параметрами

Чтобы определить значение по умолчанию для параметра, можно использовать большинство функций шаблонов. Также для создания значения по умолчанию можно использовать значение другого параметра. Следующий шаблон демонстрирует применение функций в значениях по умолчанию:

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

В разделе параметров недоступна функция `reference`. Параметры вычисляются до развертывания, поэтому функция `reference` не может получить состояние выполнения ресурса. 

## <a name="objects-as-parameters"></a>Объекты в качестве параметров

Чтобы структурировать связанные друг с другом значения, их можно передать в виде объекта. Что не менее важно, этот подход сокращает число параметров в шаблоне.

Задайте в шаблоне параметр обычным образом, а во время развертывания передайте для этого параметра не одно значение, а объект в формате JSON. 

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

Тогда вы сможете ссылаться в шаблоне на вложенные свойства этого параметра с помощью оператора-точки.

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

Эти примеры шаблонов демонстрируют разные сценарии использования параметров. Разверните их и изучите, как обрабатываются параметры в различных сценариях.

|Шаблон  |ОПИСАНИЕ  |
|---------|---------|
|[Использование параметров с функциями для определения значений по умолчанию](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Демонстрирует, как можно применить функции шаблонов при определении значений по умолчанию для параметров. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |
|[Объект параметров](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Демонстрирует использование объекта в качестве параметра. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |

## <a name="next-steps"></a>Дополнительная информация

* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Сведения о том, как вводить значения параметров во время развертывания, см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md). 
* Дополнительные рекомендации по созданию шаблонов см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
* Сведения об использовании объекта в качестве параметра см. в статье [Использование объекта в качестве параметра в шаблоне Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
