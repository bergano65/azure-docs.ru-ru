---
title: Мониторинг Функций Azure
description: Узнайте, как использовать Azure Application Insights с функциями Azure для наблюдения за выполнением функций.
author: ggailey777
manager: gwallace
keywords: функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: dc333ef542da1330672ad1dc8ad731969eef6742
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374576"
---
# <a name="monitor-azure-functions"></a>Мониторинг Функций Azure

[Функции Azure](functions-overview.md) предлагают встроенную интеграцию с [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) для мониторинга функций. В этой статье показано, как настроить функции Azure для отправки в Application Insights файлов журналов, созданных системой.

Рекомендуется использовать Application Insights, так как он собирает данные журнала, производительности и ошибок. Она автоматически обнаруживает аномалии производительности и включает мощные средства анализа, помогающие диагностировать проблемы и понять, как используются функции. Эта служба помогает постоянно улучшать производительность и удобство использования. Вы даже можете использовать Application Insights во время разработки проекта для локальных приложений функций. Дополнительные сведения см. в разделе [что такое Application Insights?](../azure-monitor/app/app-insights-overview.md).

Так как необходимая Application Insights инструментирования встроена в функции Azure, вам нужно всего лишь использовать ключ инструментирования для подключения приложения-функции к ресурсу Application Insights.

## <a name="application-insights-pricing-and-limits"></a>Стоимость и ограничения Application Insights

Вы можете протестировать интеграцию Application Insights с приложением-функцией бесплатно. Существует ежедневное ограничение на объем данных, которые могут быть обработаны бесплатно. Это ограничение может быть достигнуто во время тестирования. Azure отправляет уведомления на портале и по электронной почте при приближении к ежедневному ограничению. Если вы пропустили эти оповещения и достигли предельного значения, новые журналы не будут отображаться в Application Insightsных запросах. Обратите внимание на ограничение, чтобы избежать ненужного времени для устранения неполадок. Дополнительные сведения см. в статье [Управление ценами и объемом данных в Application Insights](../azure-monitor/app/pricing.md).

