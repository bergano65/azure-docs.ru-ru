---
title: Отладка пользовательских функций в Azure Digital двойников | Документация Майкрософт
description: Узнайте о рекомендуемых подходах к отладке определяемых пользователем функций в Azure Digital двойников.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: d362512ac6d06577a5c46bb0c6dab461f07ae709
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457022"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Сведения о том, как выполнять отладку пользовательских функций в Azure Digital Twins

В этой статье приведена сводка по диагностике и отладке определяемых пользователем функций в Azure Digital двойников. Она также описывает некоторые наиболее распространенные сценарии, обнаруженные при отладке.

>[!TIP]
> Дополнительные сведения о настройке средств отладки в Azure Digital Twins с использованием журналов действий, журналов диагностики и Azure Monitor см. в разделе [Настройка мониторинга и ведения журнала](./how-to-configure-monitoring.md).

## <a name="debug-issues"></a>Отладка проблем

Знание способов диагностики проблем в Azure Digital двойников позволяет эффективно анализировать проблемы, определять причины проблем и предоставлять соответствующие решения.

Для этой конечной точки предоставляются разнообразные средства ведения журналов, аналитики и диагностики.

### <a name="enable-logging-for-your-instance"></a>Включение ведения журнала для экземпляра

Azure Digital Twins поддерживает надежные механизмы ведения журнала, мониторинга и аналитики. Разработчики решений могут использовать журналы Azure Monitor, журналы диагностики, журналы действий и другие службы для поддержки сложных задач мониторинга приложения IoT. Можно сочетать несколько вариантов ведения журнала для запрашивания или отображения записей в нескольких службах и получения подробных сведений в журналах для многих служб.

* Сведения о настройке ведения журнала в Azure Digital двойников см. [в статье Настройка мониторинга и ведения журнала](./how-to-configure-monitoring.md).
* Дополнительные сведения о мощных параметрах журналов, включенных в Azure Monitor, см. в обзоре [Azure Monitor](../azure-monitor/overview.md) .
* Просмотрите статью [сбор и использование данных журналов из ресурсов Azure](../azure-monitor/platform/resource-logs-overview.md) для настройки параметров журналов диагностики в службе Digital двойников с помощью портал Azure, Azure CLI или PowerShell.

После настройки вы сможете выбрать все категории журналов, метрики и использовать мощные Azure Monitor рабочие области log Analytics для поддержки отладки.

### <a name="trace-sensor-telemetry"></a>Отслеживание телеметрии от датчика

Чтобы отследить телеметрию датчиков, убедитесь, что для экземпляра Azure Digital Twins включены диагностические параметры. Затем убедитесь в том, что выбраны все нужные категории журналов. Наконец, убедитесь, что нужные журналы отправляются в журналы Azure Monitor.

Чтобы сопоставлять сообщения телеметрии от датчиков с соответствующими журналами, можно указать идентификатор корреляции в отправляемых данных событий. Для этого задайте для свойства `x-ms-client-request-id` значение GUID.

После отправки данных телеметрии откройте Azure Monitor log Analytics, чтобы запросить журналы, используя идентификатор корреляции:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Значение запроса | Заменить на |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Идентификатор корреляции, который был указан для данных событий |

Чтобы просмотреть все недавние журналы телеметрии, выполните следующие действия.

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Если включить ведение журнала для определяемой пользователем функции, эти журналы будут отображаться в экземпляре log Analytics с категорией `UserDefinedFunction`. Чтобы получить их, введите следующее условие запроса в log Analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Дополнительные сведения о мощных операциях с запросами см. в статье [Приступая к работе с запросами](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Выявление стандартных проблем

Диагностика и выявление стандартных проблем одинаково важны для устранения неполадок, возникающих в решении. В следующих подразделах представлено несколько распространенных проблем, возникающих при разработке пользовательских функций.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Проверка создания назначения роли

Если в API управления не создано назначение роли, пользовательская функция не сможет использовать в топологии многие действия, например отправку уведомлений, получение метаданных и установку вычисляемых значений.

Проверьте, существует ли назначенная роль для пользовательской функции, используя API управления:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Значение параметра | Заменить на |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | Идентификатор пользовательской функции, для которой нужно извлечь назначенные роли|

Если назначения роли не существует, обратитесь к статье [Создание назначения роли для пользовательской функции](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Проверка работы сопоставителя для используемых датчиков телеметрии

Следующий вызов API службы управления для экземпляров Azure Digital Twins позволяет определить, подходит ли выбранный сопоставитель для определенного датчика.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | Идентификатор оцениваемого сопоставителя |
| *YOUR_SENSOR_IDENTIFIER* | Идентификатор оцениваемого датчика |

Ответ:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Проверка функции, запускаемой датчиком

Следующий вызов к API управления в Azure Digital Twins позволяет определить идентификаторы пользовательских функций, запускаемых при поступлении телеметрии от этого датчика:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Параметр | Заменить на |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | Идентификатор датчика для отправки телеметрии |

Ответ:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Проблема с получением уведомлений

Если от активированной пользовательской функции не поступают уведомления, убедитесь, что параметр типа в вашей топологии совпадает с типом используемого идентификатора.

**Неправильный** пример:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Такой сценарий возникает, если используемый идентификатор ссылается на датчик, а для объекта топологии указан тип `Space`.

**Правильный** пример:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Самый простой способ не допускать такой проблемы — использовать метод `Notify` для объекта метаданных.

Пример:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Общие исключения диагностики

Включив параметры диагностики, вы можете столкнуться со следующими исключениями общего характера.

1. **Регулирование**. Если пользовательская функция превышает ограничения по скорости выполнения, которые описаны в статье [Ограничения службы](./concepts-service-limits.md), к ней применяется регулирование. Дальнейшие операции не будут успешно выполнены, пока не будет достигнут предел регулирования.

1. **Данные не найдены**. Если пользовательская функция пытается получить доступ к несуществующим метаданным, такая операция завершится ошибкой.

1. **Нет прав**. Если у пользовательской функции нет назначения роли или достаточных разрешений для доступа к некоторым метаданным топологии, такая операция завершится ошибкой.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о включении [журналов и мониторинга](./how-to-configure-monitoring.md) для Azure Digital Twins.

- Дополнительные сведения о параметрах ведения журнала Azure см. в статье [Обзор журнала действий Azure](../azure-monitor/platform/activity-logs-overview.md) .
