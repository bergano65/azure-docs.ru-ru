---
title: Мониторинг функций Azure с помощью журналов Azure Monitor
description: Узнайте, как использовать журналы Azure Monitor с помощью функций Azure для наблюдения за выполнением функций.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: f4af646569edc8a9274af752e7e4f2a36585ae4d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769104"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Мониторинг функций Azure с помощью журналов Azure Monitor

Функции Azure предлагают интеграцию с [журналами Azure Monitor](../azure-monitor/platform/data-platform-logs.md) для мониторинга функций. В этой статье показано, как настроить функции Azure для отправки сформированных системой и созданных пользователем журналов в Azure Monitor журналы.

Журналы Azure Monitor дают возможность консолидировать журналы из разных ресурсов в одной рабочей области, где их можно анализировать с помощью [запросов](../azure-monitor/log-query/log-query-overview.md) для быстрого извлечения, консолидации и анализа собранных данных.  Вы можете создавать и тестировать запросы с помощью [Log Analytics](../azure-monitor/log-query/portals.md) на портале Azure, а затем либо напрямую анализировать данные с помощью этих средств, либо сохранять запросы для использования с [визуализациями](../azure-monitor/visualizations.md) или [правилами генерации оповещений](../azure-monitor/platform/alerts-overview.md).

Azure Monitor использует версию [языка запросов Kusto](/azure/kusto/query/), который применяется в Azure Data Explorer. Он позволяет выполнять простые запросы к журналам, но также включает и расширенную функциональность, например функции агрегирования, объединения и интеллектуальную аналитику. Доступно [множество уроков](../azure-monitor/log-query/get-started-queries.md) для быстрого изучения этого языка.

> [!NOTE]
> Интеграция с журналами Azure Monitor в настоящее время доступна в общедоступной предварительной версии для приложений-функций, работающих в планах использования Windows, Premium и на основе специальных планов размещения.

## <a name="setting-up"></a>Настройка

В разделе **мониторинг** выберите **параметры диагностики** , а затем щелкните **Добавить параметр диагностики**.

![Добавление параметра диагностики](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

На странице **параметры диагностики** выберите **Отправить log Analytics**, а затем выберите свою рабочую область log Analytics. В разделе **Журнал** выберите **функтионапплогс**эта таблица содержит нужные журналы.

![Добавление параметра диагностики](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Журналы, созданные пользователем

Для создания пользовательских журналов можно использовать определенную инструкцию ведения журнала в зависимости от языка. ниже приведены примеры фрагментов кода.


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Запрос журналов

Чтобы запросить созданные журналы, перейдите в рабочую область Log Analytics, в которой вы настроили отправку журналов функций, и щелкните **журналы**.

![Окно запроса в LA Workspace](media/functions-monitor-log-analytics/querying.png)

Функции Azure записывают все журналы в таблицу **функтионапплогс** . Вот несколько примеров запросов.

### <a name="all-logs"></a>Все журналы

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Журналы конкретных функций

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

- [Обзор функций Azure](functions-overview.md)
- Дополнительные сведения о [журналах Azure Monitor](../azure-monitor/platform/data-platform-logs.md)
- Узнайте больше о [языке запросов](../azure-monitor/log-query/get-started-queries.md).
