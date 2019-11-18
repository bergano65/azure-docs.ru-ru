---
title: Функции шаблона — строка
description: Описывает функции, используемые в шаблоне Azure Resource Manager для работы со строками.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: af972bac39890402cf75e83d10806e9a830c1d4a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149609"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Строковые функции для шаблонов Azure Resource Manager

Диспетчер ресурсов предоставляет следующие функции для работы со строками:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [длина](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

Возвращает входную строку в кодировке Base64.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| входная_строка |Yes |строка |Значение, которое нужно вернуть в кодировке base64. |

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая представление в кодировке base64.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) показано, как использовать функцию base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| base64Output | Строка, | b25lLCB0d28sIHRocmVl |
| toStringOutput | Строка, | one, two, three |
| toJsonOutput | Объект. | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Преобразует представление в кодировке base64 в объект JSON.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |строка |Представление в кодировке base64, которое необходимо преобразовать в объект JSON. |

### <a name="return-value"></a>Возвращаемое значение

Объект JSON.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) функция base64ToJson используется для преобразования значения в кодировке base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| base64Output | Строка, | b25lLCB0d28sIHRocmVl |
| toStringOutput | Строка, | one, two, three |
| toJsonOutput | Объект. | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Преобразует представление в кодировке base64 в строку.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |строка |Представление в кодировке base64, которое необходимо преобразовать в строку. |

### <a name="return-value"></a>Возвращаемое значение

Строка преобразованного значения в кодировке base64.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) функция base64ToString используется для преобразования значения в кодировке base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| base64Output | Строка, | b25lLCB0d28sIHRocmVl |
| toStringOutput | Строка, | one, two, three |
| toJsonOutput | Объект. | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Объединяет несколько строковых значений и возвращает объединенную строку или объединяет несколько массивов и возвращает объединенный массив.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |строка или массив |Первое значение для сцепки. |
| дополнительные аргументы |Нет |строка |Дополнительные значения в последовательном порядке для сцепки. |

### <a name="return-value"></a>Возвращаемое значение
Строка или массив объединенных значений.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) показано, как объединить два строковых значения и получить сцепленную строку.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| concatOutput | Строка, | prefix-5yj4yjf5mbg72 |

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) показано, как объединить два массива.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| return | Массив. | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Проверяет, содержит ли массив значение, содержит ли объект ключ или содержит ли строка подстроку. При сравнении строк учитывается регистр. Тем не менее при проверке объекта на наличие ключа сравнение выполняется без учета регистра.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| container |Yes |массив, объект или строка |Значение, содержащее значение, которое необходимо найти. |
| itemToFind |Yes |строка или целое число |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

**True**, если элемент найден. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) показано, как использовать функцию contains с различными типами.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| имя | введите | Значение |
| ---- | ---- | ----- |
| stringTrue | Bool | True, |
| stringFalse | Bool | Ложь |
| objectTrue | Bool | True, |
| objectFalse | Bool | Ложь |
| arrayTrue | Bool | True, |
| arrayFalse | Bool | Ложь |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Преобразует значение в универсальный код ресурса (URI) данных.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |строка |Значение, которое необходимо преобразовать в URI данных. |

### <a name="return-value"></a>Возвращаемое значение

Строка в формате URI данных.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) значение преобразуется в универсальный код ресурса (URI) данных, а URI данных преобразуется в строку.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| dataUriOutput | Строка, | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Строка, | Привет, мир! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Преобразует значение в формате URI данных в строку.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Yes |строка |Значение URI данных, которое необходимо преобразовать. |

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая преобразованное значение.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) значение преобразуется в универсальный код ресурса (URI) данных, а URI данных преобразуется в строку.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| dataUriOutput | Строка, | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Строка, | Привет, мир! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Определяет, являются ли пустыми массив, объект или строка.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| itemToTest |Yes |массив, объект или строка |Значение, которое необходимо проверить, если оно пустое. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если значение пустое. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) проверяется, являются ли пустыми массив, объект и строка.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| имя | введите | Значение |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True, |
| objectEmpty | Bool | True, |
| stringEmpty | Bool | True, |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Определяет, заканчивается ли строка определенным значением. При сравнении регистр не учитывается.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |строка |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Yes |строка |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