Полный список функций Application Insights, доступных для приложения-функции, подробно описан в [Application Insights для поддерживаемых функций функций Azure](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="enable-application-insights-integration"></a>Включение интеграции с Application Insights

Для отправки данных в Application Insights приложению-функции требуется ключ инструментирования для ресурса Application Insights. В параметре приложения должен быть ключ с именем **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Новое приложение функции на портале

При [создании приложения-функции в портал Azure](functions-create-first-azure-function.md)интеграция Application Insights включена по умолчанию. Ресурс Application Insights имеет то же имя, что и приложение функции, и создается либо в том же регионе, либо в ближайшем регионе.

Чтобы проверить создаваемый ресурс Application Insights, выберите его, чтобы развернуть окно **Application Insights** . Вы можете изменить **новое имя ресурса** или выбрать другое **Расположение** в [географии Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , где вы хотите хранить данные.

![Включение Application Insights при создании приложения-функции](media/functions-monitoring/enable-ai-new-function-app.png)

При нажатии на кнопку " **создать**" в приложении-функции будет создан Application Insights ресурс с заданным @no__t 1 в параметрах приложения. Все готово к работе.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Добавить в существующее приложение функции 

При создании приложения-функции с помощью [Azure CLI](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md)или [Visual Studio Code](functions-create-first-function-vs-code.md)необходимо создать ресурс Application Insights. Затем можно добавить ключ инструментирования из этого ресурса в качестве параметра приложения в приложение функции.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Ранние версии функций использовали встроенный мониторинг, который больше не рекомендуется. При включении интеграции Application Insights для такого приложения-функции необходимо также [отключить встроенное ведение журнала](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Просмотр телеметрии на вкладке "Монитор"

С [включенной интеграцией Application Insights](#enable-application-insights-integration)можно просматривать данные телеметрии на вкладке " **монитор** ".

1. На странице приложение-функция выберите функцию, которая выполнялась хотя бы один раз после настройки Application Insights. Затем перейдите на вкладку **монитор** .

   ![Переход на вкладку мониторинга](media/functions-monitoring/monitor-tab.png)

1. Выберите периодически **обновлять** , пока не появится список вызовов функций.

   Отображение списка во время передачи данных на сервер с помощью клиента телеметрии может занять до пяти минут. (Задержка не применяется к [Live Metrics Stream](../azure-monitor/app/live-stream.md). Эта служба подключается к узлу решения "Функции" при загрузке страницы, поэтому журналы передаются напрямую на страницу.)

   ![Список вызовов](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Чтобы просмотреть журналы для вызова определенной функции, выберите ссылку на столбец **Дата** для этого вызова.

   ![Ссылка на сведения о вызове](media/functions-monitoring/invocation-details-link-ai.png)

   Выходные данные ведения журнала для этого вызова отображаются на новой странице.

   ![Сведения о вызове](media/functions-monitoring/invocation-details-ai.png)

Вы видите, что обе страницы имеют **команду "выполнить" в Application Insights** ссылку на запрос Application Insights Analytics, который получает данные.

![Выполнение в Application Insights](media/functions-monitoring/run-in-ai.png)

Отобразится следующий запрос. Вы видите, что список вызовов ограничен последними 30 днями. В списке отображается не более 20 строк (`where timestamp > ago(30d) | take 20`). Список сведений о вызовах находится за последние 30 дней без ограничений.

![Список вызовов в аналитике Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Дополнительные сведения см. в разделе [Запросы к данным телеметрии](#query-telemetry-data) далее в этой статье.

## <a name="view-telemetry-in-application-insights"></a>Просмотр данных телеметрии в Application Insights

Чтобы открыть Application Insights из приложения-функции в портал Azure, перейдите на страницу **Обзор** приложения-функции. В разделе **настроенные компоненты**выберите **Application Insights**.

![Открытие Application Insights на странице обзора приложения-функции](media/functions-monitoring/ai-link.png)

Дополнительные сведения об использовании Application Insights см. в [документации по Application Insights](https://docs.microsoft.com/azure/application-insights/). В этом разделе представлено несколько примеров просмотра данных в Application Insights. Если вы уже знакомы с Application Insights, можно перейти непосредственно к [разделам о настройке и настройке данных телеметрии](#configure-categories-and-log-levels).

![Вкладка обзора Application Insights](media/functions-monitoring/metrics-explorer.png)

Следующие области Application Insights могут быть полезны при оценке поведения, производительности и ошибок в функциях.

| Tab | Описание |
| ---- | ----------- |
| **[Сбоев](../azure-monitor/app/asp-net-exceptions.md)** |  Создавайте диаграммы и оповещения на основе ошибок функций и исключений сервера. **Имя операции** обозначает имя функции. Сбои в зависимостях не отображаются, если не реализовать пользовательскую телеметрию для зависимостей. |
| **[Системного](../azure-monitor/app/performance-counters.md)** | Анализ проблем производительности. |
| **Серверы** | Просмотр использования ресурсов и пропускной способности для каждого сервера. Эти данные можно использовать для отладки в тех случаях, когда функции создают чрезмерную нагрузку на базовые ресурсы. Серверы здесь называются **экземплярами облачных ролей**. |
| **[Метрики](../azure-monitor/app/metrics-explorer.md)** | Создание диаграмм и оповещений на основе метрик. Метрики включают число вызовов функций, время выполнения и частоту успешных попыток. |
| **[Динамический поток метрик: мгновенные метрики для подробного отслеживания](../azure-monitor/app/live-stream.md)** | Просмотр данных метрик по мере их создания в режиме реального времени. |

## <a name="query-telemetry-data"></a>Запросы к данным телеметрии

[Application Insights Analytics](../azure-monitor/app/analytics.md) предоставляет доступ ко всем данным телеметрии в виде таблиц в базе данных. Функция аналитики поддерживает язык запросов для извлечения, обработки и визуализации данных.

![Выбор функции аналитики](media/functions-monitoring/select-analytics.png)

![Пример данных аналитики](media/functions-monitoring/analytics-traces.png)

В этом примере показано распределение запросов по рабочим ролям за последние 30 минут.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Доступные таблицы показаны на вкладке **схема** слева. Данные, создаваемые при вызовах функций, вы найдете в следующих таблицах:

| Таблицы | Описание |
| ----- | ----------- |
| **отладоч** | Журналы, созданные средой выполнения и кодом функции. |
| **requests** | Один запрос для каждого вызова функции. |
| **exceptions** | Любые исключения, вызываемые средой выполнения. |
| **customMetrics** | Число успешных и неудачных вызовов, частота успешных операций и длительность. |
| **customEvents** | События, которые отписываются средой выполнения, например: HTTP-запросы, которые активируют функцию. |
| **performanceCounters** | Сведения о производительности серверов, на которых выполняются функции. |

Другие таблицы предназначены для тестов доступности, телеметрии клиентов и браузеров. Вы можете реализовать пользовательскую телеметрию, чтобы добавлять в них данные.

В каждой таблице есть поле `customDimensions`, где хранится часть данных о конкретной функции.  Например, следующий запрос получает все трассировки с уровнем журнала `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Среда выполнения предоставляет поля `customDimensions.LogLevel` и `customDimensions.Category`. Вы можете указать дополнительные поля в журналах, которые вы пишете в коде функции. См. раздел [Структурированное ведение журнала](#structured-logging) далее в этой статье.

## <a name="configure-categories-and-log-levels"></a>Настройка категорий и уровней ведения журнала

Вы можете использовать Application Insights без какой бы то ни было пользовательской конфигурации. Конфигурация по умолчанию может привести к созданию больших объемов данных. Если вы используете подписку Azure для Visual Studio, объем данных Application Insights может достигнуть установленного верхнего предела. Далее в этой статье вы узнаете, как настроить и настроить данные, отправляемые функциями в Application Insights. Для приложения-функции ведение журнала настраивается в файле [Host. JSON] .

### <a name="categories"></a>Категории

В средстве ведения журнала Функций Azure предусмотрена *категория* для каждого журнала. Категория указывает, какая часть кода среды выполнения или кода функции записывала данные в этот журнал. 

Среда выполнения функций создает журналы с категорией, которая начинается с "Host". В версии 1. x журналы `function started`, `function executed` и `function completed` имеют категорию `Host.Executor`. Начиная с версии 2. x, эти журналы имеют категорию `Function.<YOUR_FUNCTION_NAME>`.

Если вы пишете журналы в код функции, категория будет `Function` в версии 1. x среды выполнения функций. В версии 2. x категория имеет значение `Function.<YOUR_FUNCTION_NAME>.User`.

### <a name="log-levels"></a>Уровни журнала

В средстве ведения журнала функций Azure также содержится *уровень ведения журнала* с каждым журналом. Параметр [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) (Уровень ведения журнала) является перечислением целочисленных значений, которые обозначают относительную важность.

|LogLevel    |Код|
|------------|---|
|Трассировка       | 0 |
|Отладка       | 1 |
|Информация | 2 |
|Предупреждение     | 3 |
|Ошибка       | 4 |
|Критический    | 5 |
|Нет        | 6 |

Уровень ведения журнала `None` описан в следующем разделе. 

### <a name="log-configuration-in-hostjson"></a>Конфигурация журнала в Host. JSON

Файл [Host. JSON] определяет, какой объем информации приложение-функция отправляет в журнал Application Insights. В каждой категории вы можете указать минимальный уровень ведения журнала для отправки данных. Существует два примера: первый пример предназначен для [функций среды выполнения версии 2. x](functions-versions.md#version-2x) (.NET Core), а второй пример — для среды выполнения версии 1. x.

### <a name="version-2x"></a>Версия 2.x

В среде выполнения версии 2.x используется [иерархия фильтров для ведения журналов .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

* Для журналов с категорией `Host.Results` или `Function` отправляйте только `Error` и выше для Application Insights. Данные журналов с уровнем `Warning` и ниже игнорируются.
* Все журналы с категорией `Host.Aggregator` отправляются в Application Insights. Уровень ведения журнала `Trace` — это то же, что в некоторых средствах ведения журналов именуется как `Verbose`. В файле [Host. JSON] необходимо использовать `Trace`.
* Для всех остальных журналов в Application Insights отправляются данные с уровнем `Information` и выше.

Значение категории в [Host. JSON] управляет ведением журнала для всех категорий, название которых начинается с аналогичного значения. `Host` в [Host. JSON] управляет ведением журнала для `Host.General`, `Host.Executor`, `Host.Results` и т. д.

Если [Host. JSON] содержит несколько категорий с одинаковым началом строки, сопоставление начинается с более длинных строк. Предположим, что все, что нужно сделать, за исключением `Host.Aggregator` для входа в систему на уровне `Error`, но вы хотите, чтобы `Host.Aggregator` заходило на уровне `Information`:

### <a name="version-2x"></a>Версия 2.x 

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

Чтобы не вести журналы для определенной категории, установите для нее уровень ведения журнала `None`. Журналы с этой категорией не записываются, и на уровне журнала больше нет.

В следующих разделах описаны основные категории журналов, создаваемых средой выполнения. 

### <a name="category-hostresults"></a>Категория Host.Results

Эти журналы отображаются в Application Insights как "запросы" (requests). Они содержат сведения об успешном выполнении или сбое функций.

![Диаграмма запросов](media/functions-monitoring/requests-chart.png)

Все эти журналы записываются на уровне `Information`. При фильтрации по `Warning` или более поздней версии эти данные отображаться не будут.

### <a name="category-hostaggregator"></a>Категория Host.Aggregator

Эти журналы содержат счетчики и средние значения по вызовам функций за [настраиваемый](#configure-the-aggregator) период времени. По умолчанию используется период 30 секунд или 1000 результатов в зависимости от того, что из этого наступит раньше. 

Журналы доступны в таблице **customMetrics** в Application Insights. Примерами могут быть число выполнений, частота успешных операций и длительность.

![Запрос по customMetrics](media/functions-monitoring/custom-metrics-query.png)

Все эти журналы записываются на уровне `Information`. При фильтрации по `Warning` или более поздней версии эти данные отображаться не будут.

### <a name="other-categories"></a>Другие категории

Все журналы для категорий, кроме перечисленных выше, доступны в таблице **трассировок** в Application Insights.

![Запрос по трассировкам](media/functions-monitoring/analytics-traces.png)

Все журналы с категориями, начинающимися с `Host`, записываются средой выполнения функций. Журналы "функция запущена" и "функция завершена" имеют категорию `Host.Executor`. Для успешного выполнения эти журналы имеют уровень `Information`. Исключения записываются на уровне `Error`. Кроме того, среда выполнения создает журналы уровня `Warning`, например очередь сообщений, отправленных в очередь подозрительных сообщений.

Журналы, написанные кодом функции, имеют категорию `Function` и могут быть любым уровнем ведения журнала.

## <a name="configure-the-aggregator"></a>Настройка агрегатора

Как отмечалось в предыдущем разделе, среда выполнения собирает данные о выполнении функции за определенный период времени. По умолчанию используется период в 30 секунд или 1000 запусков в зависимости от того, что из этого наступит раньше. Этот параметр можно настроить в файле [Host. JSON].  Ниже приведен пример:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Настройка выборки

Application Insights имеет функцию [выборки](../azure-monitor/app/sampling.md) , которая может защищать вас от создания слишком большого объема данных телеметрии при завершении выполнения в периоды пиковой нагрузки. Если скорость входящих выполнений превышает заданное пороговое значение, Application Insights начинает случайным образом игнорировать некоторые из входящих выполнений. Значение по умолчанию для максимального числа выполнений в секунду — 20 (пять в версии 1. x). Вы можете настроить выборку в файле [Host. JSON].  Ниже приведен пример:

### <a name="version-2x"></a>Версия 2.x 

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
> [Выборка](../azure-monitor/app/sampling.md) включена по умолчанию. Если отображаются отсутствующие данные, может потребоваться настроить параметры выборки в соответствии с конкретным сценарием мониторинга.

## <a name="write-logs-in-c-functions"></a>Запись журналов в функциях C#

В коде функции вы можете сохранять журналы, которые отображаются в виде трассировок в Application Insights.

### <a name="ilogger"></a>ILogger

Используйте параметр [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) в функциях вместо параметра `TraceWriter`. Журналы, созданные с помощью `TraceWriter` перейдите к Application Insights, но `ILogger` позволяет выполнять [структурированное ведение журнала](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Объект `ILogger` позволяет вызывать для создания журналов [методы расширения ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) `Log<level>`. Следующий код записывает `Information` журналов с функцией Category.

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

Такой метод обработки заполнителей позволяет выполнять структурированное ведение журналов. Application Insights сохраняет пары имя-значение параметра и строку сообщения. Благодаря этому все аргументы сообщения становятся полями, по которым можно выполнять запросы.

Если вызов метода средства ведения журнала выглядит как в предыдущем примере, можно запросить поле `customDimensions.prop__rowKey`. Добавлен префикс `prop__`, чтобы гарантировать отсутствие конфликтов между полями, добавляемыми средой выполнения, и полями, которые добавляет код функции.

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

### <a name="custom-metrics-logging"></a>Ведение журнала пользовательских метрик

В функциях на языке C# вы можете использовать метод расширения `LogMetric` для `ILogger`, чтобы создать пользовательские метрики в Application Insights. Ниже приведен пример вызова метода.

```csharp
logger.LogMetric("TestMetric", 1234);
```

Этот код является альтернативой вызову `TrackMetric` с помощью API Application Insights для .NET.

## <a name="write-logs-in-javascript-functions"></a>Ведение журналов в функциях JavaScript

В функциях Node.js для ведения журналов следует использовать `context.log`. Структурированное ведение журнала не включено.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Ведение журнала пользовательских метрик

При использовании [версии 1. x](functions-versions.md#creating-1x-apps) среды выполнения функций функции node. js могут использовать метод `context.log.metric` для создания пользовательских метрик в Application Insights. В настоящее время этот метод не поддерживается в версии 2. x. Ниже приведен пример вызова метода.

```javascript
context.log.metric("TestMetric", 1234);
```

Этот код является альтернативой вызову `trackMetric` с помощью пакета SDK для Node. js для Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Регистрация пользовательской телеметрии в C# функциях

Вы можете использовать пакет NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) для отправки пользовательских данных телеметрии в Application Insights. В следующем примере C# используется [настраиваемый API телеметрии](../azure-monitor/app/api-custom-events-metrics.md). Пример приведен для библиотеки классов .NET, но код Application Insights в скрипте C# будет точно таким же.

### <a name="version-2x"></a>Версия 2.x

Среда выполнения версии 2.x использует новые функции в Application Insights для выполнения автоматической корреляции данных телеметрии с текущей операцией. Вам не нужно вручную задавать `Id`, `ParentId` или `Name` поля.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

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

Не вызывайте `TrackRequest` или `StartOperation<RequestTelemetry>`, так как вы увидите дублирующиеся запросы на вызов функции.  Среда выполнения Функций Azure автоматически отслеживает запросы.

Не указывайте `telemetryClient.Context.Operation.Id`. Этот глобальный параметр приводит к неправильной корреляции при одновременном выполнении множества функций. Вместо этого создайте экземпляр телеметрии (`DependencyTelemetry`, `EventTelemetry`) и измените его свойство `Context`. Затем передайте экземпляр телеметрии в соответствующий метод `Track` в `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Этот метод гарантирует, что данные телеметрии будет иметь правильные сведения о корреляции для текущего вызова функции.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Регистрация пользовательской телеметрии в функциях JavaScript

Ниже приведен пример фрагмента кода, который отправляет пользовательские данные телеметрии с помощью [пакета SDK для Node. js Application Insights](https://github.com/microsoft/applicationinsights-node.js).

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

Параметр `tagOverrides` задает `operation_Id` для идентификатора вызова функции. Этот параметр позволяет сопоставлять все автоматически создаваемые и пользовательские данные телеметрии с конкретным вызовом функции.

## <a name="dependencies"></a>Зависимости

Функции v2 автоматически собирают зависимости для HTTP-запросов, ServiceBus и SQL.

Можно написать пользовательский код для отображения зависимостей. Примеры см. в примере кода в [ C# разделе Custom телеметрии](#log-custom-telemetry-in-c-functions). Пример кода приводит к *отображению схемы приложения* в Application Insights, которая выглядит как на следующем рисунке:

![Схема сопоставления приложений](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Сообщение о проблемах

Чтобы сообщить о проблеме с интеграцией Функций Azure с Application Insights, внести предложение или отправить запрос, [создайте обращение в GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Журналы потоковой передачи

При разработке приложения часто требуется узнать, какие записи записываются в журналы практически в реальном времени при работе в Azure.

Существует два способа просмотра потока файлов журнала, создаваемых при выполнении функций.

* **Встроенная потоковая передача журналов**. платформа службы приложений позволяет просматривать поток файлов журнала приложения. Это эквивалентно выходным данным, которые отображаются при отладке функций во время [локальной разработки](functions-develop-local.md) , а также при использовании на портале вкладки " **тестирование** ". Отобразятся все данные на основе журнала. Дополнительные сведения см. в разделе [Streaming Logs](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Этот потоковый метод поддерживает только один экземпляр и не может использоваться с приложением под управлением Linux в плане потребления.

* **Live Metrics Stream**. Если приложение-функция [подключена к Application Insights](#enable-application-insights-integration), вы можете просматривать данные журнала и другие метрики практически в реальном времени в портал Azure с помощью [Live Metrics Stream](../azure-monitor/app/live-stream.md). Используйте этот метод при мониторинге функций, выполняемых в нескольких экземплярах или в Linux, в плане потребления. Этот метод использует [данные выборки](#configure-sampling).

Потоки журнала можно просматривать как на портале, так и в большинстве локальных сред разработки. 

### <a name="portal"></a>Microsoft Azure

На портале можно просматривать оба типа потоков журналов.

#### <a name="built-in-log-streaming"></a>Встроенная потоковая передача журналов

Чтобы просмотреть журналы потоковой передачи на портале, выберите вкладку **функции платформы** в приложении функции. Затем в разделе **мониторинг**выберите **потоковая передача журнала**.

![Включение журналов потоковой передачи на портале](./media/functions-monitoring/enable-streaming-logs-portal.png)

При этом приложение будет подключено к службе потоковой передачи журналов, а журналы приложений отобразятся в окне. Можно переключаться между **журналами приложений** и **журналами веб-сервера**.  

![Просмотр журналов потоковой передачи на портале](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Динамический поток метрик

Чтобы просмотреть Live Metrics Stream для приложения, перейдите на вкладку **Обзор** приложения функции. При наличии Application Insights включается ссылка на **Application Insights** в разделе **настроенные компоненты**. Эта ссылка позволяет перейти на страницу Application Insights приложения.

В Application Insights выберите **Live Metrics Stream**. [Записи журнала выборки](#configure-sampling) отображаются в разделе " **Пример телеметрии**".

![Просмотр Live Metrics Stream на портале](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Код Visual Studio

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Основные средства

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Вы можете включить журналы потоковой передачи с помощью [Azure CLI](/cli/azure/install-azure-cli). Используйте следующие команды для входа в систему, выбора подписки и потоковой передачи файлов журнала:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Вы можете включить журналы потоковой передачи с помощью [Azure PowerShell](/powershell/azure/overview). Для PowerShell используйте следующие команды, чтобы добавить учетную запись Azure, выбрать подписку и потоковую передачу файлов журнала:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Отключение встроенного ведения журнала

При включении Application Insights отключите встроенное ведение журнала, которое использует службу хранилища Azure. Встроенное ведение журнала полезно для тестирования с облегченными рабочими нагрузками, но не предназначено для использования в рабочей среде с высокой загрузкой. Для наблюдения за рабочими средами рекомендуется Application Insights. Если в рабочей среде используется встроенное ведение журнала, запись журнала может быть неполной из-за регулирования в службе хранилища Azure.

Чтобы отключить встроенное ведение журнала, удалите параметр приложения `AzureWebJobsDashboard`. Дополнительные сведения о том, как удалять параметры приложения на портале Azure, см. в разделе **Параметры приложения** статьи [Управление приложением-функцией на портале Azure](functions-how-to-use-azure-function-app-settings.md#settings). Перед удалением параметра приложения убедитесь, что ни одна из функций в том же приложении не использует параметр для триггеров или привязок службы хранилища Azure.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Application Insights](/azure/application-insights/)
* [Общие сведения о ведении журналов в ASP.NET Core](/aspnet/core/fundamentals/logging/)

[Host. JSON]: functions-host-json.md
