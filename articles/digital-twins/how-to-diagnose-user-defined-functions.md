---
title: Отладка пользовательских функций в Azure Digital Twins | Документация Майкрософт
description: Рекомендации по отладке пользовательских функций в Azure Digital Twins
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: stefanmsft
ms.openlocfilehash: 852b2d35ae605f5529d162d52655fd258ca07c5a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946102"
---
# <a name="how-to-debug-issues-with-user-defined-functions-in-azure-digital-twins"></a>Отладка проблем с пользовательскими функциями в Azure Digital Twins

В этой статье описаны способы диагностики пользовательских функций. Также она описывает некоторые наиболее распространенных сценарии, которые возникают при работе с ними.

## <a name="debug-issues"></a>Отладка проблем

Умение диагностировать ошибки, возникающие в экземпляре Azure Digital Twins, поможет вам эффективно определять суть проблемы, ее причину и решение.

### <a name="enable-log-analytics-for-your-instance"></a>Включение Log Analytics для используемого экземпляра

Журналы и метрики для экземпляра Azure Digital Twins предоставляются через Azure Monitor. В этой документации предполагается, что вы уже создали рабочую область [Azure Log Analytics](../log-analytics/log-analytics-queries.md) с помощью [портала Azure](../log-analytics/log-analytics-quick-create-workspace.md), [Azure CLI](../log-analytics/log-analytics-quick-create-workspace-cli.md) или [ PowerShell](../log-analytics/log-analytics-quick-create-workspace-posh.md).

> [!NOTE]
> При первой отправке событий в **Log Analytics** возможна задержка до 5 минут.

В статье [Сбор и использование данных журнала из ресурсов Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) описано, как включить параметры диагностики для экземпляра Azure Digital Twins с помощью портала, Azure CLI или PowerShell. Не забудьте выбрать все категории журналов, метрики и рабочую область Azure Log Analytics.

### <a name="trace-sensor-telemetry"></a>Отслеживание телеметрии от датчика

Убедитесь, что для экземпляра Digital Twins включены параметры диагностики, выбраны все категории журналов, и эти журналы успешно отправляются в Azure Log Analytics.

Чтобы сопоставлять сообщения телеметрии от датчиков с соответствующими журналами, можно указать идентификатор корреляции в отправляемых данных событий. Для этого задайте для свойства `x-ms-client-request-id` значение GUID.

После отправки данных телеметрии откройте Azure Log Analytics, чтобы выполнять запросы к журналам по указанному идентификатору корреляции:

```Kusto
AzureDiagnostics
| where CorrelationId = 'yourCorrelationIdentifier'
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourCorrelationIdentifier* | Идентификатор корреляции, который был указан для данных событий |

Если журналы создаются для пользовательской функции, они будут отображаться в вашем экземпляре Azure Log Analytics под категорией `UserDefinedFunction`. Чтобы получить их, введите следующее условие запроса в Azure Log Analytics:

```Kusto
AzureDiagnostics
| where Category = 'UserDefinedFunction'
```

Дополнительные сведения о мощных операциях с запросами см. в статье [о начале работы с запросами](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="identify-common-issues"></a>Выявление стандартных проблем

Диагностика и выявление стандартных проблем одинаково важны для устранения неполадок, возникающих в решении. Ниже представлены несколько распространенных проблем, возникающих при разработке пользовательских функций.

### <a name="ensure-a-role-assignment-was-created"></a>Проверка создания назначения роли

Если в API управления не создано назначение роли, пользовательская функция не сможет использовать в топологии многие действия, например отправку уведомлений, получение метаданных и установку вычисляемых значений.

Проверьте, существует ли назначенная роль для пользовательской функции, используя API управления:

```plaintext
GET https://yourManagementApiUrl/api/v1.0/roleassignments?path=/&traverse=Down&objectId=yourUserDefinedFunctionId
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourManagementApiUrl* | Полный URL-путь к нужному API управления  |
| *yourUserDefinedFunctionId* | Идентификатор пользовательской функции, для которой нужно извлечь назначенные роли|

Если назначения ролей извлечь не удается, выполните инструкции из статьи [Использование пользовательских функций в Azure Digital Twins](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>Проверка работы сопоставителя для используемых датчиков телеметрии

Следующий вызов API службы управления для экземпляров Azure Digital Twins позволяет определить, подходит ли выбранный сопоставитель для определенного датчика.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/matchers/yourMatcherIdentifier/evaluate/yourSensorIdentifier?enableLogging=true
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourManagementApiUrl* | Полный URL-путь к нужному API управления  |
| *yourMatcherIdentifier* | Идентификатор оцениваемого сопоставителя |
| *yourSensorIdentifier* | Идентификатор оцениваемого датчика |

Ответ:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-will-trigger"></a>Проверка функции, запускаемой датчиком

Следующий вызов к API управления экземпляров Azure Digital Twins позволяет определить идентификаторы пользовательских функций, которые будут вызываться при поступлении телеметрии от этого датчика:

```plaintext
GET https://yourManagementApiUrl/api/v1.0/sensors/yourSensorIdentifier/matchers?includes=UserDefinedFunctions
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourManagementApiUrl* | Полный URL-путь к нужному API управления  |
| *yourSensorIdentifier* | Идентификатор датчика, который будет отправлять данные телеметрии |

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

Если от активированной пользовательской функции не поступают уведомления, убедитесь, что параметра типа в вашей топологии совпадает с типом используемого идентификатора.

**Неправильный** пример:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Такой сценарий возникает, если используемый идентификатор ссылается на датчик, а для объекта топологии указан тип "Место".

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

1. **Регулирование**. Если пользовательская функция превышает ограничения по скорости выполнения, которые описаны в статье [Ограничения службы](./concepts-service-limits.md), к ней применяется регулирование. Это означает, что последующие операции не будут успешно выполняться, пока не истечет срок действия ограничения.

1. **Данные не найдены**. Если пользовательская функция пытается получить доступ к несуществующим метаданным, такая операция завершится ошибкой.

1. **Нет прав**. Если у пользовательской функции нет назначения роли или достаточных разрешений для доступа к некоторым метаданным топологии, такая операция завершится ошибкой.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о включении [журналов и мониторинга](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) для Azure Digital Twins.
