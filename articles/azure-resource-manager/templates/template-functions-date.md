---
title: Функции шаблонов — Дата
description: Описывает функции, используемые в шаблоне Azure Resource Manager для работы с датами.
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: abdc88ce15279b90f8f9dc05a38a2ae236498f12
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058050"
---
# <a name="date-functions-for-arm-templates"></a>Функции даты для шаблонов ARM

Диспетчер ресурсов предоставляет следующие функции для работы с датами в шаблонах Azure Resource Manager (ARM):

* [датетимеадд](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>датетимеадд

`dateTimeAdd(base, duration, [format])`

Добавляет длительность времени к базовому значению. Ожидался формат ISO 8601.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| base | Да | string | Начальное значение даты и времени для сложения. Используйте [Формат метки времени ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| длительность | Да | string | Значение времени, которое необходимо добавить в базу. Это может быть отрицательное значение. Используйте [формат длительности ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Нет | строка | Формат выходных данных для результата даты и времени. Если не указано, используется формат базового значения. Используйте либо [стандартные строки формата](/dotnet/standard/base-types/standard-date-and-time-format-strings) , либо [строки настраиваемого формата](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Возвращаемое значение

Значение DateTime, полученное в результате добавления значения длительности в базовое значение.

### <a name="examples"></a>Примеры

В следующем примере шаблона показаны различные способы добавления значений времени.

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

При развертывании предыдущего шаблона с базовым временем `2020-04-07 14:53:14Z` выводится следующее:

| Имя | Type | Значение |
| ---- | ---- | ----- |
| add3Years | Строка | 4/7/2023 2:53:14 РМ |
| subtract9Days | Строка | 3/29/2020 2:53:14 РМ |
| add1Hour | Строка | 4/7/2020 3:53:14 РМ |

В следующем примере шаблона показано, как задать время начала для расписания автоматизации.

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

Возвращает текущее значение (UTC) DateTime в указанном формате. Если формат не указан, используется формат ISO 8601 (Ииииммддсхммссз). **Эта функция может использоваться только в значении по умолчанию для параметра.**

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | Описание: |
|:--- |:--- |:--- |:--- |
| format |Нет |строка |Значение, закодированное в формате URI, которое необходимо преобразовать в строку. Используйте либо [стандартные строки формата](/dotnet/standard/base-types/standard-date-and-time-format-strings) , либо [строки настраиваемого формата](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Комментарии

Эту функцию можно использовать только в выражении для значения по умолчанию для параметра. Использование этой функции в любом месте шаблона возвращает ошибку. Функция не разрешена в других частях шаблона, поскольку она возвращает другое значение при каждом вызове. Развертывание одного и того же шаблона с теми же параметрами не будет уверенно давать одинаковые результаты.

Если вы используете [параметр для отката при ошибке](rollback-on-error.md) до предыдущего успешного развертывания, а предыдущее развертывание включает параметр, использующий UtcNow, параметр не вычисляется повторно. Вместо этого значение параметра из предыдущего развертывания автоматически используется повторно в развертывании отката.

Будьте внимательны при повторном развертывании шаблона, который использует функцию utcNow для значения по умолчанию. При повторном развертывании без предоставления значения для параметра функция вычисляется повторно. Если вы хотите обновить существующий ресурс, а не создать новый, передайте значение параметра из предыдущего развертывания.

### <a name="return-value"></a>Возвращаемое значение

Текущее значение даты и времени в формате UTC.

### <a name="examples"></a>Примеры

В следующем примере шаблона показаны различные форматы для значения DateTime.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Имя | Type | Значение |
| ---- | ---- | ----- |
| уткаутпут | string | 20190305T175318Z |
| уткшортаутпут | string | 05.03.2019 |
| утккустомаутпут | string | 3 5 |

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

## <a name="next-steps"></a>Дальнейшие шаги

* Описание разделов в шаблоне Azure Resource Manager см. [в разделе Общие сведения о структуре и синтаксисе шаблонов ARM](template-syntax.md).
