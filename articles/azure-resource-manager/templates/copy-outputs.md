---
title: Определение нескольких экземпляров конечного значения
description: Используйте операцию копирования в шаблоне управления ресурсами Azure для итерации несколько раз при возвращении значения из развертывания.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617823"
---
# <a name="output-iteration-in-arm-templates"></a>Итерация вывода в шаблонах ARM

В этой статье показано, как создать несколько значений для вывода в шаблоне Azure Resource Manager (ARM). Добавляя элемент **копирования** в раздел выводов шаблона, можно динамически вернуть ряд элементов во время развертывания.

Вы также можете использовать копию с [ресурсами,](copy-resources.md) [свойствами в ресурсе](copy-properties.md)и [переменными.](copy-variables.md)

## <a name="outputs-iteration"></a>Итерация выходов

Элемент копирования имеет следующий общий формат:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

Свойство **подсчета** определяет количество итераций, которые вы хотите для значения вывода.

Свойство **ввода** определяет свойства, которые вы хотите повторить. Создается массив элементов, построенных из значения **в входно-свойстве.** Это может быть одно свойство (например, строка) или объект с несколькими свойствами.

Следующий пример создает переменное число учетных записей хранения и возвращает конечную точку для каждой учетной записи хранилища:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Предыдущий шаблон возвращает массив со следующими значениями:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Следующий пример возвращает три свойства из новых учетных записей хранения.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

Предыдущий пример возвращает массив со следующими значениями:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы пройти через учебник, [см. Tutorial: создайте несколько экземпляров ресурсов с помощью шаблонов ARM.](template-tutorial-create-multiple-instances.md)
* Для других видов использования элемента копирования см.:
  * [Итерация ресурсов в шаблонах ARM](copy-resources.md)
  * [Итерация свойств в шаблонах ARM](copy-properties.md)
  * [Переменная итерация в шаблонах ARM](copy-variables.md)
* Если вы хотите узнать о разделах шаблона, [см.](template-syntax.md)
* Чтобы узнать, как развернуть шаблон, [см. Развертывание приложения с шаблоном ARM.](deploy-powershell.md)