**True**, если последний знак или знаки строки соответствуют значению. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) показано, как использовать функции startsWith и endsWith.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| startsTrue | Bool | True, |
| startsCapTrue | Bool | True, |
| startsFalse | Bool | Ложь |
| endsTrue | Bool | True, |
| endsCapTrue | Bool | True, |
| endsFalse | Bool | Ложь |

## <a name="first"></a>first

`first(arg1)`

Возвращает первый знак строки или первый элемент массива.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |массив или строка |Значение, из которого необходимо извлечь первый элемент или знак. |

### <a name="return-value"></a>Возвращаемое значение

Строка первого знака или тип (строка, целое число, массив или объект) первого элемента в массиве.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) показано, как использовать функцию first с массивом и строкой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| arrayOutput | Строка, | one |
| stringOutput | Строка, | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Создает форматированную строку из входных значений.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| formatString | Yes | строка | Строка составного формата. |
| arg1 | Yes | Строка, целое число или логическое значение | Значение, включаемое в отформатированную строку. |
| дополнительные аргументы | Нет | Строка, целое число или логическое значение | Дополнительные значения для включения в отформатированную строку. |

### <a name="remarks"></a>Примечания

Эта функция используется для форматирования строки в шаблоне. В нем используются те же параметры форматирования, что и в методе [System. String. Format](/dotnet/api/system.string.format) в .NET.

### <a name="examples"></a>Примеры

В следующем примере шаблона показано, как использовать функцию Format.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| форматтест | Строка, | Привет, пользователь. Форматированное число: 8 175 133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Создает значение в формате глобального уникального идентификатора на основе значений, указанных как параметры.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| baseString |Yes |строка |Значение, используемое в хэш-функции для создания GUID. |
| Дополнительные параметры (если необходимы) |Нет |строка |Можно добавить столько строк, сколько необходимо для создания значения, которое задает уровень уникальности. |

### <a name="remarks"></a>Примечания

Эта функция полезна, если нужно создать значение в формате глобального уникального идентификатора. Указываются значения параметров, которые ограничивают область уникальности результата. Можно указать, является ли уникальным имя в подписке, группе ресурсов или развертывании.

Возвращаемое значение не является случайной строкой, а результатом хэш-функции для параметров. Возвращаемое значение содержит 36 символов. Он не является глобально уникальным. Чтобы создать новый идентификатор GUID, который не основан на этом хэш-значении параметров, используйте функцию [newGuid](#newguid) .

В следующих примерах показывается, как использовать GUID при создании уникального значения для часто используемых уровней.

Уникальное в пределах подписки.

```json
"[guid(subscription().subscriptionId)]"
```

Уникальное в пределах группы ресурсов.

```json
"[guid(resourceGroup().id)]"
```

Уникальное в пределах развертывания для группы ресурсов.

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая 36 символов, в формате глобального уникального идентификатора.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) возвращаются результаты выполнения GUID.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Возвращает первую позицию значения в строке. При сравнении регистр не учитывается.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |строка |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Yes |строка |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее позицию искомого элемента. Значение отсчитывается, начиная с нуля. Если элемент не найден, возвращается значение-1.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) показано, как использовать функции indexOf и lastIndexOf.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

## <a name="last"></a>last

`last (arg1)`

Возвращает последний знак строки или последний элемент массива.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |массив или строка |Значение, из которого необходимо извлечь последний элемент или знак. |

### <a name="return-value"></a>Возвращаемое значение

