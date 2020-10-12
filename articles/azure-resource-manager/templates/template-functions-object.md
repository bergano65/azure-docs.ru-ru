---
title: Функции шаблонов — объекты
description: Описывает функции, используемые в шаблоне Azure Resource Manager для работы с объектами.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fede4d6c71e45b119e500d4c9c6f91765d052036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84676800"
---
# <a name="object-functions-for-arm-templates"></a>Функции объектов для шаблонов ARM

Диспетчер ресурсов предоставляет несколько функций для работы с объектами в шаблоне Azure Resource Manager (ARM).

* [contains](#contains)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
* [union](#union)

## <a name="contains"></a>содержит

`contains(container, itemToFind)`

Проверяет, содержит ли массив значение, содержит ли объект ключ или содержит ли строка подстроку. При сравнении строк учитывается регистр. Тем не менее при проверке объекта на наличие ключа сравнение выполняется без учета регистра.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| контейнер |Да |массив, объект или строка |Значение, содержащее значение, которое необходимо найти. |
| itemToFind |Да |строка или целое число |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

**True**, если элемент найден. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) показано, как использовать функцию contains с различными типами.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="empty"></a>пустых

`empty(itemToTest)`

Определяет, являются ли пустыми массив, объект или строка.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| itemToTest |Да |массив, объект или строка |Значение, которое необходимо проверить, если оно пустое. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если значение пустое. В противном случае — **False**.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) проверяется, являются ли пустыми массив, объект и строка.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="intersection"></a>пересечению

`intersection(arg1, arg2, arg3, ...)`

Возвращает из параметров один массив или объект с общими элементами.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив или объект |Первое значение для поиска общих элементов. |
| arg2 |Да |массив или объект |Второе значение для поиска общих элементов. |
| дополнительные аргументы |нет |массив или объект |Дополнительные значения для поиска общих элементов. |

### <a name="return-value"></a>Возвращаемое значение

Массив или объект с общими элементами.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) показано, как использовать пересечение с массивами и объектами.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| objectOutput | Объект | {"one": "a", "three": "c"} |
| arrayOutput | Массив | ["two", "three"] |

## <a name="json"></a>json

`json(arg1)`

Возвращает объект JSON.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |строка |Значение для преобразования в JSON. |

### <a name="return-value"></a>Возвращаемое значение

Объект JSON из указанной строки или пустой объект, если указано значение **null**.

### <a name="remarks"></a>Remarks

Если необходимо включить значение параметра или переменную в объект JSON, используйте функцию [concat](template-functions-string.md#concat) для создания строки, передаваемой в функцию.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) показано, как использовать функцию JSON. Обратите внимание, что можно передать строку, представляющую объект, или использовать **значение NULL** , если значение не требуется.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonObject1": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject2": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput1": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonObject1')))]"
        },
        "jsonOutput2": {
            "type": "object",
            "value": "[json(parameters('jsonObject2'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| jsonOutput1 | Логическое значение | True |
| jsonOutput2 | Объект | {"a": "b"} |
| paramOutput | Объект | {"a": "demo value"}

## <a name="length"></a>length

`length(arg1)`

Возвращает количество элементов в массиве, символах в строке или свойствах корневого уровня в объекте.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив, строка или объект |Массив, используемый для получения числа элементов, строки, используемой для получения числа символов, или объекта, используемого для получения числа свойств корневого уровня. |

### <a name="return-value"></a>Возвращаемое значение

Целое число.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) показано, как использовать функцию length с массивом и строкой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| обжектленгс | Int | 4 |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Возвращает из параметров один массив или объект со всеми элементами. Повторяющиеся значения или ключи включаются только один раз.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив или объект |Первое значение для объединения элементов. |
| arg2 |Да |массив или объект |Второе значение для объединения элементов. |
| дополнительные аргументы |нет |массив или объект |Дополнительные значения для объединения элементов. |

### <a name="return-value"></a>Возвращаемое значение

Массив или объект.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) показано, как использовать функцию union с массивами и объектами.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| objectOutput | Объект | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Массив | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Дальнейшие действия

* Описание разделов в шаблоне Azure Resource Manager см. [в разделе Общие сведения о структуре и синтаксисе шаблонов ARM](template-syntax.md).
