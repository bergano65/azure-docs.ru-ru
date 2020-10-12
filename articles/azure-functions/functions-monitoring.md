---
title: Мониторинг Функций Azure
description: Сведения об использовании Azure Application Insights с решением "Функции Azure" для мониторинга выполнения функций.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.custom: devx-track-csharp, fasttrack-edit
ms.openlocfilehash: 239d1da028a06d4272ed9b22b624413394aa142f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212995"
---
# <a name="monitor-azure-functions"></a>Мониторинг Функций Azure

Решение [Функции Azure](functions-overview.md) предлагает встроенную интеграцию со службой [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) для мониторинга функций. В этой статье показано, как настроить отправку файлов журнала, созданных системой, из Функций Azure в Application Insights.

Мы рекомендуем использовать Application Insights, так как эта служба собирает данные журнала, сведения о производительности и информацию об ошибках. Она автоматически обнаруживает аномалии в производительности и содержит мощные аналитические средства, которые помогают диагностировать проблемы и анализировать использование функций. Эта служба помогает постоянно улучшать производительность и удобство использования. Вы даже можете применить Application Insights на этапе локальной работы над проектом приложения-функции. Дополнительные сведения см. в статье [Что такое Application Insights?](../azure-monitor/app/app-insights-overview.md).

Так как необходимое инструментирование Application Insights встроено в Функции Azure, для подключения приложения-функции к ресурсу Application Insights нужно просто использовать действительный ключ инструментирования. Этот ключ инструментирования следует поместить в параметры приложения при создании ресурса приложения-функции в Azure. Если у приложения-функции еще нет такого ключа, его можно [настроить вручную](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Стоимость и ограничения Application Insights

Вы можете протестировать интеграцию Application Insights с Функциями Azure бесплатно. Суточный объем данных, которые могут быть обработаны бесплатно, ограничен. Во время тестирования вы можете достичь этого предельного значения. Azure отправляет уведомления на портале и по электронной почте при приближении к ежедневному ограничению. Если вы пропустите эти оповещения и превысите предельное значение, новые журналы не будут отображаться в Application Insights. Учитывайте ограничения, чтобы не тратить время на ненужное устранение неполадок. Дополнительные сведения см. в статье [Управление ценами и объемом данных в Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> В Application Insights есть функция [выборки](../azure-monitor/app/sampling.md), которая позволяет избежать создания слишком большого объема данных телеметрии для завершенных выполнений в периоды пиковой нагрузки. Выборка включена по умолчанию. Если кажется, что некоторые данные отсутствуют, можно просто настроить параметры выборки в соответствии с конкретным сценарием мониторинга. Дополнительные сведения см. в разделе о [настройке выборки](#configure-sampling).

Полный список функций Application Insights, доступных для приложения-функции, подробно описан в статье [Application Insights для функций, поддерживаемых в решении "Функции Azure"](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="view-telemetry-in-monitor-tab"></a>Просмотр телеметрии на вкладке "Монитор"

При [включенной интеграции с Application Insights](#enable-application-insights-integration) данные телеметрии можно просматривать на вкладке **Монитор**.

1. На странице приложения-функции выберите функцию, которая выполнялась по крайней мере один раз после настройки Application Insights. Затем в области слева выберите **Монитор**. Периодически выбирайте **Обновить**, пока не появится список вызовов функций.

   ![Список вызовов](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Для отображения списка может потребоваться до пяти минут. Это время необходимо клиенту, чтобы подготовить пакеты данных телеметрии для передачи на сервер. Такая задержка отсутствует в случае [Live Metrics Stream](../azure-monitor/app/live-stream.md). Эта служба подключается к узлу решения "Функции" при загрузке страницы, поэтому журналы передаются на нее напрямую.

1. Чтобы просмотреть журналы для вызова определенной функции, выберите ссылку на столбец **Дата (UTC)** для этого вызова. Выходные данные ведения журнала для этого вызова отображаются на новой странице.

   ![Сведения о вызове](media/functions-monitoring/invocation-details-ai.png)

1. Выберите **Запустить в Application Insights**, чтобы просмотреть источник запроса, который извлекает данные журнала Azure Monitor в журнале Azure. Если вы впервые используете функцию Azure Log Analytics в подписке, вам будет предложено ее включить.

1. После включения Log Analytics отображается следующий запрос. Как можно видеть, результаты запроса ограничены последними 30 днями (`where timestamp > ago(30d)`). Кроме того, в результатах отображается не более 20 строк (`take 20`). В отличие от них, список сведений о вызовах для функции содержит информацию за последние 30 дней без ограничений.

   ![Список вызовов в аналитике Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Дополнительные сведения см. в разделе [Запросы к данным телеметрии](#query-telemetry-data) далее в этой статье.

## <a name="view-telemetry-in-application-insights"></a>Просмотр данных телеметрии в Application Insights

Чтобы открыть Application Insights из приложения-функции на портале Azure, слева на странице в разделе **Параметры** выберите **Application Insights**. Если вы впервые используете Application Insights с подпиской, вам будет предложено включить эту функцию. Выберите **Включить Application Insights**, а затем на следующей странице выберите **Применить**.

![Открытие Application Insights на странице обзора приложения-функции](media/functions-monitoring/ai-link.png)

Дополнительные сведения об использовании Application Insights см. в [документации по Application Insights](/azure/application-insights/). В этом разделе представлено несколько примеров просмотра данных в Application Insights. Если вы уже знакомы с Application Insights, можете сразу переходить к [разделам, посвященным конфигурации и настройке данных телеметрии](#configure-categories-and-log-levels).

![Вкладка обзора Application Insights](media/functions-monitoring/metrics-explorer.png)

При оценке поведения, производительности и ошибок в функциях могут быть полезны следующие области Application Insights.

| Изучение | Описание |
| ---- | ----------- |
| **[Сбои](../azure-monitor/app/asp-net-exceptions.md)** |  Создание диаграмм и оповещений на основе сбоев функции и исключений сервера. **Имя операции** обозначает имя функции. Сбои в зависимостях здесь не отображаются, если вы не настроили для них пользовательскую телеметрию. |
| **[Производительность](../azure-monitor/app/performance-counters.md)** | Анализ проблем с производительностью путем просмотра использования ресурсов и пропускной способности для **экземпляров облачных ролей**. Эти данные можно использовать для отладки в тех случаях, когда функции создают чрезмерную нагрузку на базовые ресурсы. |
| **[Метрики](../azure-monitor/platform/metrics-charts.md)** | Создание диаграмм и оповещений на основе метрик. Используются такие метрики: число вызовов функций, время выполнения и частота успешных попыток. |
| **[Интерактивные метрики](../azure-monitor/app/live-stream.md)** | Просмотр данных метрик по мере их создания почти в реальном времени. |

## <a name="query-telemetry-data"></a>Запросы к данным телеметрии

[Аналитика в Application Insights](../azure-monitor/log-query/log-query-overview.md) предоставляет доступ ко всем данным телеметрии в формате таблиц в базе данных. Функция аналитики поддерживает язык запросов для извлечения, обработки и визуализации данных. 

Выберите **Журналы** для просмотра или запроса событий в журналах.

![Пример данных аналитики](media/functions-monitoring/analytics-traces.png)

В этом примере показано распределение запросов по рабочим ролям за последние 30 минут.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Доступные таблицы представлены на вкладке **Схема** в области слева. Данные, создаваемые при вызовах функций, вы найдете в следующих таблицах:

| Таблица | Описание |
| ----- | ----------- |
| **traces** | Журналы, созданные средой выполнения и кодом функций. |
| **requests** | По одному запросу для каждого вызова функции. |
| **exceptions** | Любые исключения в среде выполнения. |
| **customMetrics** | Число успешных и неудачных вызовов, доля успешных попыток, продолжительность. |
| **customEvents** | События, отслеживаемые средой выполнения, например HTTP-запросы, которые активируют функции; |
| **performanceCounters** | Сведения о производительности серверов, на которых выполняются функции. |

Остальные таблицы предназначены для проверок доступности, а также телеметрии клиента и браузера. Вы можете реализовать пользовательскую телеметрию, чтобы добавлять в них данные.

В каждой таблице есть поле `customDimensions`, где хранится часть данных о конкретной функции.  Например, следующий запрос получает все трассировки с уровнем журнала `Error`.

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Среда выполнения предоставляет поля `customDimensions.LogLevel` и `customDimensions.Category`. Вы можете указать дополнительные поля в журналах, которые указываются в коде функции. См. раздел [Структурированное ведение журнала](#structured-logging) далее в этой статье.

## <a name="configure-categories-and-log-levels"></a>Настройка категорий и уровней ведения журнала

Вы можете использовать Application Insights без какой бы то ни было настройки конфигурации. Конфигурация по умолчанию может привести к созданию больших объемов данных. Если вы используете подписку Azure для Visual Studio, объем данных Application Insights может достигнуть установленного верхнего предела. Далее в этой статье вы узнаете, как настроить данные, отправляемые функциями в Application Insights. Для приложения-функции ведение журнала настраивается в файле [host.json].

### <a name="categories"></a>Категории

В средстве ведения журнала Функций Azure предусмотрена *категория* для каждого журнала. Категория указывает, какая часть кода среды выполнения или кода функции записывала данные в этот журнал. В следующей таблице описаны основные категории журналов, создаваемых средой выполнения. 

| Категория | Описание |
| ----- | ----- | 
| Host.Results | Эти журналы отображаются в Application Insights как **запросы**. Они содержат сведения об успешном выполнении или сбое функций. Все эти журналы записываются на уровне `Information`. При фильтрации на уровне `Warning` или выше эти данные отображаться не будут. |
| Host.Aggregator | Эти журналы содержат счетчики и средние значения по вызовам функций за [настраиваемый](#configure-the-aggregator) период времени. По умолчанию используется период 30 секунд или 1000 результатов в зависимости от того, что из этого наступит раньше. Журналы доступны в таблице **customMetrics** в Application Insights. Например, здесь вы найдете число запусков, долю успешных попыток и длительность выполнения. Все эти журналы записываются на уровне `Information`. При фильтрации на уровне `Warning` или выше эти данные отображаться не будут. |

Все журналы для категорий, кроме перечисленных выше, доступны в таблице **traces** в Application Insights.

Все журналы с категориями, имена которых начинаются со слова `Host`, сохраняются средой выполнения Функций Azure. Журналы для **запущенных** и **выполненных** функций относятся к категории `Host.Executor`. Успешные выполнения записываются в эти журналы на уровне `Information`. Исключения записываются на уровне `Error`. Кроме того, среда выполнения создает журналы уровня `Warning`, например очередь сообщений, отправленных в очередь подозрительных сообщений.

Среда выполнения Функций Azure создает журналы, название категории для которых начинается со слова "Host". В версии 1.x журналы `function started`, `function executed` и `function completed` относятся к категории `Host.Executor`. Начиная с версии 2.x, эти журналы относятся к категории `Function.<YOUR_FUNCTION_NAME>`.

Если вы сохраняете данные в журналы из кода функции, используется категория `Function.<YOUR_FUNCTION_NAME>.User`, а уровень ведения журнала может быть любым. В версии 1.x среды выполнения Функций используется категория `Function`.

### <a name="log-levels"></a>Уровни журнала

В средстве ведения журнала Функций Azure также предусмотрена возможность задать *уровень ведения* для каждого журнала. Параметр [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) (Уровень ведения журнала) является перечислением целочисленных значений, которые обозначают относительную важность.

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

Файл [host.json] определяет, какой объем информации приложение-функция отправляет в журнал Application Insights. В каждой категории вы можете указать минимальный уровень ведения журнала для отправки данных. Приведем два примера: первый пример предназначен для среды выполнения Функций [версии 2.x и более поздних](functions-versions.md#version-2x) (с .NET Core), а второй — для среды выполнения версии 1.x.

### <a name="version-2x-and-higher"></a>Версия 2.x и более поздние

Среда выполнения Функций версии 2.x и более поздних использует [иерархию фильтра ведения журнала .NET Core](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

* Для журналов категории `Host.Results` или `Function` в Application Insights отправляются данные с уровнем `Error` и выше. Данные журналов с уровнем `Warning` и ниже игнорируются.
* Все журналы с категорией `Host.Aggregator` отправляются в Application Insights. Уровень ведения журнала `Trace` — это то же, что в некоторых средствах ведения журналов именуется как `Verbose`. В файле [host.json] необходимо использовать `Trace`.
* Для всех остальных журналов в Application Insights отправляются данные с уровнем `Information` и выше.

Значение категории в [host.json] управляет ведением журнала для всех категорий, название которых начинается с аналогичного значения. `Host` в файле [host.json] управляет ведением журнала для категорий `Host.General`, `Host.Executor`, `Host.Results` и т. д.

Если [host.json] содержит несколько категорий с одинаковым началом строки, сопоставление начинается с более длинных строк. Предположим, вы хотите регистрировать все данные среды выполнения без данных `Host.Aggregator` на уровне `Error`, но с данными `Host.Aggregator` на уровне `Information`.

### <a name="version-2x-and-later"></a>Версия 2.x и более поздние

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

Чтобы не вести журналы для определенной категории, установите для нее уровень ведения журнала `None`. Теперь для этой категории не будут сохраняться никакие журналы, так как более высокий уровень ведения журнала не существует.

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

В Application Insights есть функция [выборки](../azure-monitor/app/sampling.md), которая позволяет избежать создания слишком большого объема данных телеметрии для завершенных выполнений в периоды пиковой нагрузки. Если скорость входящих выполнений превышает заданное пороговое значение, служба Application Insights будет случайным образом игнорировать часть входящих выполнений. Максимальное количество выполнений в секунду по умолчанию — 20 (пять в версии 1.x). Вы можете настроить выборку в файле [host.json](./functions-host-json.md#applicationinsights).  Ниже приведен пример:

### <a name="version-2x-and-later"></a>Версия 2.x и более поздние

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request"
      }
    }
  }
}
```

В версии 2. x можно исключить определенные типы данных телеметрии из выборки. В приведенном выше примере данные типа `Request` исключаются из выборки. Это гарантирует, что *все* выполнения функций (запросов) записываются в журнал, а другие типы телеметрии остаются в процессе выборки.

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

## <a name="write-logs-in-c-functions"></a>Запись журналов в функциях C#

В коде функции вы можете сохранять журналы, которые отображаются в виде трассировок в Application Insights.

### <a name="ilogger"></a>ILogger

Используйте параметр [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) в функциях вместо параметра `TraceWriter`. Журналы, созданные с помощью `TraceWriter`, тоже отправляются в Application Insights, но `ILogger` позволяют использовать [структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Объект `ILogger` позволяет вызывать для создания журналов [методы расширения ILogger](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods)`Log<level>`. Следующий код записывает журнал `Information` с категорией "Function.<YOUR_FUNCTION_NAME>.User."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Структурированное ведение журнала

Использование параметров в сообщении журнала определяется порядком заполнителей, а не их именами. Предположим, что у вас есть следующий код:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Если вы примените эту же строку сообщения с обратным порядком параметров, в тексте сообщения значения окажутся на неправильных местах.

Такой метод обработки заполнителей позволяет выполнять структурированное ведение журналов. Application Insights сохраняет не только строку сообщения, но и параметры в формате пар "имя-значение". Благодаря этому все аргументы сообщения становятся полями, по которым можно выполнять запросы.

Если используется метод ведения журнала из предыдущего примера, вы сможете отправить запрос поля `customDimensions.prop__rowKey`. При этом добавляется префикс `prop__`, чтобы не возникало конфликтов между полями, которые добавляет среда выполнения и которые добавляет код вашей функции.

Исходную строку сообщения можно получить, указав в запросе поле `customDimensions.prop__{OriginalFormat}`.  

Ниже приведен пример JSON-представления для данных `customDimensions`.

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Запись в журнал пользовательских метрик

В функциях на языке C# вы можете использовать метод расширения `LogMetric` для `ILogger`, чтобы создать пользовательские метрики в Application Insights. Ниже приведен пример вызова метода.

```csharp
logger.LogMetric("TestMetric", 1234);
```

Этот пример кода действует так же, как вызов `TrackMetric` с использованием API Application Insights для .NET.

## <a name="write-logs-in-javascript-functions"></a>Ведение журналов в функциях JavaScript

В функциях Node.js для ведения журналов следует использовать `context.log`. Структурированное ведение журнала не используется.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Запись в журнал пользовательских метрик

При работе в среде выполнения Функций Azure [версии 1.x](functions-versions.md#creating-1x-apps) в функциях Node.js вы можете использовать метод `context.log.metric`, чтобы создавать пользовательские метрики в Application Insights. Этот метод не поддерживается в версии 2.x и более поздних. Ниже приведен пример вызова метода.

```javascript
context.log.metric("TestMetric", 1234);
```

Этот пример кода действует так же, как вызов `trackMetric` с использованием пакета SDK Node.js для Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Раздел о записи пользовательской телеметрии в функциях C#

Для отправки пользовательских данных телеметрии из функций в Application Insights можно использовать зависящую от функций версию пакета SDK Application Insights: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Для установки этого пакета используйте следующую команду из командной строки:

# <a name="command"></a>[Command](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

В этой команде замените `<VERSION>` версией этого пакета, которая поддерживает установленную версию [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

В следующем примере C# используется [настраиваемый интерфейс API телеметрии](../azure-monitor/app/api-custom-events-metrics.md). Пример приведен для библиотеки классов .NET, но код Application Insights в скрипте C# будет точно таким же.

### <a name="version-2x-and-later"></a>Версия 2.x и более поздние

Среда выполнения версии 2.x или более поздних использует новые функции в Application Insights для выполнения автоматической корреляции данных телеметрии с текущей операцией. Нет необходимости вручную задавать для операции поля `Id`, `ParentId` или `Name`.

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

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) — это рекомендуемый в данный момент интерфейс API для создания метрики.

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

Не вызывайте `TrackRequest` или `StartOperation<RequestTelemetry>`, ведь при этом будут отображаться повторные запросы на вызов функции.  Среда выполнения Функций Azure автоматически отслеживает запросы.

Не указывайте `telemetryClient.Context.Operation.Id`. Этот глобальный параметр может вызвать неправильную корреляцию при одновременном выполнении нескольких функций. Вместо этого создайте экземпляр телеметрии (`DependencyTelemetry`, `EventTelemetry`) и измените его свойство `Context`. Затем передайте экземпляр телеметрии в соответствующий метод `Track` в `TelemetryClient` (`TrackDependency()`, `TrackEvent()`, `TrackMetric()`). Этот метод гарантирует правильность сведений о корреляции телеметрии для текущего вызова функции.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Добавление пользовательской телеметрии в функциях JavaScript

Ниже приведены примеры фрагментов кода, которые отправляют пользовательские данные телеметрии с помощью [пакета SDK для Application Insights Node.js](https://github.com/microsoft/applicationinsights-node.js).

### <a name="version-2x-and-later"></a>Версия 2.x и более поздние

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

### <a name="version-1x"></a>Версия 1.x

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

Параметр `tagOverrides` присваивает параметру `operation_Id` значение идентификатора вызова функции. Этот параметр позволяет сопоставлять все автоматически создаваемые и пользовательские данные телеметрии с конкретным вызовом функции.

## <a name="dependencies"></a>Зависимости

Функции версии 2 автоматически собирают зависимости для HTTP-запросов, ServiceBus, EventHub и SQL.

Для отображения зависимостей можно написать пользовательский код. Пример такого кода см. в [разделе о пользовательских данных телеметрии на C#](#log-custom-telemetry-in-c-functions). Этот пример кода создает в Application Insights *карту приложения* следующего вида.

![Схема сопоставления приложений](./media/functions-monitoring/app-map.png)

> [!NOTE]
> Зависимости записываются на уровне информации. Если фильтр выполняется по предупреждению или выше, эти данные отображаться не будут. Кроме того, автоматическая коллекция зависимостей происходит в области, отличной от пользовательской. Поэтому убедитесь, что для уровня задано по меньшей мере **информация** вне области пользователя в host.js(т. е. за пределами функции. <YOUR_FUNCTION_NAME>. Ключ пользователя), если необходимо записать эти зависимости.

## <a name="enable-application-insights-integration"></a>Включение интеграции с Application Insights

Для отправки данных в Application Insights приложению-функции требуется ключ инструментирования для ресурса Application Insights. В параметре приложения должен быть ключ с именем **APPINSIGHTS_INSTRUMENTATIONKEY**.

При создании приложения-функции на [портале Azure](functions-create-first-azure-function.md) из командной строки с помощью [Azure Functions Core Tools](functions-create-first-azure-function-azure-cli.md) или в [Visual Studio Code](functions-create-first-function-vs-code.md) интеграция с Application Insights включена по умолчанию. Ресурс Application Insights имеет то же имя, что и приложение-функция, и создается в том же или ближайшем регионе.

### <a name="new-function-app-in-the-portal"></a>Создание приложения-функции на портале Azure

Для проверки создаваемого ресурса Application Insights выберите его, чтобы развернуть окно **Application Insights**. Вы можете задать **новое имя ресурса** или выбрать другое **расположение** в [географическом регионе Azure](https://azure.microsoft.com/global-infrastructure/geographies/), где будут храниться данные.

![Включение Application Insights при создании приложения-функции](media/functions-monitoring/enable-ai-new-function-app.png)

При выборе команды **Создать** создается ресурс Application Insights с приложением-функцией, в параметрах приложения которого задано `APPINSIGHTS_INSTRUMENTATIONKEY`. Все готово к работе.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Добавление имеющегося приложения-функции 

При создании приложения-функции с помощью [Visual Studio](functions-create-your-first-function-visual-studio.md) необходимо создать ресурс Application Insights. Затем ключ инструментирования из этого ресурса можно добавить в качестве [параметра приложения](functions-how-to-use-azure-function-app-settings.md#settings) в приложение-функцию.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Ранние версии решения "Функции" использовали встроенный мониторинг, который больше не рекомендуется. При включении интеграции Application Insights для такого приложения-функции необходимо также [отключить встроенное ведение журналов](#disable-built-in-logging).  

## <a name="report-issues"></a>Сообщение о проблемах

Чтобы сообщить о проблеме с интеграцией Функций Azure с Application Insights, внести предложение или отправить запрос, [создайте обращение в GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Журналы потоковой передачи

При разработке приложения часто требуется узнать, какие записи записываются в журналы практически в реальном времени при работе в Azure.

Существует два способа просмотра потока файлов журнала, создаваемых при выполнении функций.

* **Встроенная потоковая передача журналов**. Платформа Службы приложений позволяет просматривать поток файлов журналов приложений. Он эквивалентен выходным данным, которые отображаются при отладке функций во время [локальной разработки](functions-develop-local.md) и при использовании вкладки **Тест** на портале. При этом отображаются все данные в журнале. Дополнительные сведения см. в разделе [Потоковая передача журналов](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Этот потоковый метод поддерживает только один экземпляр и не может использоваться с приложением, которое выполняется в Linux в плане потребления.

* **Live Metrics Stream**. Если приложение-функция [подключено к Application Insights](#enable-application-insights-integration), данные журнала и другие метрики можно просматривать почти в реальном времени на портале Azure с помощью [Live Metrics Stream](../azure-monitor/app/live-stream.md). Используйте этот метод при мониторинге функций, выполняемых в нескольких экземплярах или в Linux в плане потребления. Этот метод использует [данные выборки](#configure-sampling).

Потоки журнала можно просматривать как на портале, так и в большинстве локальных сред разработки. 

### <a name="portal"></a>Портал

На портале можно просматривать оба типа потоков журналов.

#### <a name="built-in-log-streaming"></a>Встроенная потоковая передача журналов

Чтобы просмотреть журналы потоковой передачи на портале, выберите в приложении-функции вкладку **Функции платформы**. Затем в разделе **Мониторинг** выберите **Потоковая передача журнала**.

![Включение потоковой передачи журналов на портале](./media/functions-monitoring/enable-streaming-logs-portal.png)

При этом приложение будет подключено к службе потоковой передачи журналов, а журналы приложений отобразятся в окне. Вы можете переключаться между режимом отображения журналов для **приложения** и **веб-сервера**.  

![Просмотр потоковой передачи журналов на портале](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Динамический поток метрик

Чтобы просмотреть Live Metrics Stream для приложения, перейдите на вкладку **Обзор** приложения-функции. Если компонент Application Insights включен, в разделе **Настроенные функции** отображается ссылка **Application Insights**. По этой ссылке можно перейти на страницу Application Insights приложения.

В Application Insights выберите **Live Metrics Stream**. В разделе **Пример телеметрии** отображаются [записи выборки из журнала](#configure-sampling).

![Просмотр Live Metrics Stream на портале](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Вы можете включить потоковую передачу журналов с помощью [Azure CLI](/cli/azure/install-azure-cli). Используйте следующие команды, чтобы войти, выбрать подписку и включить потоковую передачу файлов журнала.

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Вы можете включить потоковую передачу журналов с помощью [Azure PowerShell](/powershell/azure/). Для PowerShell используйте команду [Set-азвебапп](/powershell/module/az.websites/set-azwebapp) , чтобы включить ведение журнала в приложении-функции, как показано в следующем фрагменте кода: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Дополнительные сведения см. в [полном примере кода](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="scale-controller-logs-preview"></a>Журналы контроллера масштабирования (Предварительная версия)

Эта функция предоставляется в предварительной версии. 

[Контроллер масштабирования функций Azure](./functions-scale.md#runtime-scaling) наблюдает за экземплярами узла функций Azure, на котором выполняется приложение. Этот контроллер принимает решения о том, когда следует добавлять или удалять экземпляры на основе текущей производительности. Чтобы лучше понять, какие решения выполняет контроллер масштабирования для приложения-функции, можно подать журналы контроллера масштабирования как Application Insights или в хранилище BLOB-объектов.

Чтобы включить эту функцию, добавьте новый параметр приложения с именем `SCALE_CONTROLLER_LOGGING_ENABLED` . Значение этого параметра должно иметь формат в `<DESTINATION>:<VERBOSITY>` зависимости от следующих значений:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Например, следующая команда Azure CLI включает подробное ведение журнала из контроллера масштабирования для Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

В этом примере замените `<FUNCTION_APP_NAME>` и `<RESOURCE_GROUP_NAME>` именем приложения функции и именем группы ресурсов соответственно. 

Следующая команда Azure CLI отключает ведение журнала, устанавливая уровень детализации `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Можно также отключить ведение журнала, удалив `SCALE_CONTROLLER_LOGGING_ENABLED` параметр с помощью следующей команды Azure CLI:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="disable-built-in-logging"></a>Отключение встроенного ведения журнала

При включении Application Insights отключите встроенное ведение журнала, которое использует службу хранилища Azure. Встроенное ведение журнала полезно для тестирования с легкими рабочими нагрузками, но не предназначено для использования в рабочей среде с высокой нагрузкой. Для мониторинга рабочей среды рекомендуем использовать Application Insights. Если в рабочей среде используется встроенное ведение журнала, записи журнала могут быть неполными из-за регулирования службы хранилища Azure.

Чтобы отключить встроенное ведение журнала, удалите параметр приложения `AzureWebJobsDashboard`. Дополнительные сведения о том, как удалять параметры приложения на портале Azure, см. в разделе **Параметры приложения** статьи [Управление приложением-функцией на портале Azure](functions-how-to-use-azure-function-app-settings.md#settings). Перед удалением параметра приложения убедитесь, что имеющиеся функции в том же приложении-функции не используют этот параметр для триггеров или привязок службы хранилища Azure.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Application Insights](/azure/application-insights/)
* [Общие сведения о ведении журналов в ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