Строка последнего знака или тип (строка, целое число, массив или объект) последнего элемента в массиве.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) показано, как использовать функцию last с массивом и строкой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| arrayOutput | Строка, | three |
| stringOutput | Строка, | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Возвращает последнюю позицию значения в строке. При сравнении регистр не учитывается.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |строка |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Yes |строка |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее последнюю позицию искомого элемента. Значение отсчитывается, начиная с нуля. Если элемент не найден, возвращается значение-1.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) показано, как использовать функции indexOf и lastIndexOf.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

## <a name="length"></a>длина

`length(string)`

Возвращает количество символов в строке, элементах в массиве или свойствах корневого уровня в объекте.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |массив, строка или объект |Массив, используемый для получения числа элементов, строки, используемой для получения числа символов, или объекта, используемого для получения числа свойств корневого уровня. |

### <a name="return-value"></a>Возвращаемое значение

Целое число. 

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) показано, как использовать функцию length с массивом и строкой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| имя | введите | Значение |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13. |
| обжектленгс | int | 4\. |

## <a name="newguid"></a>newGuid

`newGuid()`

Возвращает значение в формате глобального уникального идентификатора. **Эта функция может использоваться только в значении по умолчанию для параметра.**

### <a name="remarks"></a>Примечания

Эту функцию можно использовать только в выражении для значения по умолчанию для параметра. Использование этой функции в любом месте шаблона возвращает ошибку. Функция не разрешена в других частях шаблона, поскольку она возвращает другое значение при каждом вызове. Развертывание одного и того же шаблона с теми же параметрами не будет уверенно давать одинаковые результаты.

Функция newGuid отличается от функции [GUID](#guid) , так как она не принимает никаких параметров. При вызове идентификатора GUID с тем же параметром каждый раз возвращается один и тот же идентификатор. Используйте идентификатор GUID, если необходимо надежно создать тот же идентификатор GUID для конкретной среды. Используйте newGuid, если каждый раз требуется другой идентификатор, например развертывание ресурсов в тестовой среде.

Если вы используете [параметр для повторного развертывания предыдущего успешного развертывания](rollback-on-error.md), а предыдущее развертывание включает параметр, использующий newGuid, параметр не вычисляется повторно. Вместо этого значение параметра из предыдущего развертывания автоматически используется повторно в развертывании отката.

В тестовой среде может потребоваться многократное развертывание ресурсов, которые находятся только в течение короткого времени. Вместо создания уникальных имен можно использовать newGuid с [uniqueString](#uniquestring) для создания уникальных имен.

Будьте внимательны при повторном развертывании шаблона, который использует функцию newGuid для значения по умолчанию. При повторном развертывании без предоставления значения для параметра функция вычисляется повторно. Если вы хотите обновить существующий ресурс, а не создать новый, передайте значение параметра из предыдущего развертывания.

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая 36 символов, в формате глобального уникального идентификатора.

### <a name="examples"></a>Примеры

В следующем примере шаблона показан параметр с новым идентификатором.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

Выходные данные предыдущего примера зависят от каждого развертывания, но будут выглядеть примерно так:

| имя | введите | Значение |
| ---- | ---- | ----- |
| гуидаутпут | строка | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

В следующем примере функция newGuid используется для создания уникального имени для учетной записи хранения. Этот шаблон может работать для тестовой среды, где учетная запись хранения существует в течение короткого времени и не развертывается повторно.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

Выходные данные предыдущего примера зависят от каждого развертывания, но будут выглядеть примерно так:

| имя | введите | Значение |
| ---- | ---- | ----- |
| намеаутпут | строка | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Возвращает выровненную по правому краю строку, добавляя символы в левую часть до достижения общее указанной длины.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| значение_для_заполнения |Yes |строка или целое число |Значение, выравниваемое по правому краю. |
| общая_длина |Yes |int |Общее число символов в возвращаемой строке. |
| символ_заполнения |Нет |один знак |Символ, используемый для заполнения левой части до достижения общей длины. Значение по умолчанию — пробел. |

Если длина исходной строки превышает число знаков для заполнения, то знаки не добавляются.

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая по крайней мере число указанных знаков.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) показано, как заполнить указанное пользователем значение параметра, добавляя знак нуля до достижения общего числа знаков. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| stringOutput | Строка, | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

Возвращает новую строку, в которой все экземпляры одной строки заменены другой строкой.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| исходная_строка |Yes |строка |Значение, в котором все экземпляры одной строки заменены другой строкой. |
| oldString |Yes |строка |Строка, которая удаляется из исходной строки. |
| newString |Yes |строка |Строка, добавляемая вместо удаляемой строки. |

### <a name="return-value"></a>Возвращаемое значение

Строка с замененными знаками.

### <a name="examples"></a>Примеры

В приведенном ниже [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) показано, как удалить все тире из предоставленной пользователем строки и как заменить часть строки другой строкой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| firstOutput | Строка, | 1231231234 |
| secondOutput | Строка, | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Возвращает строку, содержащую все знаки из исходной строки, начиная с заданной позиции, или массив, содержащий все элементы из исходного массива, начиная с заданной позиции.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |массив или строка |Массив или строка, используемые для пропуска. |
| numberToSkip |Yes |int |Число элементов или знаков, которые необходимо пропустить. Если это значение меньше или равно 0, то возвращаются все элементы или знаки в значении. Если он превышает длину массива или строки, возвращается пустой массив или строка. |

### <a name="return-value"></a>Возвращаемое значение

Массив или строка.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) пропускается заданное число элементов в массиве и заданное число знаков в строке.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| arrayOutput | Массив. | ["three"] |
| stringOutput | Строка, | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

