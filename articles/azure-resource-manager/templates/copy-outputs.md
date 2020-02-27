---
title: Определение нескольких экземпляров выходного значения
description: Используйте операцию копирования в шаблоне Azure Resource Manager для многократного выполнения итерации при возврате значения из развертывания.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624778"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Выходная итерация в шаблонах Azure Resource Manager

В этой статье показано, как создать более одного значения для выходных данных в шаблоне Azure Resource Manager. Добавив элемент **Copy** в раздел Outputs шаблона, можно динамически возвращать ряд элементов во время развертывания.

Можно также использовать Copy с [ресурсами](copy-resources.md), [свойствами в ресурсе](copy-properties.md)и [переменными](copy-variables.md).

## <a name="outputs-iteration"></a>Результат итерации

Элемент Copy имеет следующий общий формат:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Свойство **Count** указывает количество итераций, которое требуется для выходного значения.

Свойство **input** указывает свойства, которые необходимо повторить. Создается массив элементов, созданных на основе значения **входного** свойства. Это может быть одно свойство (например, строка) или объект с несколькими свойствами.

В следующем примере создается переменное число учетных записей хранения и возвращается конечная точка для каждой учетной записи хранения:

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

В следующем примере возвращаются три свойства из новых учетных записей хранения.

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

В предыдущем примере возвращается массив со следующими значениями:

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

* См. статью [Руководство: создание нескольких экземпляров ресурса с помощью шаблонов Resource Manager](template-tutorial-create-multiple-instances.md).
* Другие способы использования элемента copy см. в следующих статьях:
  * [Итерация ресурсов в шаблонах Azure Resource Manager](copy-resources.md)
  * [Итерация свойств в шаблонах Azure Resource Manager](copy-properties.md)
  * [Итерация переменных в шаблонах Azure Resource Manager](copy-variables.md)
* Сведения о разделах шаблона см. в статье, посвященной [созданию шаблонов Azure Resource Manager](template-syntax.md).
* Инструкции по развертыванию шаблонов см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](deploy-powershell.md).

