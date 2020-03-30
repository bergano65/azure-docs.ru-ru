---
title: Мониторинг функций Azure с помощью журналов Azure Monitor
description: Узнайте, как использовать журналы мониторинга Azure с функциями Azure для мониторинга выполнения функций.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649880"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Мониторинг функций Azure с помощью журналов Azure Monitor

Функции Azure предлагают интеграцию с [журналами Azure Monitor](../azure-monitor/platform/data-platform-logs.md) для мониторинга функций. В этой статье показано, как настроить функции Azure для отправки журналов, генерируемых системой и пользователем, в журналы Azure Monitor.

Журналы Azure Monitor позволяют консолидировать журналы из разных ресурсов в одном рабочем пространстве, где их можно анализировать с [помощью запросов](../azure-monitor/log-query/log-query-overview.md) для быстрого извлечения, консолидации и анализа собранных данных.  Вы можете создавать и тестировать запросы с помощью [Log Analytics](../azure-monitor/log-query/portals.md) на портале Azure, а затем либо напрямую анализировать данные с помощью этих средств, либо сохранять запросы для использования с [визуализациями](../azure-monitor/visualizations.md) или [правилами генерации оповещений](../azure-monitor/platform/alerts-overview.md).

Azure Monitor использует версию [языка запросов Kusto](/azure/kusto/query/), который применяется в Azure Data Explorer. Он позволяет выполнять простые запросы к журналам, но также включает и расширенную функциональность, например функции агрегирования, объединения и интеллектуальную аналитику. Доступно [множество уроков](../azure-monitor/log-query/get-started-queries.md) для быстрого изучения этого языка.

> [!NOTE]
> Интеграция с журналами Azure Monitor в настоящее время находится в открытом доступе для функциональных приложений, работающих на windows Consumption, Premium и Dedicated hosting plans.

## <a name="setting-up"></a>Настройка

Из раздела **Мониторинг** выберите **настройки диагностики,** а затем нажмите **Добавить диагностическую настройку.**

![Добавление диагностической настройки](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

На странице **настройки диагностики** выберите **«Отправить в журнал Analytics»,** а затем выберите рабочее пространство «Аналитика журнала». Под **журналом** выберите **FunctionAppLogs,** эта таблица содержит нужные журналы.

![Добавление диагностической настройки](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Созданные пользователями журналы

Для создания пользовательских журналов можно использовать конкретную выписку журнала в зависимости от языка, вот фрагменты кода образца:


# <a name="c"></a>[C #](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Запрос журналов

Чтобы задать запрос генерируемым журналам, перейдите в рабочее пространство Log Analytics, настроенное для отправки журналов функций и нажмите **«Журналы».**

![Окно запроса в рабочей области Лос-Анджелеса](media/functions-monitor-log-analytics/querying.png)

Azure Functions записывает все журналы в таблицу **FunctionAppLogs,** вот некоторые примеры запросов.

### <a name="all-logs"></a>Все журналы

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Определенные журналы функций

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Исключения

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Дальнейшие действия

- Обзор [обзора функций Azure](functions-overview.md)
- Узнайте больше о [журналах мониторинга Azure](../azure-monitor/platform/data-platform-logs.md)
- Подробнее о [языке запроса.](../azure-monitor/log-query/get-started-queries.md)