Возвращает массив строк, содержащий подстроки входной строки, разделенные переданными разделителями.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| входная_строка |Yes |строка |Строка для разделения. |
| delimiter |Yes |строка или массив строк |Разделитель для разбиения строки. |

### <a name="return-value"></a>Возвращаемое значение

Массив строк.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) входная строка разбивается с помощью запятой или точки с запятой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| firstOutput | Массив. | ["one", "two", "three"] |
| secondOutput | Массив. | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Определяет, начинается ли строка с определенного значения. При сравнении регистр не учитывается.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |строка |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Yes |строка |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

**True**, если первый знак или знаки строки соответствуют значению. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) показано, как использовать функции startsWith и endsWith.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| startsTrue | Bool | True, |
| startsCapTrue | Bool | True, |
| startsFalse | Bool | Ложь |
| endsTrue | Bool | True, |
| endsCapTrue | Bool | True, |
| endsFalse | Bool | Ложь |

## <a name="string"></a>строка

`string(valueToConvert)`

Преобразует указанное значение в строку.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes | Любой |Значение, которое необходимо преобразовать в строку. Можно преобразовать любой тип значения, включая объекты и массивы. |

### <a name="return-value"></a>Возвращаемое значение

Строка преобразованного значения.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) показано, как преобразовать различные типы значений в строки.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| objectOutput | Строка, | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | Строка, | ["a","b","c"] |
| intOutput | Строка, | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Возвращает подстроку, которая начинается с указанной позиции и содержит указанное количество символов.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToParse |Yes |строка |Исходная строка, из которой извлекается подстрока. |
| startIndex |Нет |int |Отсчитываемая от нуля позиция первого знака для подстроки. |
| длина |Нет |int |Число символов в подстроке. Этот параметр должен ссылаться на позицию в строке. Значение этого параметра должно быть равным нулю или больше него. |

### <a name="return-value"></a>Возвращаемое значение

Подстрока. Также может принимать значение пустой строки, когда длина равна нулю.

### <a name="remarks"></a>Примечания

