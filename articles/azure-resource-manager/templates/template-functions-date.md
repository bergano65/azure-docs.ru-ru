---
title: Функции шаблона - дата
description: Описывает функции, которые можно использовать в шаблоне менеджера ресурсов Azure для работы с датами.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 364b41e9e92cb248a7bd2fac5a41eb535adbf440
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084793"
---
# <a name="date-functions-for-arm-templates"></a>Функции дат для шаблонов ARM

Диспетчер ресурсов предоставляет следующие функции для работы с датами в шаблонах Azure Resource Manager (ARM):

* [dateTimeAdd](#datetimeadd)
* [utcnow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Добавляет продолжительность времени к базовому значению. Ожидается формат ISO 8601.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| base | Да | строка | Значение даты начала для добавления. Используйте [формат Метки времени ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601) |
| длительность | Да | строка | Значение времени для добавления в базу. Это может быть отрицательное значение. Используйте [формат длительности ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
| format | нет | строка | Формат вывода для результата времени даты. Если не предусмотрено, используется формат базового значения. Используйте [строки стандартного формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) или [строки пользовательского формата.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) |

### <a name="return-value"></a>Возвращаемое значение

Значение времени даты, которое приводит к добавлению значения продолжительности к базовому значению.

### <a name="examples"></a>Примеры

В следующем примере шаблона отображается различные способы добавления значений времени.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

При развертывании предыдущего `2020-04-07 14:53:14Z`шаблона с базовым временем вывода:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| add3Years | Строка | 4/7/2023 2:53:14 PM |
| вычитание9дней | Строка | 3/29/2020 2:53:14 PM |
| add1Hour | Строка | 4/7/2020 3:53:14 PM |

В следующем примере шаблона показано, как установить время начала для графика автоматизации.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcnow

`utcNow(format)`

Возвращает текущее (UTC) значение времени даты в указанном формате. Если формат не предусмотрен, используется формат ISO 8601 (yyyMMddTHHMmss). **Эта функция может быть использована только в значении по умолчанию для параметра.**

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| format |нет |строка |Значение, закодированное в формате URI, которое необходимо преобразовать в строку. Используйте [строки стандартного формата](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) или [строки пользовательского формата.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) |

### <a name="remarks"></a>Remarks

Эту функцию можно использовать только в выражении для значения параметра по умолчанию. Использование этой функции в другом месте в шаблоне возвращает ошибку. Функция не допускается в других частях шаблона, поскольку она возвращает различное значение каждый раз, когда она называется. Развертывание одного и того же шаблона с теми же параметрами не даст точно такие же результаты.

Если вы используете [опцию для передислокации более раннего успешного развертывания,](rollback-on-error.md)а более раннее развертывание включает параметр, который использует utcNow, параметр не переоценивается. Вместо этого значение параметра от предыдущего развертывания автоматически используется в развертывании отката.

Будьте осторожны передислокации шаблона, который опирается на функцию utcNow для значения по умолчанию. При передислокации и не предоставлении значения для параметра функция переоценивается. Если требуется обновить существующий ресурс, а не создать новый, перейдите в значение параметра от предыдущего развертывания.

### <a name="return-value"></a>Возвращаемое значение

Текущее значение времени дат UTC.

### <a name="examples"></a>Примеры

В следующем примере шаблона отображают различные форматы для значения времени даты.

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

Выход из предыдущего примера варьируется для каждого развертывания, но будет аналогичен:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| utcOutput | строка | 20190305Т175318 |
| utcShortOutput | строка | 05.03.2019 |
| utcCustomOutput | строка | 3 5 |

В следующем примере показано, как использовать значение из функции при настройке значения тега.

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