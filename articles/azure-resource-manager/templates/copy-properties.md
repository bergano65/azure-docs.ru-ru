---
title: Определение нескольких экземпляров свойства
description: Используйте операцию копирования в шаблоне управления ресурсами Azure для итерации несколько раз при создании свойства на ресурсе.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e86d38b0e5d2e39d54b3c419b6eebdcda74022db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258113"
---
# <a name="property-iteration-in-arm-templates"></a>Итерация свойств в шаблонах ARM

В этой статье показано, как создать несколько экземпляров свойства в шаблоне Azure Resource Manager (ARM). Добавляя элемент **копирования** в раздел свойств ресурса в шаблоне, можно динамически установить количество элементов для свойства во время развертывания. Вы также избежать необходимости повторять шаблон синтаксиса.

Вы также можете использовать копию с [ресурсами, переменными](copy-variables.md)и [выходами.](copy-outputs.md) [resources](copy-resources.md)

## <a name="property-iteration"></a>Итерация свойства

Элемент копирования имеет следующий общий формат:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Для **имени**укажите имя ресурса, которое вы хотите создать. Свойство **подсчета** определяет количество итераций, которые вы хотите для свойства.

Свойство **ввода** определяет свойства, которые вы хотите повторить. Создается массив элементов, построенных из значения **в входно-свойстве.**

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
> Итерация свойств также поддерживает смещенный аргумент. Смещение должно прийти после названия итерации, например, copyIndex ('dataDisks', 1).
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
  "name": "exampleLB",
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

Количество не может превышать 800.

Подсчет не может быть отрицательным числом. Если вы развернете шаблон с Azure PowerShell 2.6 или позже, Azure CLI 2.0.74 или позже, или rest API версии **2019-05-10** или позже, можно установить значение до нуля. Более ранние версии PowerShell, CLI и REST API не поддерживают ноль для подсчета.

## <a name="example-templates"></a>Образцы шаблонов

В следующем примере показан общий сценарий для создания более одного значения для свойства.

|Шаблон  |Описание  |
|---------|---------|
|[Развертывание виртуальной машины с переменным количеством дисков данных](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Развертывает несколько дисков данных с виртуальной машиной. |

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы пройти через учебник, [см. Tutorial: создайте несколько экземпляров ресурсов с помощью шаблонов ARM.](template-tutorial-create-multiple-instances.md)
* Для других видов использования элемента копирования см.:
  * [Итерация ресурсов в шаблонах ARM](copy-resources.md)
  * [Переменная итерация в шаблонах ARM](copy-variables.md)
  * [Итерация вывода в шаблонах ARM](copy-outputs.md)
* Если вы хотите узнать о разделах шаблона, [см.](template-syntax.md)
* Чтобы узнать, как развернуть шаблон, [см. Развертывание приложения с шаблоном ARM.](deploy-powershell.md)