Если substring выходит за пределы строки или когда длина меньше нуля, происходит ошибка выполнения функции. Следующий пример завершается ошибкой "Параметры индекса и длины должны относиться к расположению в строке. Параметр индекса: 0, параметр длины: 11, параметр длины строкового параметра: 10".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) из параметра извлекается подстрока.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| substringOutput | Строка, | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Возвращает строку, содержащую указанное число знаков, считая от начала строки, или массив, содержащий указанное число элементов, считая от начала массива.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |массив или строка |Массив или строка, из которых берутся элементы. |
| numberToTake |Yes |int |Число элементов или знаков, которые необходимо взять. Если это значение меньше или равно 0, то возвращается пустой массив или строка. Если он превышает длину заданного массива или строки, возвращаются все элементы массива или строки. |

### <a name="return-value"></a>Возвращаемое значение

Массив или строка.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) из массива извлекается заданное число элементов, а из строки — заданное число знаков.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| arrayOutput | Массив. | ["one", "two"] |
| stringOutput | Строка, | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Преобразует указанную строку в нижний регистр.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| изменяемая_строка |Yes |строка |Значение, преобразовываемое в нижний регистр. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает строку, преобразованную в нижний регистр.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) значение параметра преобразуется в нижний и в верхний регистр.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| toLowerOutput | Строка, | one two three |
| toUpperOutput | Строка, | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Преобразует указанную строку в верхний регистр.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| изменяемая_строка |Yes |строка |Значение, преобразовываемое в верхний регистр. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает строку, преобразованную в верхний регистр.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) значение параметра преобразуется в нижний и в верхний регистр.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| toLowerOutput | Строка, | one two three |
| toUpperOutput | Строка, | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

Удаляет все начальные и конечные знаки пробела из указанной строки.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |строка |Обрезаемое значение. |

### <a name="return-value"></a>Возвращаемое значение

Строка без пробелов в начале и в конце.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) из параметра удаляются пробелы.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| return | Строка, | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Создает детерминированную хэш-строку на основании значений, указанных как параметры. 

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| baseString |Yes |строка |Значение, используемое в хэш-функции для создания уникальной строки. |
| Дополнительные параметры (если необходимы) |Нет |строка |Можно добавить столько строк, сколько необходимо для создания значения, которое задает уровень уникальности. |

### <a name="remarks"></a>Примечания

Эта функция полезна в тех случаях, когда необходимо создать уникальное имя ресурса. Указываются значения параметров, которые ограничивают область уникальности результата. Можно указать, является ли уникальным имя в подписке, группе ресурсов или развертывании. 

Возвращаемое значение не является случайной строкой, а результатом хэш-функции. Возвращаемое значение содержит 13 знаков. Он не является глобально уникальным. Вы можете добавить к значению префикс из своего соглашения об именовании, чтобы создать значимое имя. В следующем примере показан формат возвращаемого значения. Фактическое значение зависит от указанных параметров.

    tcvhiyu5h2o5o

В следующих примерах показывается, как использовать uniqueString при создании уникального значения для часто используемых уровней.

Уникальное в пределах подписки.

```json
"[uniqueString(subscription().subscriptionId)]"
```

Уникальное в пределах группы ресурсов.

```json
"[uniqueString(resourceGroup().id)]"
```

Уникальное в пределах развертывания для группы ресурсов.

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

В следующем примере показано, как создать уникальное имя учетной записи хранения на основе вашей группы ресурсов. Внутри группы ресурсов имя не является уникальным, если оно создано аналогичным образом.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Если необходимо создать новое уникальное имя каждый раз при развертывании шаблона и не обновлять ресурс, можно использовать функцию [UtcNow](#utcnow) с uniqueString. Этот подход можно использовать в тестовой среде. Пример см. в разделе [UtcNow](#utcnow).

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая 13 символов.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) возвращаются результаты выполнения uniquestring.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Создает абсолютный URI, объединяя строки baseUri и relativeUri.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |строка |Строка базового универсального кода ресурса (URI). Следите за поведением обработки замыкающей косой черты ('/'), как описано ниже в этой таблице.  |
| relativeUri |Yes |строка |Строка относительного универсального кода ресурса (URI), добавляемая к строке базового универсального кода ресурса (URI). |

* Если **baseUri** заканчивается в конце косой черты, результатом является просто **baseUri** , за которым следует значение **relativeUri**.

