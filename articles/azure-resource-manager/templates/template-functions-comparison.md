---
title: Функции шаблонов — сравнение
description: Описывает функции, используемые в шаблоне Azure Resource Manager для сравнения значений.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 01d66f43cf73dcc9228118db5a9b6149b19ee66d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677837"
---
# <a name="comparison-functions-for-arm-templates"></a>Функции сравнения для шаблонов ARM

Диспетчер ресурсов предоставляет несколько функций для выполнения сравнений в шаблонах Azure Resource Manager (ARM).

* [coalesce](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

Возвращает из параметров первое значение, отличное от null. Пустые строки, пустые массивы и пустые объекты не имеют значение null.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число, строка, массив или объект |Первое значение, которое проверяется на соответствие значению null. |
| дополнительные аргументы |Нет |целое число, строка, массив или объект |Дополнительные значения, которые проверяются на соответствие значению null. |

### <a name="return-value"></a>Возвращаемое значение

Значение первых параметров, отличных от null, которое может быть строкой, целым числом, массивом или объектом. Null, если все параметры имеют значение null.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) показаны выходные данные для разных случаев использования coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null,
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Type | Значение |
| ---- | ---- | ----- |
| stringOutput | Строка | default |
| intOutput | Int | 1 |
| objectOutput | Объект | {"first": "default"} |
| arrayOutput | Массив |  [1] |
| emptyOutput | Bool | True |

## <a name="equals"></a>equals (равно)

`equals(arg1, arg2)`

Проверяет, являются ли два значения равными.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число, строка, массив или объект |Первое значение, которое необходимо проверить на равенство. |
| arg2 |Да |целое число, строка, массив или объект |Второе значение, которое необходимо проверить на равенство. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если значения равны. В противном случае — **False**.

### <a name="remarks"></a>Комментарии

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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Имя | Type | Значение |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) используется [not](template-functions-logical.md#not) и **equals**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Имя | Type | Значение |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Проверяет, является ли первое значение большим, чем второе.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли большим). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли большим). |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение больше второго. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) проверяется, является ли первое значение большим, чем второе.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Имя | Type | Значение |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Проверяет, является ли первое значение большим, чем второе, или равным ему.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли большим или равным). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли большим или равным). |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение больше или равно второму. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) проверяется, является ли первое значение большим, чем второе, или равным ему.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Имя | Type | Значение |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="less"></a>less

`less(arg1, arg2)`

Проверяет, является ли первое значение меньшим, чем второе.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли меньшим). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли меньшим). |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение меньше второго. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) проверяется, является ли первое значение меньшим, чем второе.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Имя | Type | Значение |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Проверяет, является ли первое значение меньшим, чем второе, или равным ему.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли меньшим или равным). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли меньшим или равным). |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение меньше или равно второму. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) проверяется, является ли первое значение меньшим, чем второе, или равным ему.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Имя | Type | Значение |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="next-steps"></a>Дальнейшие шаги

* Описание разделов в шаблоне Azure Resource Manager см. [в разделе Общие сведения о структуре и синтаксисе шаблонов ARM](template-syntax.md).
