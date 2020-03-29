---
title: Как отладить UDF - Azure Digital Twins Документы Майкрософт
description: Узнайте о рекомендуемых подходах к отладке пользовательских функций в Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511643"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Сведения о том, как выполнять отладку пользовательских функций в Azure Digital Twins

В этой статье кратко излагается, как диагностировать и отладить пользовательские функции в Azure Digital Twins. Она также описывает некоторые наиболее распространенные сценарии, обнаруженные при отладке.

>[!TIP]
> Дополнительные сведения о настройке средств отладки в Azure Digital Twins с использованием журналов действий, журналов диагностики и Azure Monitor см. в разделе [Настройка мониторинга и ведения журнала](./how-to-configure-monitoring.md).

## <a name="debug-issues"></a>Отладка проблем

Знание того, как диагностировать проблемы в Azure Digital Twins позволяет эффективно анализировать проблемы, выявлять причины проблем и предоставлять соответствующие решения для них.

С этой целью предоставляются различные средства ведения журнала, аналитики и диагностики.

### <a name="enable-logging-for-your-instance"></a>Включить журнал для вашего экземпляра

Azure Digital Twins поддерживает надежные механизмы ведения журнала, мониторинга и аналитики. Разработчики решений могут использовать журналы Azure Monitor, диагностические журналы, журналы активности и другие службы для поддержки сложных потребностей в мониторинге приложения IoT. Можно сочетать несколько вариантов ведения журнала для запрашивания или отображения записей в нескольких службах и получения подробных сведений в журналах для многих служб.

* Для конфигурации регистрации, специфичной для Azure Digital Twins, прочитайте [Как настроить мониторинг и журнал.](./how-to-configure-monitoring.md)
* Проконсультируйтесь с обзором [Azure Monitor,](../azure-monitor/overview.md) чтобы узнать о мощных настройках журнала, включенных через Azure Monitor.
* Просмотрите статью [«Сбор и потребление данных журналов из ресурсов Azure»](../azure-monitor/platform/platform-logs-overview.md) для настройки параметров диагностического журнала в Azure Digital Twins через портал Azure, Azure CLI или PowerShell.

После настройки вы сможете выбрать все категории журналов, метрики и использовать мощные рабочие области аналитики журналов Azure Monitor для поддержки усилий по отладке.

### <a name="trace-sensor-telemetry"></a>Отслеживание телеметрии от датчика

Чтобы отследить телеметрию датчиков, убедитесь, что для экземпляра Azure Digital Twins включены диагностические параметры. Затем убедитесь в том, что выбраны все нужные категории журналов. Наконец, подтвердите, что нужные журналы отправляются в журналы Azure Monitor.

Чтобы сопоставлять сообщения телеметрии от датчиков с соответствующими журналами, можно указать идентификатор корреляции в отправляемых данных событий. Для этого задайте для свойства `x-ms-client-request-id` значение GUID.

После отправки телеметрии откройте аналитику журналов Azure Monitor для запроса журналов с использованием установленного идентификатора корреляции:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Значение запроса | Заменить на |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Идентификатор корреляции, который был указан для данных событий |

Чтобы прочитать все последние телеметрии журналы запроса:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Если вы включите журнал для функции, определяемой пользователем, эти журналы отображаются в экземпляре аналитики журналов с категорией. `UserDefinedFunction` Чтобы получить их, введите следующее условие запроса в аналитике журнала:

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

**Неправильно** Примере:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Такой сценарий возникает, если используемый идентификатор ссылается на датчик, а для объекта топологии указан тип `Space`.

**Правильно** Примере:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Самый простой способ не допускать такой проблемы — использовать метод `Notify` для объекта метаданных.

Пример

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

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о включении [журналов и мониторинга](./how-to-configure-monitoring.md) для Azure Digital Twins.

- Прочитайте [статью «Обзор деятельности Azure»](../azure-monitor/platform/platform-logs-overview.md) для получения дополнительных вариантов регистрации Azure.
