---
title: Определение нескольких экземпляров переменной
description: Используйте операцию копирования в шаблоне управления ресурсами Azure для итерации несколько раз при создании переменной.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153307"
---
# <a name="variable-iteration-in-arm-templates"></a>Переменная итерация в шаблонах ARM

В этой статье показано, как создать несколько значений для переменной в шаблоне Azure Resource Manager (ARM). Добавляя элемент **копирования** в раздел переменных шаблона, можно динамически установить количество элементов для переменной во время развертывания. Вы также избежать необходимости повторять шаблон синтаксиса.

Также можно использовать копию с [ресурсами,](copy-resources.md) [свойствами в ресурсе](copy-properties.md)и [выходами.](copy-outputs.md)

## <a name="variable-iteration"></a>Итерация переменной

Элемент копирования имеет следующий общий формат:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Свойство **имени** — это любое значение, идентифицируя цикл. Свойство **подсчета** определяет количество итераций, которые вы хотите для переменной.

Свойство **ввода** определяет свойства, которые вы хотите повторить. Создается массив элементов, построенных из значения **в входно-свойстве.** Это может быть одно свойство (например, строка) или объект с несколькими свойствами.

В следующем примере показано, как создать массив значений строки:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

Предыдущий шаблон возвращает массив со следующими значениями:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

В следующем примере показано, как создать массив объектов с тремя свойствами - имя, diskSizeGB и diskIndex.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

Предыдущий пример возвращает массив со следующими значениями:

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> Переменная итерация поддерживает смещение аргумента. Смещение должно прийти после названия итерации, например, copyIndex ('diskNames', 1). Если вы не предоставляете офсетное значение, оно по умолчанию по умолчанию до 0 в первом экземпляре.
>

Элемент копирования также можно использовать в переменной. Следующий пример создает объект, который имеет массив в качестве одного из своих значений.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

Предыдущий пример возвращает объект со следующими значениями:

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

Следующий пример показывает различные способы использования копии с переменными.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>Ограничения копирования

Количество не может превышать 800.

Подсчет не может быть отрицательным числом. Если вы развернете шаблон с Azure PowerShell 2.6 или позже, Azure CLI 2.0.74 или позже, или rest API версии **2019-05-10** или позже, можно установить значение до нуля. Более ранние версии PowerShell, CLI и REST API не поддерживают ноль для подсчета.

## <a name="example-templates"></a>Образцы шаблонов

Следующие примеры показывают общие сценарии для создания более одного значения для переменной.

|Шаблон  |Описание  |
|---------|---------|
|[Копирование переменных](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Демонстрирует разные способы итерации переменных. |
|[Несколько правил безопасности](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Развертывает несколько правил безопасности в группу безопасности сети. Кроме того, этот шаблон создает правила безопасности на основе параметра. Чтобы узнать параметр, см. [файл параметров нескольких групп безопасности сети](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы пройти через учебник, [см. Tutorial: создайте несколько экземпляров ресурсов с помощью шаблонов ARM.](template-tutorial-create-multiple-instances.md)
* Для других видов использования элемента копирования см.:
  * [Итерация ресурсов в шаблонах ARM](copy-resources.md)
  * [Итерация свойств в шаблонах ARM](copy-properties.md)
  * [Итерация вывода в шаблонах ARM](copy-outputs.md)
* Если вы хотите узнать о разделах шаблона, [см.](template-syntax.md)
* Чтобы узнать, как развернуть шаблон, [см. Развертывание приложения с шаблоном ARM.](deploy-powershell.md)

