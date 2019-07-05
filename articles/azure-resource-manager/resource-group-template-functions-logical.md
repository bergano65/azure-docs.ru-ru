---
title: Логические функции шаблона Azure Resource Manager | Документация Майкрософт
description: Описываются функции, используемые в шаблоне Azure Resource Manager для определения логических значений.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/15/2019
ms.author: tomfitz
ms.openlocfilehash: 2487cf928685423e4b60bb2923fc7e348eaff0c3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447973"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Логические функции для шаблонов Azure Resource Manager

Resource Manager предоставляет ряд функций для выполнения сравнений в шаблонах.

* [and](#and) (и);
* [bool](#bool);
* [if](#if) (если);
* [not](#not) (не);
* [or](#or)

## <a name="and"></a>и

`and(arg1, arg2, ...)`

Проверяет, соответствуют ли истине все значения параметров.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |boolean |Первое значение, которое необходимо проверить на соответствие истине. |
| arg2 |Да |boolean |Второе значение, которое необходимо проверить на соответствие истине. |
| дополнительные аргументы |Нет |boolean |Дополнительные аргументы для проверки соответствия истине. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если все значения соответствуют истине. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) показано, как использовать логические функции.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Преобразует параметр в логическое значение.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |строка или целое число |Значение, которое необходимо преобразовать в логическое. |

### <a name="return-value"></a>Возвращаемое значение
Логическое выражение преобразованного значения.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) показано, как использовать функцию bool со строкой или целым числом.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Возвращает значение в зависимости от того, выполняется ли условие.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| condition |Да |boolean |Значение, чтобы проверить, является ли значение true или false. |
| trueValue |Да | строка, целое число, объект или массив |Возвращаемое значение, если условие выполняется. |
| falseValue |Да | строка, целое число, объект или массив |Возвращаемое значение, если условие не выполняется. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает второй параметр, если первый параметр имеет значение **True** (Истина). В противном случае возвращает третий параметр.

### <a name="remarks"></a>Примечания

Если условие равно **True**, вычисляется только значение true. Если условие равно **False**, вычисляется значение false. С помощью **Если** функции, можно включить выражения, которые допустимы только по условию. Например можно ссылаться на ресурс, который существует одно условие, но не в других условиях. В следующем разделе показан пример условной оценки выражений.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) показано, как использовать функцию `if`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| yesOutput | String | Да |
| noOutput | String | Нет |
| objectOutput | Object | { "test": "value1" } |

Следующие [пример шаблона](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) показано, как использовать эту функцию с выражениями, которые допустимы только по условию.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

Преобразует логическое значение в противоположное ему значение.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |boolean |Значение, которое необходимо преобразовать. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает значение **True** (Истина), если параметр имеет значение **False** (Ложь). Возвращает значение **False** (Ложь), если параметр имеет значение **True** (Истина).

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) показано, как использовать логические функции.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) используется **not** и [equals](resource-group-template-functions-comparison.md#equals).

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
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>или

`or(arg1, arg2, ...)`

Проверяет, соответствует ли истине какое-либо значение параметров.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |boolean |Первое значение, которое необходимо проверить на соответствие истине. |
| arg2 |Да |boolean |Второе значение, которое необходимо проверить на соответствие истине. |
| дополнительные аргументы |Нет |boolean |Дополнительные аргументы для проверки соответствия истине. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если какое-либо значение соответствует истине. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) показано, как использовать логические функции.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Дальнейшие действия

* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).

