---
title: Мониторинг Функций Azure
description: Сведения об использовании Azure Application Insights с помощью функций Azure для мониторинга выполнения функции.
services: functions
author: ggailey777
manager: jeconnoc
keywords: функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: glenga
ms.openlocfilehash: e9e47eff3df941b0c1437083dc7440fab4091418
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317074"
---
# <a name="monitor-azure-functions"></a>Мониторинг Функций Azure

[Функции Azure](functions-overview.md) предлагает встроенную интеграцию с [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) для мониторинга функций. В этой статье показано, как настроить функции Azure, чтобы отправлять файлы системных журналов в Application Insights.

![Обозреватель метрик в Application Insights](media/functions-monitoring/metrics-explorer.png)

Функции Azure также имеет [встроенные функции мониторинга, не используется Application Insights](#monitoring-without-application-insights). Мы рекомендуем использовать службу Application Insights, которая предоставляет дополнительные данные и больше возможностей для их анализа.

## <a name="application-insights-pricing-and-limits"></a>Стоимость и ограничения Application Insights

Вы можете протестировать интеграцию Application Insights с приложением-функцией бесплатно. Нет ежедневное ограничение объема данных, которые могут обрабатываться бесплатно. Вы можете достигнуть предела во время тестирования. Azure отправляет уведомления на портале и по электронной почте при приближении к ежедневному ограничению. Если вы пропустите эти предупреждения и достигла предела, новые журналы не будет отображаться в запросах Application Insights. Следует учитывать ограничения, чтобы избежать ненужных устранять неполадки. Дополнительные сведения см. в статье [Управление ценами и объемом данных в Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Включение интеграции с Application Insights

Для отправки данных в Application Insights приложению-функции требуется ключ инструментирования для ресурса Application Insights. В параметре приложения должен быть ключ с именем **APPINSIGHTS_INSTRUMENTATIONKEY**.

Это подключение можно настроить на [портале Azure](https://portal.azure.com):

* [Автоматически подключаться приложение-функцию](#new-function-app)
* [Подключение ресурса Application Insights вручную](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Новое приложение-функция
<!-- Add a transitional sentence to introduce the procedure. -->

1. Перейдите на страницу **Создание** приложения-функции.

1. Установите переключатель **Application Insights** в состояние **Включено**.

1. Выберите **расположение Application Insights**. Выберите регион, ближайший к области приложения-функции и в [географического региона Azure](https://azure.microsoft.com/global-infrastructure/geographies/) место для хранения данных.

   ![Включение Application Insights при создании приложения-функции](media/functions-monitoring/enable-ai-new-function-app.png)

1. Введите другие необходимые сведения и щелкните **Создать**.

Следующим шагом является [отключение встроенного ведения журнала](#disable-built-in-logging).


<a id="manually-connect-an-app-insights-resource"></a>
### <a name="application-insights-resource"></a>Ресурс Application Insights 
<!-- Add a transitional sentence to introduce the procedure. -->

1. Создайте ресурс Application Insights. Задайте тип приложения **Общие**.

   ![Создайте ресурс Application Insights типа Общие](media/functions-monitoring/ai-general.png)

1. Скопируйте ключ инструментирования на странице **Основные компоненты** в ресурсе Application Insights. Указатель на конец отображаемого значения ключа для получения **щелкните, чтобы скопировать** кнопки.

   ![Копирование ключа инструментирования Application Insights](media/functions-monitoring/copy-ai-key.png)

1. В приложении-функции **параметры приложения** странице [добавьте параметр приложения](functions-how-to-use-azure-function-app-settings.md#settings) , выбрав **добавить новый параметр**. Присвойте имя новому параметру **APPINSIGHTS_INSTRUMENTATIONKEY** и вставьте скопированный ключ инструментирования.

   ![Добавление ключа инструментирования в настройках приложения](media/functions-monitoring/add-ai-key.png)

1. Щелкните **Сохранить**.

<!-- Before the next H2 heading, add transitional sentences to summarize why the procedures were necessary. -->

## <a name="disable-built-in-logging"></a>Отключение встроенного ведения журнала

При включении Application Insights, отключите [встроенное ведение журнала, который использует хранилище Azure](#logging-to-storage). Встроенное ведение журнала полезно для тестирования с легкими рабочими нагрузками, но не предназначена для использования в рабочей среде с высокой нагрузкой. Для мониторинга рабочей среды рекомендуется Application Insights. Если встроенное ведение журнала используется в рабочей среде, записи журнала могут быть неполными из-за регулирования службы хранилища Azure.

Чтобы отключить встроенное ведение журнала, удалите параметр приложения `AzureWebJobsDashboard`. Дополнительные сведения о том, как удалять параметры приложения на портале Azure, см. в разделе **Параметры приложения** статьи [Управление приложением-функцией на портале Azure](functions-how-to-use-azure-function-app-settings.md#settings). Прежде чем удалить параметр приложения, убедитесь, что нет существующих функций в одном приложении-функции используется параметр для триггеров хранилища Azure или привязок.

## <a name="view-telemetry-in-monitor-tab"></a>Просмотр телеметрии на вкладке "Монитор"

После настройки интеграции Application Insights как показано в предыдущих разделах, можно просмотреть данные телеметрии в **монитор** вкладки.

1. На странице приложения-функции выберите функцию, которая запущена хотя бы один раз после настройки Application Insights. Затем выберите **монитор** вкладки.

   ![Переход на вкладку мониторинга](media/functions-monitoring/monitor-tab.png)

1. Выберите **обновить** периодически, пока не появится список вызовов функций.

   Может занять до пяти минут для списка, чтобы открывается, когда клиент телеметрии упаковывает данные для передачи на сервер. (Задержка не применяется к [Live Stream метрики](../azure-monitor/app/live-stream.md). Эта служба подключается к узлу решения "Функции" при загрузке страницы, поэтому журналы передаются напрямую на страницу.)

   ![Список вызовов](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Чтобы просмотреть журналы для вызова определенной функции, выберите ссылку на столбец **Дата** для этого вызова.

   ![Ссылка на сведения о вызове](media/functions-monitoring/invocation-details-link-ai.png)

   Выходные данные ведения журнала для этого вызова отображаются на новой странице.

   ![Сведения о вызове](media/functions-monitoring/invocation-details-ai.png)

Обе страницы (список вызова и подробности о вызове) свяжите запрос аналитики Application Insights, который получает данные.

![Выполнение в Application Insights](media/functions-monitoring/run-in-ai.png)

![Список вызовов в аналитике Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Из этих запросов вы увидите, что список вызовов ограничено до последнего 30 дней. В списке отображается не более чем 20 строк (`where timestamp > ago(30d) | take 20`). Список сведений о вызовов — за последние 30 дней без ограничения.

Дополнительные сведения см. в разделе [Запросы к данным телеметрии](#query-telemetry-data) далее в этой статье.

## <a name="view-telemetry-in-application-insights"></a>Просмотр данных телеметрии в Application Insights

Чтобы открыть Application Insights из приложения-функции на портале Azure, перейдите к приложению-функции **Обзор** страницы. В разделе **настройки компонентов**выберите **Application Insights**.

![Откройте Application Insights на странице обзора приложения функции](media/functions-monitoring/ai-link.png)

Дополнительные сведения об использовании Application Insights см. в [документации по Application Insights](https://docs.microsoft.com/azure/application-insights/). В этом разделе представлено несколько примеров просмотра данных в Application Insights. Если вы уже знакомы с Application Insights, вы можете перейти непосредственно на [разделы о том, как настроить данные телеметрии,](#configure-categories-and-log-levels).

В [обозревателя метрик](../azure-monitor/app/metrics-explorer.md), можно создавать диаграммы и оповещения, которые основаны на метриках. Метрики включают количество вызовов функции времени выполнения и частота успешных выполнений.

![Обозреватель метрик](media/functions-monitoring/metrics-explorer.png)

На вкладке [Сбои](../azure-monitor/app/asp-net-exceptions.md) можно создавать диаграммы и оповещения на основе сбоев функции и исключений сервера. **Имя операции** обозначает имя функции. Сбои в зависимостях не отображаются, если вы реализуете [пользовательские данные телеметрии](#custom-telemetry-in-c-functions) для зависимостей.

![Сбои](media/functions-monitoring/failures.png)

На вкладке [Производительность](../azure-monitor/app/performance-counters.md) вы можете анализировать проблемы производительности.

![Производительность](media/functions-monitoring/performance.png)

Вкладка **Серверы** отображает использование ресурсов и пропускную способность для каждого сервера. Эти данные можно использовать для отладки в тех случаях, когда функции создают чрезмерную нагрузку на базовые ресурсы. Серверы здесь называются **экземплярами облачных ролей**.

![Серверы](media/functions-monitoring/servers.png)

[Live Stream метрики](../azure-monitor/app/live-stream.md) вкладке отображаются данные метрик, так как оно создается в режиме реального времени.

![Live Stream](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Запросы к данным телеметрии

[Аналитика Application Insights](../azure-monitor/app/analytics.md) вы получаете доступ ко всем данным телеметрии в виде таблиц в базе данных. Функция аналитики поддерживает язык запросов для извлечения, обработки и визуализации данных.

![Выбор функции аналитики](media/functions-monitoring/select-analytics.png)

![Пример данных аналитики](media/functions-monitoring/analytics-traces.png)

В этом примере показано распределение запросов по рабочим ролям за последние 30 минут.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Доступные таблицы отображаются в **схемы** вкладка в левой части. Данные, создаваемые при вызовах функций, вы найдете в следующих таблицах:

* **трассировки**: Журналы, созданные средой выполнения и кодом функций.
* **Запросы**: Один запрос для каждого вызова функции.
* **исключения**: Все исключения, возникшие при выполнении.
* **customMetrics**: Число успешных и неудачных вызовов, Доля успешных попыток и длительность.
* **customEvents**: События, отслеживаемые средой выполнения, например: HTTP-запросы, которые активируют функции;
* **performanceCounters**: Сведения о производительности серверов, на которых выполняются функции на.

Остальные таблицы предназначены для тестов доступности, а также телеметрии клиента и браузера. Вы можете реализовать пользовательскую телеметрию, чтобы добавлять в них данные.

В каждой таблице есть поле `customDimensions`, где хранится часть данных о конкретной функции.  Например, следующий запрос получает все трассировки с уровнем журнала `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Среда выполнения предоставляет `customDimensions.LogLevel` и `customDimensions.Category` поля. Вы можете указать дополнительные поля в журналах, которые были написаны в коде функции. См. раздел [Структурированное ведение журнала](#structured-logging) далее в этой статье.

## <a name="configure-categories-and-log-levels"></a>Настройка категорий и уровней ведения журнала

Application Insights можно использовать без пользовательские настройки. Конфигурация по умолчанию можно привести большие объемы данных. Если вы используете подписку Azure для Visual Studio, объем данных Application Insights может достигнуть установленного верхнего предела. Далее в этой статье вы узнаете, как настроить и настроить данные, отправляемые функциями в Application Insights.

### <a name="categories"></a>Категории

В средстве ведения журнала Функций Azure предусмотрена *категория* для каждого журнала. Категория указывает, какая часть кода среды выполнения или кода функции записывала данные в этот журнал. 

Среда выполнения функций создает журналы с помощью категории, которые начинаются с «Host». «Function started,» «функция выполняется» и журналы «функции завершения» относятся к категории «Host.Executor». 

Если журналы записываются в коде функции, ему присваивается категория «Функция».

### <a name="log-levels"></a>Уровни журнала

Средство ведения журнала функций Azure также включает *уровень ведения журнала* каждого журнала. Параметр [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) (Уровень ведения журнала) является перечислением целочисленных значений, которые обозначают относительную важность.

|LogLevel    |Код|
|------------|---|
|Трассировка       | 0 |
|Отладка       | 1 |
|Информация | 2 |
|Предупреждение     | 3 |
|Ошибка       | 4. |
|критические ошибки.    | 5 |
|Нет        | 6 |

Уровень ведения журнала `None` описан в следующем разделе. 

### <a name="log-configuration-in-hostjson"></a>Конфигурация журналов в host.json

Файл [host.json](functions-host-json.md) определяет, какой объем информации приложение-функция отправляет в журнал Application Insights. В каждой категории вы можете указать минимальный уровень ведения журнала для отправки данных. Существует два примера: первый пример ориентируется [среда выполнения функций версии 2.x](functions-versions.md#version-2x) (.NET Core), а во втором примере — для среды выполнения версии 1.x.

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

* Для журналов с категорией `Host.Results` или `Function`, только отправка `Error` уровень и более поздних версий в Application Insights. Данные журнала с уровнем `Warning` и ниже игнорируются.
* Все журналы с категорией `Host.Aggregator` отправляются в Application Insights. Уровень ведения журнала `Trace` — это то же, что в некоторых средствах ведения журналов именуется как `Verbose`. В файле [host.json](functions-host-json.md) необходимо использовать `Trace`.
* Для всех остальных журналов в Application Insights отправляются данные с уровнем `Information` и выше.

Значение категории в [host.json](functions-host-json.md) управляет ведением журнала для всех категорий, название которых начинается с аналогичного значения. `Host` в [host.json](functions-host-json.md) ведения журнала для элементов управления `Host.General`, `Host.Executor`, `Host.Results`, и т. д.

Если [host.json](functions-host-json.md) содержит несколько категорий с одинаковым началом строки, сопоставление начинается с более длинных строк. Предположим, что вы хотите регистрировать все данные среды выполнения, кроме `Host.Aggregator` ведения журнала `Error` уровень, но требуется `Host.Aggregator` ведения журнала `Information` уровень:

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

Чтобы не вести журналы для определенной категории, установите для нее уровень ведения журнала `None`. Журналы не записываются с этой категорией, и нет уровень ведения журнала не над ним.

В следующих разделах описаны основные категории журналов, создаваемых средой выполнения. 

### <a name="category-hostresults"></a>Категория Host.Results

Эти журналы отображаются в Application Insights как "запросы" (requests). Они содержат сведения об успешном выполнении или сбое функций.

![Диаграмма запросов](media/functions-monitoring/requests-chart.png)

Все эти журналы ведутся на `Information` уровень. При фильтрации по `Warning` или более поздней версии, вы не увидите никакие данные.

### <a name="category-hostaggregator"></a>Категория Host.Aggregator

Эти журналы содержат счетчики и средние значения по вызовам функций за [настраиваемый](#configure-the-aggregator) период времени. По умолчанию используется период 30 секунд или 1000 результатов в зависимости от того, что из этого наступит раньше. 

Журналы доступны в таблице **customMetrics** в Application Insights. Примеры: количество выполнений, Доля успешных попыток и длительность.

![Запрос по customMetrics](media/functions-monitoring/custom-metrics-query.png)

Все эти журналы ведутся на `Information` уровень. При фильтрации по `Warning` или более поздней версии, вы не увидите никакие данные.

### <a name="other-categories"></a>Другие категории

Все журналы для категорий, кроме перечисленных выше, доступны в таблице **трассировок** в Application Insights.

![Запрос по трассировкам](media/functions-monitoring/analytics-traces.png)

Все журналы с категориями, которые начинаются с `Host` записываются в среде выполнения функций. «Функция started» и «Function завершена» журналы имеют категорию `Host.Executor`. Для успешного выполнения эти журналы являются `Information` уровень. Исключения заносятся в `Error` уровень. Кроме того, среда выполнения создает журналы уровня `Warning`, например очередь сообщений, отправленных в очередь подозрительных сообщений.

Журналы, сохраняемые в коде функции имеют категорию `Function` и может быть любой уровень ведения журнала.

## <a name="configure-the-aggregator"></a>Настройка агрегатора

Как отмечалось в предыдущем разделе, среда выполнения собирает данные о выполнении функции за определенный период времени. По умолчанию используется период в 30 секунд или 1000 запусков в зависимости от того, что из этого наступит раньше. Этот параметр можно настроить в файле [host.json](functions-host-json.md).  Ниже приведен пример:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Настройка выборки

В Application Insights есть функция [выборки](../azure-monitor/app/sampling.md), которая позволят избежать создания слишком большого объема данных телеметрии в периоды пиковой нагрузки. Если скорость входящей телеметрии превышает заданное пороговое значение, служба Application Insights будет случайным образом игнорировать часть поступающих элементов. Максимальное количество элементов в секунду по умолчанию равно пяти. Вы можете настроить выборку в файле [host.json](functions-host-json.md).  Ниже приведен пример:

### <a name="version-2x"></a>Версия 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 5
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
> [Выборка](../azure-monitor/app/sampling.md) включена по умолчанию. Если вы будут отображаться данные, может потребоваться настроить параметры выборки для своего конкретного сценария мониторинга.

## <a name="write-logs-in-c-functions"></a>Запись журналов в функциях C#

В коде функции вы можете сохранять журналы, которые отображаются в виде трассировок в Application Insights.

### <a name="ilogger"></a>ILogger

Используйте параметр [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) в функциях вместо параметра `TraceWriter`. Журналы, созданные с помощью `TraceWriter` перейдите в Application Insights, но `ILogger` позволяет сделать [структурированное ведение журнала](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Объект `ILogger` позволяет вызывать для создания журналов [методы расширения ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) `Log<level>`. Следующий код записывает `Information` журналов с категорией «Функция».

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

Такой метод обработки заполнителей позволяет выполнять структурированное ведение журналов. Application Insights сохраняет пары имя значение параметра и строку сообщения. Благодаря этому все аргументы сообщения становятся полями, по которым можно выполнять запросы.

Если вызов метода средства ведения журнала выглядит как в предыдущем примере, можно запросить поле `customDimensions.prop__rowKey`. `prop__` Добавляется префикс, чтобы не возникало конфликтов между полями, среда выполнения добавляет и код вашей функции добавляет.

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

Этот код является альтернативой вызову `TrackMetric` с помощью [API Application Insights для .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Ведение журналов в функциях JavaScript

В функциях Node.js для ведения журналов следует использовать `context.log`. Структурированное ведение журнала не включено.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Ведение журнала пользовательских метрик

Если вы выполняете [версии 1.x](functions-versions.md#creating-1x-apps) среды выполнения функций, можно использовать функции Node.js `context.log.metric` метод, чтобы создать пользовательские метрики в Application Insights. Этот метод не поддерживается в версии 2.x. Ниже приведен пример вызова метода.

```javascript
context.log.metric("TestMetric", 1234);
```

Этот код является альтернативой вызову `trackMetric` с помощью [пакета SDK Node.js для Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="log-custom-telemetry-in-c-functions"></a>Пользовательские данные телеметрии войдите C# функции

Вы можете использовать пакет NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) для отправки пользовательских данных телеметрии в Application Insights. В следующем примере C# используется [настраиваемый API телеметрии](../azure-monitor/app/api-custom-events-metrics.md). Пример приведен для библиотеки классов .NET, но код Application Insights в скрипте C# будет точно таким же.

### <a name="version-2x"></a>Версия 2.x

Среда выполнения версии 2.x использует новые функции в Application Insights для выполнения автоматической корреляции данных телеметрии с текущей операцией. Нет необходимости вручную задать операцию `Id`, `ParentId`, или `Name` полей.

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
    public static class HttpTrigger2
    {
        // In Functions v2, TelemetryConfiguration.Active is initialized with the InstrumentationKey
        // from APPINSIGHTS_INSTRUMENTATIONKEY. Creating a default TelemetryClient like this will 
        // automatically use that key for all telemetry. It will also enable telemetry correlation
        // with the current operation.
        // If you require a custom TelemetryConfiguration, create it initially with
        // TelemetryConfiguration.CreateDefault() to include this automatic correlation.
        private static TelemetryClient telemetryClient = new TelemetryClient();

        [FunctionName("HttpTrigger2")]
        public static Task<IActionResult> Run(
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
            telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
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
            dependency.Context.User.Id = name;
            telemetryClient.TrackDependency(dependency);

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

Не вызывайте `TrackRequest` или `StartOperation<RequestTelemetry>` так, как вы увидите повторные запросы для вызова функции.  Среда выполнения Функций Azure автоматически отслеживает запросы.

Не указывайте `telemetryClient.Context.Operation.Id`. Этот глобальный параметр вызывает неправильное корреляции, при одновременном выполнении многих функций. Вместо этого создайте экземпляр телеметрии (`DependencyTelemetry`, `EventTelemetry`) и измените его свойство `Context`. Затем передайте экземпляр телеметрии в соответствующий метод `Track` в `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Этот метод гарантирует, что данные телеметрии имеет правильный корреляции для текущего вызова функции.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Журнал пользовательские данные телеметрии в функциях JavaScript

[Пакет SDK Node.js для Application Insights](https://www.npmjs.com/package/applicationinsights) сейчас доступен в бета-версии. Ниже приведен пример кода, который отправляет пользовательские данные телеметрии в Application Insights.

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

`tagOverrides` Наборов параметров `operation_Id` для ИД вызова функции. Этот параметр позволяет сопоставлять все автоматически создаваемые и пользовательские данные телеметрии с конкретным вызовом функции.

## <a name="known-issues"></a>Известные проблемы
<!-- Add a transitional sentence to introduce the section. -->

### <a name="dependencies"></a>Зависимости

Зависимости, которые имеют функции для других служб не отображаются автоматически. Можно написать пользовательский код, чтобы показать зависимости. Примеры см. в образце кода в [ C# раздел пользовательской телеметрии](#custom-telemetry-in-c-functions). Пример кода приводит к *схема сопоставления приложений* в Application Insights, который выглядит, как ниже:

![Схема сопоставления приложений](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Сообщение о проблемах

Чтобы сообщить о проблеме с интеграцией Функций Azure с Application Insights, внести предложение или отправить запрос, [создайте обращение в GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitor-without-application-insights"></a>Монитор без Application Insights

Мы рекомендуем использовать Application Insights для мониторинга функций. Он предлагает дополнительные данные и больше возможностей для анализа данных. Но если вы предпочитаете системы встроенное ведение журнала, который использует службу хранилища Azure, можно продолжать использовать этот метод.

### <a name="azure-storage-account-for-logging"></a>Учетная запись хранения Azure для ведения журнала

Встроенное ведение журнала использует учетную запись хранения, указанную в строке подключения в параметре приложения `AzureWebJobsDashboard`. На странице приложения-функции выберите функцию, а затем выберите **монитор** вкладке и сохраните его **классический вид**.

![Переход к классическому представлению](media/functions-monitoring/switch-to-classic-view.png)

Отобразится список выполнений функции. Щелкнув конкретное выполнение, можно просмотреть его длительность, входные данные, ошибки и связанные файлы журнала.

Если вы включили Application Insights, можно отправлять с помощью встроенного ведения журнала. Отключить Application Insights вручную, а затем выберите **монитор** вкладки. Чтобы отключить интеграцию с Application Insights, удалите `APPINSIGHTS_INSTRUMENTATIONKEY` параметр приложения.

Даже если на вкладке **Монитор** отображаются данные Application Insights, вы можете просмотреть данные журнала в файловой системе, если [встроенное ведение журнала](#disable-built-in-logging) не отключено. В ресурсе хранилища перейдите к **файлы**и выберите службу файлов для функции. Затем перейдите к разделу **LogFiles** > **приложения** > **функции** > **функция**  >  **your_function** для просмотра файла журнала.

### <a name="real-time-monitoring"></a>Мониторинг в реальном времени

Можно осуществлять потоковую передачу файлов журнала в сеанс командной строки на локальной рабочей станции. Используйте [Azure интерфейс командной строки (CLI)](/cli/azure/install-azure-cli) или [Azure PowerShell](/powershell/azure/overview).  

Для Azure CLI используйте следующие команды, чтобы войти, выбрать подписку и включить потоковую передачу файлов журнала:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

Для Azure PowerShell используйте следующие команды, чтобы добавить учетную запись Azure, выбрать подписку и включить потоковую передачу файлов журнала:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <function app name> -Tail
```

Дополнительные сведения см. в разделе [Практическое руководство. Потоковая передача журналов](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Application Insights](/azure/application-insights/)
* [Общие сведения о ведении журналов в ASP.NET Core](/aspnet/core/fundamentals/logging/)
