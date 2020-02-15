---
title: Определение нескольких экземпляров свойства
description: Используйте операцию копирования в шаблоне Azure Resource Manager для многократного выполнения итерации при создании свойства ресурса.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b1e31f981f361b4cfbe7e7930f2c70bfce8b8656
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210870"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Итерация свойств в шаблонах Azure Resource Manager

В этой статье показано, как создать более одного экземпляра свойства в шаблоне Azure Resource Manager. Добавив элемент **Copy** в раздел свойств ресурса в шаблоне, можно динамически задать количество элементов для свойства во время развертывания. Кроме того, не нужно повторять синтаксис шаблона.

Можно также использовать Copy с [ресурсами](copy-resources.md) и [переменными](copy-variables.md).

## <a name="property-iteration"></a>Итерация свойства

Элемент Copy имеет следующий общий формат:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

В **поле имя**укажите имя свойства ресурса, которое необходимо создать. Свойство **Count** указывает количество итераций, которое требуется для свойства.

Свойство **input** указывает свойства, которые необходимо повторить. Создается массив элементов, созданных на основе значения **входного** свойства.

В следующем примере показано, как применить массив `copy` к свойству dataDisks на виртуальной машине:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Обратите внимание, что при использовании `copyIndex` в итерации свойства, необходимо указать имя итерации.

> [!NOTE]
> Итерация свойства также поддерживает аргумент offset. Смещение должно располагаться после имени итерации, например copyIndex (' "диски", 1).
>

Диспетчер ресурсов разворачивает массив `copy` во время развертывания. Имя массива становится именем свойства. Входные значения становятся свойствами объекта. Развернутый шаблон выглядит так:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

Элемент копирования представляет собой массив, поэтому вы можете указать несколько свойств для ресурса.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Вы можете совместно использовать итерацию свойства и ресурса. Обращайтесь к итерации свойства по имени.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Ограничения копирования

Число не может превышать 800.

Число не может быть отрицательным числом. Если вы развертываете шаблон с Azure PowerShell 2,6 или более поздней версии, Azure CLI 2.0.74 или более поздней версии или REST API версию **2019-05-10** или более поздней, можно установить значение счетчика равным нулю. Более ранние версии PowerShell, CLI и REST API не поддерживают нулевое значение для счетчика.

## <a name="example-templates"></a>Образцы шаблонов

В следующем примере показан типичный сценарий для создания более одного значения для свойства.

|Шаблон  |Описание  |
|---------|---------|
|[Развертывание виртуальной машины с переменным количеством дисков данных](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Развертывает несколько дисков данных с виртуальной машиной. |

## <a name="next-steps"></a>Следующие шаги

* См. статью [Руководство: создание нескольких экземпляров ресурса с помощью шаблонов Resource Manager](template-tutorial-create-multiple-instances.md).
* Другие способы использования элемента copy см. в разделе [Итерация ресурсов в шаблонах Azure Resource Manager](copy-resources.md) и [переменная Iteration в Azure Resource Manager Templates](copy-variables.md).
* Сведения о разделах шаблона см. в статье, посвященной [созданию шаблонов Azure Resource Manager](template-syntax.md).
* Инструкции по развертыванию шаблонов см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](deploy-powershell.md).