* Если **baseUri** не заканчивается в конце косой черты, происходит одно из двух действий.  

   * Если параметр **baseUri** вообще не имеет косой черты (кроме символа "//" рядом с передним), результатом является просто **baseUri** , за которым следует значение **relativeUri**.

   * Если **baseUri** имеет несколько косых черт, но не заканчивается косой чертой, все, начиная с последней косой черты, удаляется из **baseUri** , а результатом является **baseUri** , за которым следует значение **relativeUri**.
     
Ниже приведены некоторые примеры:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Для получения полных сведений параметры **baseUri** и **relativeUri** разрешаются, как указано в [RFC 3986, раздел 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Возвращаемое значение

Строка, представляющая абсолютный URI для базового и относительного значений.

### <a name="examples"></a>Примеры

В следующем примере показано создание ссылки на вложенный шаблон в зависимости от значения параметра родительского шаблона.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) показано, как использовать функции uri, uriComponent и uriComponentToString.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| uriOutput | Строка, | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Строка, | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Строка, | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Кодирует URI.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToEncode |Yes |строка |Значение для кодирования. |

### <a name="return-value"></a>Возвращаемое значение

Строка со значением, закодированным в формате URI.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) показано, как использовать функции uri, uriComponent и uriComponentToString.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| uriOutput | Строка, | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Строка, | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Строка, | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Возвращает строку со значением, закодированным в формате URI.

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Yes |строка |Значение, закодированное в формате URI, которое необходимо преобразовать в строку. |

### <a name="return-value"></a>Возвращаемое значение

Декодированная строка со значением, закодированным в формате URI.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) показано, как использовать функции uri, uriComponent и uriComponentToString.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| имя | введите | Значение |
| ---- | ---- | ----- |
| uriOutput | Строка, | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Строка, | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Строка, | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="utcnow"></a>utcnow

`utcNow(format)`

Возвращает текущее значение (UTC) DateTime в указанном формате. Если формат не указан, используется формат ISO 8601 (Ииииммддсхммссз). **Эта функция может использоваться только в значении по умолчанию для параметра.**

### <a name="parameters"></a>parameters

| Параметр | обязательные | введите | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| format |Нет |строка |Значение, закодированное в формате URI, которое необходимо преобразовать в строку. Используйте либо [стандартные строки формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) , либо [строки настраиваемого формата](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Примечания

Эту функцию можно использовать только в выражении для значения по умолчанию для параметра. Использование этой функции в любом месте шаблона возвращает ошибку. Функция не разрешена в других частях шаблона, поскольку она возвращает другое значение при каждом вызове. Развертывание одного и того же шаблона с теми же параметрами не будет уверенно давать одинаковые результаты.

Если вы используете [параметр для повторного развертывания предыдущего успешного развертывания](rollback-on-error.md), а предыдущее развертывание включает параметр, использующий UtcNow, параметр не вычисляется повторно. Вместо этого значение параметра из предыдущего развертывания автоматически используется повторно в развертывании отката.

Будьте внимательны при повторном развертывании шаблона, который использует функцию utcNow для значения по умолчанию. При повторном развертывании без предоставления значения для параметра функция вычисляется повторно. Если вы хотите обновить существующий ресурс, а не создать новый, передайте значение параметра из предыдущего развертывания.

### <a name="return-value"></a>Возвращаемое значение

Текущее значение даты и времени в формате UTC.

### <a name="examples"></a>Примеры

В следующем примере шаблона показаны различные форматы для значения DateTime.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Выходные данные предыдущего примера зависят от каждого развертывания, но будут выглядеть примерно так:

| имя | введите | Значение |
| ---- | ---- | ----- |
| уткаутпут | строка | 20190305T175318Z |
| уткшортаутпут | строка | 03/05/2019 |
| утккустомаутпут | строка | 3 5 |

В следующем примере показано, как использовать значение из функции при задании значения тега.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).

