---
title: Мониторинг функций Azure с помощью журналов Azure Monitor
description: Узнайте, как использовать журналы Azure Monitor с помощью функций Azure для наблюдения за выполнением функций.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: a4fcf6b4dfeae3d8e53e083b2951d9d594c53d73
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966142"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Мониторинг функций Azure с помощью журналов Azure Monitor

Функции Azure предлагают интеграцию с [журналами Azure Monitor](../azure-monitor/platform/data-platform-logs.md) для мониторинга функций. В этой статье показано, как настроить функции Azure для отправки сформированных системой и созданных пользователем журналов в Azure Monitor журналы.

Журналы Azure Monitor дают возможность консолидировать журналы из разных ресурсов в одной рабочей области, где их можно анализировать с помощью [запросов](../azure-monitor/log-query/log-query-overview.md) для быстрого извлечения, консолидации и анализа собранных данных.  Вы можете создавать и тестировать запросы с помощью [Log Analytics](../azure-monitor/log-query/portals.md) на портале Azure, а затем либо напрямую анализировать данные с помощью этих средств, либо сохранять запросы для использования с [визуализациями](../azure-monitor/visualizations.md) или [правилами генерации оповещений](../azure-monitor/platform/alerts-overview.md).

В Azure Monitor используется версия [языка запросов Kusto](/azure/kusto/query/), который используется в Azure Data Explorer и совместим с простыми запросами к журналу, но также включает и расширенную функциональность (например, функции агрегирования, объединения и интеллектуальную аналитику). Быстро изучить язык запросов можно с помощью [множества уроков](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Интеграция с журналами Azure Monitor в настоящее время доступна в общедоступной предварительной версии для приложений-функций, работающих в планах использования Windows, Premium и на основе специальных планов размещения.

## <a name="setting-up"></a>Настройка

В разделе Мониторинг выберите **параметры диагностики** , а затем нажмите кнопку **Добавить**.

![Добавление параметра диагностики](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

На странице "Параметры" выберите **отправить log Analytics**и в разделе **Журнал** выберите **функтионапплогс**эта таблица содержит нужные журналы.

![Добавление параметра диагностики](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Журналы, созданные пользователем

Для создания пользовательских журналов можно использовать определенную инструкцию ведения журнала в зависимости от языка. ниже приведены примеры фрагментов кода.

**JavaScript**

```javascript
    context.log('My app logs here.');
```

**Python**

```python
    logging.info('My app logs here.')
```

**.NET**

```csharp
    log.LogInformation("My app logs here.");
```

**Java**

```java
    context.getLogger().info("My app logs here.");
```

**PowerShell**

```powershell
    Write-Host "My app logs here."
```

## <a name="querying-the-logs"></a>Запрос журналов

Чтобы запросить созданные журналы, перейдите в рабочую область log Analytics и щелкните **журналы**.

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
- Дополнительные сведения о [языке запросов](../azure-monitor/log-query/get-started-queries.md).
