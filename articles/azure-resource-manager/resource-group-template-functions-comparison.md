---
title: Функции сравнения в шаблонах Azure Resource Manager | Документация Майкрософт
description: Описывает функции, используемые в шаблоне Azure Resource Manager для сравнения значений.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 6591a75577670f5bb50c1275af7e51e9b8328d50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128685"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Функции сравнения для шаблонов Azure Resource Manager

Resource Manager предоставляет ряд функций для выполнения сравнений в шаблонах.

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="equals"></a>equals (равно)
`equals(arg1, arg2)`

Проверяет, являются ли два значения равными.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число, строка, массив или объект |Первое значение, которое необходимо проверить на равенство. |
| arg2 |Да |целое число, строка, массив или объект |Второе значение, которое необходимо проверить на равенство. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если значения равны. В противном случае — **False**.

### <a name="remarks"></a>Примечания

Функция equals часто используется с элементом `condition`, чтобы проверить, развернут ли ресурс.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) проверяются разные типы значений на равенство. Все значения по умолчанию возвращают результат True.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Value |
| ---- | ---- | ----- |
| checkInts | Логический | Истина |
| checkStrings | Логический | Истина |
| checkArrays | Логический | Истина |
| checkObjects | Логический | Истина |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) используется [not](resource-group-template-functions-logical.md#not) и **equals**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Value |
| ---- | ---- | ----- |
| checkNotEquals | Логический | Истина |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Проверяет, является ли первое значение большим, чем второе.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли большим). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли большим). |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение больше второго. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) проверяется, является ли первое значение большим, чем второе.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Value |
| ---- | ---- | ----- |
| checkInts | Логический | False |
| checkStrings | Логический | Истина |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Проверяет, является ли первое значение большим, чем второе, или равным ему.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли большим или равным). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли большим или равным). |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение больше или равно второму. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) проверяется, является ли первое значение большим, чем второе, или равным ему.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Value |
| ---- | ---- | ----- |
| checkInts | Логический | False |
| checkStrings | Логический | Истина |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>меньше
`less(arg1, arg2)`

Проверяет, является ли первое значение меньшим, чем второе.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли меньшим). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли меньшим). |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение меньше второго. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) проверяется, является ли первое значение меньшим, чем второе.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Value |
| ---- | ---- | ----- |
| checkInts | Логический | Истина |
| checkStrings | Логический | False |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Проверяет, является ли первое значение меньшим, чем второе, или равным ему.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли меньшим или равным). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли меньшим или равным). |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение меньше или равно второму. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) проверяется, является ли первое значение меньшим, чем второе, или равным ему.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Value |
| ---- | ---- | ----- |
| checkInts | Логический | Истина |
| checkStrings | Логический | False |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).

