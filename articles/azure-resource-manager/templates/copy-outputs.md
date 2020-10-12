---
title: Определение нескольких экземпляров выходного значения
description: Используйте операцию копирования в шаблоне Azure Resource Manager для многократного выполнения итерации при возврате значения из развертывания.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 50c4b4b8f301ad88d3dfde98ace1aed4431693db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82583425"
---
# <a name="output-iteration-in-arm-templates"></a>Выходная итерация в шаблонах ARM

В этой статье показано, как создать более одного значения для выходных данных в шаблоне Azure Resource Manager (ARM). Добавив элемент **Copy** в раздел Outputs шаблона, можно динамически возвращать ряд элементов во время развертывания.

Можно также использовать Copy с [ресурсами](copy-resources.md), [свойствами в ресурсе](copy-properties.md)и [переменными](copy-variables.md).

## <a name="syntax"></a>Синтаксис

Элемент Copy имеет следующий общий формат:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

Свойство **Count** указывает количество итераций, которое требуется для выходного значения.

Свойство **input** указывает свойства, которые необходимо повторить. Создается массив элементов, созданных на основе значения **входного** свойства. Это может быть одно свойство (например, строка) или объект с несколькими свойствами.

## <a name="copy-limits"></a>Ограничения копирования

Число не может превышать 800.

Число не может быть отрицательным числом. Если вы развертываете шаблон с помощью последней версии Azure CLI, PowerShell или REST API, он может быть равен нулю. В частности, необходимо использовать:

* Azure PowerShell **2,6** или более поздней версии
* Azure CLI **2.0.74** или более поздней версии
* REST API версии **2019-05-10** или более поздней
* [Связанные развертывания](linked-templates.md) должны использовать API версии **2019-05-10** или более поздней для типа ресурса развертывания.

Более ранние версии PowerShell, CLI и REST API не поддерживают нулевое значение для счетчика.

## <a name="outputs-iteration"></a>Результат итерации

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

* Чтобы пройти обучение, см. раздел [учебник. Создание нескольких экземпляров ресурсов с помощью шаблонов ARM](template-tutorial-create-multiple-instances.md).
* Другие способы использования элемента copy см. в следующих статьях:
  * [Итерация ресурсов в шаблонах ARM](copy-resources.md)
  * [Итерация свойства в шаблонах ARM](copy-properties.md)
  * [Итерация переменных в шаблонах ARM](copy-variables.md)
* Если вы хотите узнать о разделах шаблона, см. раздел [Создание шаблонов ARM](template-syntax.md).
* Сведения о развертывании шаблона см. в статье [развертывание приложения с помощью шаблона ARM](deploy-powershell.md).

