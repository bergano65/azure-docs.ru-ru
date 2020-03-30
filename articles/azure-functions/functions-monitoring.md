---
title: Мониторинг Функций Azure
description: Узнайте, как использовать Azure Application Insights с функциями Azure для мониторинга выполнения функций.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 08da17f1ef023676aa0c499cf4e7e1bb9687f1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257859"
---
# <a name="monitor-azure-functions"></a>Мониторинг Функций Azure

[Azure Functions](functions-overview.md) предлагает встроенную интеграцию с [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) для мониторинга функций. В этой статье показано, как настроить функции Azure для отправки системных файлов журналов в Application Insights.

Мы рекомендуем использовать Application Insights, поскольку он собирает данные о журнале, производительности и ошибках. Он автоматически обнаруживает аномалии производительности и включает в себя мощные инструменты аналитики, которые помогут вам диагностировать проблемы и понять, как используются ваши функции. Эта служба помогает постоянно улучшать производительность и удобство использования. Вы даже можете использовать Application Insights во время разработки локальных функциональных приложений. Для получения дополнительной информации, [см. Что такое application Insights?](../azure-monitor/app/app-insights-overview.md).

Поскольку необходимые приборы Insights приложения встроены в функции Azure, все, что вам нужно, это действительный ключ приборов для подключения приложения функции к ресурсу Application Insights. Ключ приборов следует добавлять в настройки приложения при создании ресурса приложения функции в Azure. Если в приложении функции еще нет этого ключа, его можно [установить вручную.](#enable-application-insights-integration)  

## <a name="application-insights-pricing-and-limits"></a>Стоимость и ограничения Application Insights

Вы можете протестировать интеграцию Application Insights с приложением-функцией бесплатно. Существует ежедневный лимит на то, сколько данных может быть обработано бесплатно. Вы можете достичь этого предела во время тестирования. Azure отправляет уведомления на портале и по электронной почте при приближении к ежедневному ограничению. Если вы пропустили эти предупреждения и достигли предела, новые журналы не будут отображаться в запросах Application Insights. Будьте в курсе лимита, чтобы избежать ненужного времени устранения неполадок. Дополнительные сведения см. в статье [Управление ценами и объемом данных в Application Insights](../azure-monitor/app/pricing.md).

Полный список функций Application Insights, доступных для вашего приложения функции подробно описаны в [приложениях Insights для функций Azure поддерживается.](../azure-monitor/app/azure-functions-supported-features.md)

## <a name="view-telemetry-in-monitor-tab"></a>Просмотр телеметрии на вкладке "Монитор"

С [включенной интеграцией Application Insights](#enable-application-insights-integration)можно просматривать данные телеметрии во вкладке **Monitor.**

1. На странице приложения функции выберите функцию, которая работала по крайней мере один раз после настройки Application Insights. Затем выберите вкладку **Monitor.** Выберите **Периодически обновлять,** пока не появится список вызовов функций.

   ![Список вызовов](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Появление списка может занять до пяти минут, в то время как клиент телеметрии расставляет данные для передачи на сервер. Задержка не распространяется на [Live Metrics Stream.](../azure-monitor/app/live-stream.md) Эта служба подключается к хосту Функции при загрузке страницы, поэтому журналы передаются непосредственно на страницу.

1. Чтобы просмотреть журналы для вызова конкретной функции, выберите ссылку столбца **Дата (UTC)** для этого вызова. Выходные данные ведения журнала для этого вызова отображаются на новой странице.

   ![Сведения о вызове](media/functions-monitoring/invocation-details-ai.png)

1. Выберите ссылку **Run in Application Insights** для просмотра источника запроса, который получает данные журнала Azure Monitor в журнале Azure, если это первый раз, когда вы используете аналитику журналов Azure в вашей подписке, вам предлагается включить его.

1. При выборе этой ссылки и выбора включения log Analytic. отображается следующий запрос. Вы можете видеть, что результаты запроса ограничены`where timestamp > ago(30d)`за последние 30 дней (). Кроме того, результаты показывают не более`take 20`20 строк (). В отличие от этого, список сведений вызова для вашей функции за последние 30 дней без ограничений.

   ![Список вызовов в аналитике Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Дополнительные сведения см. в разделе [Запросы к данным телеметрии](#query-telemetry-data) далее в этой статье.

## <a name="view-telemetry-in-application-insights"></a>Просмотр данных телеметрии в Application Insights

Чтобы открыть приложение Insights из приложения функции на портале Azure, перейдите на страницу **обзор** приложения функции. Под **настраиваемыми функциями**выберите **Application Insights.**

![Открытые исследования приложений со страницы обзор приложения функции](media/functions-monitoring/ai-link.png)

Дополнительные сведения об использовании Application Insights см. в [документации по Application Insights](https://docs.microsoft.com/azure/application-insights/). В этом разделе представлено несколько примеров просмотра данных в Application Insights. Если вы уже знакомы с Application Insights, вы можете перейти непосредственно к разделам о том, [как настроить и настроить данные телеметрии.](#configure-categories-and-log-levels)

![Вкладка «Обзор информации о приложениях»](media/functions-monitoring/metrics-explorer.png)

Следующие области Application Insights могут быть полезны при оценке поведения, производительности и ошибок в функциях:

| Изучение | Описание |
| ---- | ----------- |
| **[Сбоев](../azure-monitor/app/asp-net-exceptions.md)** |  Создавайте диаграммы и оповещения на основе сбоев функций и исключений серверов. **Имя операции** обозначает имя функции. Сбои в зависимостях не отображаются, если только вы не внедрите пользовательскую телеметрию для зависимостей. |
| **[Производительность](../azure-monitor/app/performance-counters.md)** | Проанализируйте проблемы производительности, просматривая использование ресурсов и пропускную способность в **экземплярах роли облака.** Эти данные можно использовать для отладки в тех случаях, когда функции создают чрезмерную нагрузку на базовые ресурсы. |
| **[Метрики](../azure-monitor/app/metrics-explorer.md)** | Создавайте диаграммы и оповещения, основанные на метриках. Метрика включает количество вызовов функций, время выполнения и показатели успешности. |
| **[Прямая метрика](../azure-monitor/app/live-stream.md)** | Просматривайте данные метрик по мере их создания практически в режиме реального времени. |

## <a name="query-telemetry-data"></a>Запросы к данным телеметрии

[Application Insights Analytics](../azure-monitor/app/analytics.md) предоставляет вам доступ ко всем телеметрическим данным в виде таблиц в базе данных. Функция аналитики поддерживает язык запросов для извлечения, обработки и визуализации данных. 

Выберите **журналы** для изучения или запроса для зарегистрированных событий.

![Пример данных аналитики](media/functions-monitoring/analytics-traces.png)

В этом примере показано распределение запросов по рабочим ролям за последние 30 минут.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Доступные таблицы отображаются во вкладке **Schema** слева. Данные, создаваемые при вызовах функций, вы найдете в следующих таблицах:

| Таблица | Описание |
| ----- | ----------- |
| **Следы** | Логи, созданные временем выполнения и кодом функций. |
| **запросы** | Один запрос для каждого вызова функции. |
| **Исключения** | Любые исключения, брошенные временем выполнения. |
| **customMetrics** | Подсчет успешных и неудачных вызовов, коэффициент успеха и продолжительность. |
| **customEvents** | События, отслеживаемые временем выполнения, например: запросы HTTP, которые вызывают функцию. |
| **производительностьКонтры** | Информация о производительности серверов, на которые работают функции. |

Другие таблицы для тестов доступности, а также телеметрии клиента и браузера. Вы можете реализовать пользовательскую телеметрию, чтобы добавлять в них данные.

В каждой таблице есть поле `customDimensions`, где хранится часть данных о конкретной функции.  Например, следующий запрос получает все трассировки с уровнем журнала `Error`.

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Время выполнения обеспечивает `customDimensions.LogLevel` `customDimensions.Category` и поля. Вы можете предоставить дополнительные поля в журналах, которые вы пишете в коде функции. См. раздел [Структурированное ведение журнала](#structured-logging) далее в этой статье.

## <a name="configure-categories-and-log-levels"></a>Настройка категорий и уровней ведения журнала

Вы можете использовать Application Insights без какой-либо пользовательской конфигурации. Конфигурация по умолчанию может привести к большим объемам данных. Если вы используете подписку Azure для Visual Studio, объем данных Application Insights может достигнуть установленного верхнего предела. Позже в этой статье вы узнаете, как настроить и настроить данные, которые ваши функции отправляют в Application Insights. Для приложения функции журнал ирование настроено в файле [host.json.]

### <a name="categories"></a>Категории

В средстве ведения журнала Функций Azure предусмотрена *категория* для каждого журнала. Категория указывает, какая часть кода среды выполнения или кода функции записывала данные в этот журнал. На следующем графике описаны основные категории журналов, которые создает время выполнения. 

| Категория | Описание |
| ----- | ----- | 
| Категория Host.Results | Эти журналы отображаемы как **запросы** в Application Insights. Они содержат сведения об успешном выполнении или сбое функций. Все эти журналы написаны на `Information` уровне. Если вы `Warning` фильтруете на уровне или выше, вы не увидите ни одного из этих данных. |
| Host.Агрегатор | Эти журналы содержат счетчики и средние значения по вызовам функций за [настраиваемый](#configure-the-aggregator) период времени. По умолчанию используется период 30 секунд или 1000 результатов в зависимости от того, что из этого наступит раньше. Журналы доступны в таблице **customMetrics** в Application Insights. Примерами являются количество запусков, частота успешности и продолжительность. Все эти журналы написаны на `Information` уровне. Если вы `Warning` фильтруете на уровне или выше, вы не увидите ни одного из этих данных. |

Все журналы для других категорий доступны в таблице **следов** в Application Insights.

Все журналы с категориями, которые начинаются с, `Host` пишутся временем выполнения функций. **Функция запущена** и **функция завершенных** журналов имеют категорию `Host.Executor`. Для успешного запуска эти `Information` журналы являются уровнями. Исключения регистрируются `Error` на уровне. Кроме того, среда выполнения создает журналы уровня `Warning`, например очередь сообщений, отправленных в очередь подозрительных сообщений.

Время выполнения функций создает журналы с категорией, которая начинается с "Host". В версии `function started`1.x, , `function executed`и `function completed` журналы имеют категорию `Host.Executor`. Начиная с версии 2.x, эти `Function.<YOUR_FUNCTION_NAME>`журналы имеют категорию.

Если вы пишете журналы в коде `Function.<YOUR_FUNCTION_NAME>.User` функции, категория есть и может быть любой уровень журнала. В версии 1.x времени выполнения функций категория находится `Function`в режиме .

### <a name="log-levels"></a>Уровни журнала

Регистратор функций Azure также включает *уровень журнала* с каждым журналом. Параметр [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) (Уровень ведения журнала) является перечислением целочисленных значений, которые обозначают относительную важность.

|LogLevel    |Код|
|------------|---|
|Трассировка       | 0 |
|Отладка       | 1 |
|Сведения | 2 |
|Предупреждение     | 3 |
|Error       | 4 |
|Critical    | 5 |
|None        | 6 |

Уровень ведения журнала `None` описан в следующем разделе. 

### <a name="log-configuration-in-hostjson"></a>Конфигурация журнала в host.json

Файл [host.json] определяет, какой объем информации приложение-функция отправляет в журнал Application Insights. В каждой категории вы можете указать минимальный уровень ведения журнала для отправки данных. Примеров есть два: первый пример нацелен [на версию 2.x, а затем](functions-versions.md#version-2x) время выполнения функций (с ядром .NET), а второй пример — для версии 1.x.

### <a name="version-2x-and-higher"></a>Версия 2.x и выше

Версия v2.x и более поздние версии времени выполнения функций используют [иерархию фильтров для регистрации .NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Версия 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

В этом примере настраиваются следующие правила:

* Для журналов `Host.Results` с `Function`категорией `Error` или , отправить только уровень и выше, чтобы Приложение Исследования. Данные журналов с уровнем `Warning` и ниже игнорируются.
* Все журналы с категорией `Host.Aggregator` отправляются в Application Insights. Уровень ведения журнала `Trace` — это то же, что в некоторых средствах ведения журналов именуется как `Verbose`. В файле [host.json] необходимо использовать `Trace`.
* Для всех остальных журналов в Application Insights отправляются данные с уровнем `Information` и выше.

Значение категории в [host.json] управляет ведением журнала для всех категорий, название которых начинается с аналогичного значения. `Host`в [host.json] контролирует `Host.General` `Host.Executor`журнал `Host.Results`для , , и так далее.

Если [host.json] содержит несколько категорий с одинаковым началом строки, сопоставление начинается с более длинных строк. Предположим, что вы хотите `Host.Aggregator` все, `Error` начиная от `Host.Aggregator` времени выполнения, `Information` кроме входа на уровне, но вы хотите войти на уровне:

### <a name="version-2x-and-later"></a>Версия 2.x и более поздняя

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Версия 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Чтобы не вести журналы для определенной категории, установите для нее уровень ведения журнала `None`. Нет журналы написаны с этой категорией и нет уровня журнала над ним.

## <a name="configure-the-aggregator"></a>Настройка агрегатора

Как отмечалось в предыдущем разделе, среда выполнения собирает данные о выполнении функции за определенный период времени. По умолчанию используется период в 30 секунд или 1000 запусков в зависимости от того, что из этого наступит раньше. Этот параметр можно настроить в файле [host.json].  Ниже приведен пример:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Настройка выборки

Приложение Insights имеет функцию [выборки,](../azure-monitor/app/sampling.md) которая может защитить вас от получения слишком много телеметрических данных о завершенных выполнениях во время пиковой нагрузки. Когда скорость входящих выполнения превышает указанный порог, Application Insights начинает случайным образом игнорировать некоторые входящие выполнения. Настройка по умолчанию для максимального количества выполнения в секунду составляет 20 (пять в версии 1.x). Вы можете настроить выборку в файле [host.json].  Ниже приведен пример:

### <a name="version-2x-and-later"></a>Версия 2.x и более поздняя

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Версия 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Выборка](../azure-monitor/app/sampling.md) включена по умолчанию. Если данные не кажутся, возможно, потребуется настроить параметры выборки в соответствии с конкретным сценарием мониторинга.

## <a name="write-logs-in-c-functions"></a>Запись журналов в функциях C#

В коде функции вы можете сохранять журналы, которые отображаются в виде трассировок в Application Insights.

### <a name="ilogger"></a>ILogger

Используйте параметр [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) в функциях вместо параметра `TraceWriter`. Логи, созданные `TraceWriter` с помощью перейдите на Application Insights, но `ILogger` позволяют делать [структурированные журналы.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)

Объект `ILogger` позволяет вызывать для создания журналов  [методы расширения ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods)`Log<level>`. Следующий код `Information` записывает журналы с категорией "Function.<YOUR_FUNCTION_NAME>. Пользователь."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Структурированное ведение журнала

Использование параметров в сообщении журнала определяется порядком заполнителей, а не их именами. Предположим, у вас есть следующий код:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Если вы примените эту же строку сообщения с обратным порядком параметров, в тексте сообщения значения окажутся на неправильных местах.

Такой метод обработки заполнителей позволяет выполнять структурированное ведение журналов. Application Insights хранит пары значения параметра и строку сообщения. Благодаря этому все аргументы сообщения становятся полями, по которым можно выполнять запросы.

Если вызов метода регистрации выглядит как предыдущий пример, можно заказать поле. `customDimensions.prop__rowKey` Префикс `prop__` добавляется, чтобы убедиться, что нет коллизий между полями, добавленными временем выполнения, и полями, добавленными кодом функции.

Исходную строку сообщения можно получить, указав в запросе поле `customDimensions.prop__{OriginalFormat}`.  

Ниже приведен пример JSON-представления для данных `customDimensions`.

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Регистрация пользовательских метрик

В функциях на языке C# вы можете использовать метод расширения `LogMetric` для `ILogger`, чтобы создать пользовательские метрики в Application Insights. Ниже приведен пример вызова метода.

```csharp
logger.LogMetric("TestMetric", 1234);
```

Этот код является альтернативой вызову `TrackMetric` с помощью API Application Insights для .NET.

## <a name="write-logs-in-javascript-functions"></a>Ведение журналов в функциях JavaScript

В функциях Node.js для ведения журналов следует использовать `context.log`. Структурированная запись не включена.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Регистрация пользовательских метрик

При запуске [версии 1.x](functions-versions.md#creating-1x-apps) времени выполнения функций Node.js `context.log.metric` функции могут использовать метод для создания пользовательских метрик в Application Insights. Этот метод в настоящее время не поддерживается в версии 2.x и позже. Ниже приведен пример вызова метода.

```javascript
context.log.metric("TestMetric", 1234);
```

Этот код является альтернативой вызову `trackMetric` с помощью Node.js SDK для исследования приложений.

## <a name="log-custom-telemetry-in-c-functions"></a>Раздел о записи пользовательской телеметрии в функциях C#

Существует специфическая для функций версия Приложения Исследования SDK, которые можно использовать для отправки пользовательских телеметрических данных из ваших функций в Application Insights: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Используйте следующую команду из запроса команды для установки этого пакета:

# <a name="command"></a>[Команда](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

В этой команде `<VERSION>` замените версию этого пакета, которая поддерживает установленную версию [Microsoft.Azure.WebJobs.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/) 

В следующих примерах СЗ используется [пользовательский API телеметрии.](../azure-monitor/app/api-custom-events-metrics.md) Пример приведен для библиотеки классов .NET, но код Application Insights в скрипте C# будет точно таким же.

### <a name="version-2x-and-later"></a>Версия 2.x и более поздняя

Версия 2.x и более поздние версии времени выполнения используют новые функции в Application Insights для автоматического соотношения телеметрии с текущей операцией. Нет необходимости вручную устанавливать `Id`операцию `ParentId`или `Name` поля.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) является рекомендуемым в настоящее время API для создания метрики.

### <a name="version-1x"></a>Версия 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Не звоните `TrackRequest` `StartOperation<RequestTelemetry>` или потому, что вы увидите дубликаты запросов на вызов функции.  Среда выполнения Функций Azure автоматически отслеживает запросы.

Не указывайте `telemetryClient.Context.Operation.Id`. Эта глобальная настройка вызывает неправильную корреляцию, когда многие функции работают одновременно. Вместо этого создайте экземпляр телеметрии (`DependencyTelemetry`, `EventTelemetry`) и измените его свойство `Context`. Затем перейдите в экземпляртелейки `TelemetryClient` на`TrackDependency()` `TrackEvent()`соответствующий `TrackMetric()` `Track` метод на (, , ). Этот метод гарантирует, что телеметрия имеет правильные детали корреляции для текущего вызова функции.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Раздел о записи пользовательской телеметрии в функциях JavaScript

Вот фрагмент кода образца, который отправляет пользовательские телеметрии с [приложением Исследования Node.js SDK](https://github.com/microsoft/applicationinsights-node.js):

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

Параметр `tagOverrides` устанавливает `operation_Id` идентификатор вызова функции. Этот параметр позволяет сопоставлять все автоматически создаваемые и пользовательские данные телеметрии с конкретным вызовом функции.

## <a name="dependencies"></a>Зависимости

Функции v2 автоматически собирают зависимости для запросов HTTP, ServiceBus, EventHub и S'L.

Вы можете написать пользовательский код, чтобы показать зависимости. Например, см. пример кода в [разделе телеметрии с пользовательским образом сc.](#log-custom-telemetry-in-c-functions) Образец кода приводит к тому, *что карта приложений* в Application Insights выглядит следующим образом:

![Схема сопоставления приложений](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Включение интеграции с Application Insights

Для отправки данных в Application Insights приложению-функции требуется ключ инструментирования для ресурса Application Insights. В параметре приложения должен быть ключ с именем **APPINSIGHTS_INSTRUMENTATIONKEY**.

При создании приложения функции на [портале Azure,](functions-create-first-azure-function.md)из командной строки с помощью [основных инструментов Azure Functions](functions-create-first-azure-function-azure-cli.md)или с помощью [Visual Studio Code,](functions-create-first-function-vs-code.md)интеграция Application Insights включена по умолчанию. Ресурс Application Insights имеет то же имя, что и приложение функции, и создается либо в том же регионе, либо в ближайшем регионе.

### <a name="new-function-app-in-the-portal"></a>Новое функциональное приложение на портале

Чтобы просмотреть создаваемый ресурс Application Insights, выберите его для расширения окна **Application Insights.** Вы можете изменить **имя нового ресурса** или выбрать другое **местоположение** в [географии Azure,](https://azure.microsoft.com/global-infrastructure/geographies/) где требуется хранить данные.

![Включение Application Insights при создании приложения-функции](media/functions-monitoring/enable-ai-new-function-app.png)

При **выборе Create**ресурс Application Insights создается с помощью приложения функции, которое имеет `APPINSIGHTS_INSTRUMENTATIONKEY` набор в настройках приложения. Все готово к работе.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Добавление в существующее функциональное приложение 

При создании приложения функции с помощью [Visual Studio](functions-create-your-first-function-visual-studio.md)необходимо создать ресурс Application Insights. Затем можно добавить ключ приборов из этого ресурса в качестве настройки приложения в приложении.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Ранние версии функций использовали встроенный мониторинг, который больше не рекомендуется. При включении интеграции Application Insights для такого приложения функции необходимо также [отключить встроенный журнал.](#disable-built-in-logging)  

## <a name="report-issues"></a>Сообщение о проблемах

Чтобы сообщить о проблеме с интеграцией Функций Azure с Application Insights, внести предложение или отправить запрос, [создайте обращение в GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Журналы потоковой передачи

При разработке приложения часто требуется увидеть, что пишется в журналы в режиме реального времени при запуске в Azure.

Существует два способа просмотра потока файлов журнала, генерируемых выполнением функций.

* **Встроенная потоковая передача журнала:** платформа App Service позволяет просматривать поток файлов журнала приложения. Это эквивалентно выходу, который просматривается при отладке функций во время [локальной разработки](functions-develop-local.md) и при использовании вкладки **Test** на портале. Отображается вся информация на основе журналов. Для получения дополнительной [информации см.](../app-service/troubleshoot-diagnostic-logs.md#stream-logs) Этот метод потоковой передачи поддерживает только один экземпляр и не может быть использован с приложением, работающим на Linux в плане потребления.

* **Live Metrics Stream:** когда приложение функции [подключено к Application Insights,](#enable-application-insights-integration)вы можете просматривать данные журнала и другие метрики практически в режиме реального времени на портале Azure с помощью [Live Metrics Stream.](../azure-monitor/app/live-stream.md) Используйте этот метод при мониторинге функций, работающих на нескольких экземплярах или на Linux в плане потребления. Этот метод использует [выборочные данные.](#configure-sampling)

Потоки журналов можно просматривать как на портале, так и в большинстве локальных сред разработки. 

### <a name="portal"></a>Портал

Оба типа потоков журналов можно просмотреть на портале.

#### <a name="built-in-log-streaming"></a>Встроенная потоковая передача журналов

Для просмотра журналов потоковой передачи на портале выберите вкладку **функций Платформы** в приложении функции. Затем, под **мониторингом,** выберите **потоковое журнал.**

![Включить потоковые журналы на портале](./media/functions-monitoring/enable-streaming-logs-portal.png)

Это подключает ваше приложение к службе потоковой передачи журналов, и журналы приложений отображаются в окне. Вы можете переключаться между **журналами приложений** и **журналами веб-серверов.**  

![Просмотр журналов потоковой передачи на портале](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Динамический поток метрик

Для просмотра live Metrics Stream для вашего приложения выберите вкладку **«Обзор»** приложения функции. Если у вас есть Application Insights позволяет, вы видите ссылку **Application Insights** под **настраиваемыми функциями.** Эта ссылка приведет вас к странице Application Insights для вашего приложения.

В application Insights выберите **Поток live Metrics**Stream. [Образцы записей журнала](#configure-sampling) отображаются в **образце телеметрии**.

![Посмотреть Live МетрикС Поток на портале](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Основные инструменты

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Включить потоковые журналы с помощью [Azure CLI.](/cli/azure/install-azure-cli) Используйте следующие команды для входа, выбора подписки и файлов журнала потока:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Вы можете включить потоковые журналы с помощью [Azure PowerShell.](/powershell/azure/overview) Для PowerShell используйте следующие команды для добавления учетной записи Azure, выбора подписки и файлов журнала потока:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Отключение встроенного ведения журнала

При включании приложений Insights отключите встроенную систему, используюую хранилище Azure. Встроенная запись полезна для тестирования с легкими рабочими нагрузками, но не предназначена для высоконагрузного использования. Для мониторинга производства мы рекомендуем Application Insights. Если встроенная запись используется в производстве, запись регистрации может быть неполной из-за регулирования на Хранилище Azure.

Чтобы отключить встроенное ведение журнала, удалите параметр приложения `AzureWebJobsDashboard`. Дополнительные сведения о том, как удалять параметры приложения на портале Azure, см. в разделе **Параметры приложения** статьи [Управление приложением-функцией на портале Azure](functions-how-to-use-azure-function-app-settings.md#settings). Прежде чем удалить настройки приложения, убедитесь, что никакие существующие функции в той же функции приложения использовать настройки для Azure Storage триггеров или привязок.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих ресурсах:

* [Application Insights](/azure/application-insights/)
* [Ведение журнала ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
