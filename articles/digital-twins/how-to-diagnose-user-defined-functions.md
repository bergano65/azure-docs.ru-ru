---
title: Отладка пользовательских функций в Azure Digital Twins | Документация Майкрософт
description: Рекомендации по отладке пользовательских функций в Azure Digital Twins
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: 455e78c63960103f5facae764aff3d2b3b2a590d
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735197"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Сведения о том, как выполнять отладку пользовательских функций в Azure Digital Twins

В этой статье перечислены способы диагностики и отладки определяемых пользователем функций в Двойниках цифровых Azure. Она также описывает некоторые наиболее распространенные сценарии, обнаруженные при отладке.

>[!TIP]
> Дополнительные сведения о настройке средств отладки в Azure Digital Twins с использованием журналов действий, журналов диагностики и Azure Monitor см. в разделе [Настройка мониторинга и ведения журнала](./how-to-configure-monitoring.md).

## <a name="debug-issues"></a>Отладка проблем

Нужно знать, как диагностировать проблемы в цифровой двойниками Azure позволяет эффективно анализировать проблемы, определять причины проблем и предоставить соответствующие решения для их.

С этой целью предоставляются разнообразные средства диагностики, ведение журнала и аналитики.

### <a name="enable-logging-for-your-instance"></a>Включение ведения журнала для своего экземпляра

Azure Digital Twins поддерживает надежные механизмы ведения журнала, мониторинга и аналитики. Разработчики решений могут использовать журналы, журналы диагностики, журналы действий и других служб Azure Monitor для поддержки сложных задач мониторинга приложения Интернета вещей. Можно сочетать несколько вариантов ведения журнала для запрашивания или отображения записей в нескольких службах и получения подробных сведений в журналах для многих служб.

* Конфигурацию ведения журнала, относящиеся к Azure цифровой Двойники, чтение [как настроить мониторинг и ведение журнала](./how-to-configure-monitoring.md).
* Обратитесь к [Azure Monitor](../azure-monitor/overview.md) обзором и узнайте о параметрах мощные журнала с помощью Azure Monitor.
* Ознакомьтесь со статьей [сбор и использование данных журнала из ресурсов Azure](../azure-monitor/platform/diagnostic-logs-overview.md) для настройки параметров журнала диагностики в Двойниках цифровых Azure через портал Azure, Azure CLI или PowerShell.

После настройки, вы сможете выбрать все категории журналов, метрики и использовать мощные рабочие области log analytics Azure Monitor для поддержки усилия при отладке.

### <a name="trace-sensor-telemetry"></a>Отслеживание телеметрии от датчика

Чтобы отследить телеметрию датчиков, убедитесь, что для экземпляра Azure Digital Twins включены диагностические параметры. Затем убедитесь в том, что выбраны все нужные категории журналов. Наконец убедитесь, что нужные журналы отправляются в журналы Azure Monitor.

Чтобы сопоставлять сообщения телеметрии от датчиков с соответствующими журналами, можно указать идентификатор корреляции в отправляемых данных событий. Для этого задайте для свойства `x-ms-client-request-id` значение GUID.

После отправки данных телеметрии, откройте log analytics для запроса журналов с помощью набора идентификатор корреляции:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Значение запроса | Заменить на |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Идентификатор корреляции, который был указан для данных событий |

Если включить ведение журнала для вашей пользовательской функции, эти журналы отображаются в свой экземпляр log analytics с категорией `UserDefinedFunction`. Чтобы получить их, введите следующее условие запроса в log analytics:

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

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Значение параметра | Заменить на |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | Идентификатор пользовательской функции, для которой нужно извлечь назначенные роли|

Если назначения роли не существует, обратитесь к статье [Создание назначения роли для пользовательской функции](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Проверка работы сопоставителя для используемых датчиков телеметрии

Следующий вызов API службы управления для экземпляров Azure Digital Twins позволяет определить, подходит ли выбранный сопоставитель для определенного датчика.

```plaintext
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

```plaintext
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

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о включении [журналов и мониторинга](./how-to-configure-monitoring.md) для Azure Digital Twins.

- Чтение [журнал действий Azure Обзор](../azure-monitor/platform/activity-logs-overview.md) статьи для Azure параметры ведения журнала.
