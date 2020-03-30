---
title: Мониторинг приложений Python с azure Monitor (предварительный просмотр) Документы Майкрософт
description: Предоставляет инструкции для провода OpenCensus Python с Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537114"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Настройка Azure Monitor для приложения Python

Azure Monitor поддерживает распределенный отслеживание, сбор метрик и регистрацию приложений Python через интеграцию с [OpenCensus.](https://opencensus.io) В этой статье вы проведете процесс настройки OpenCensus для Python и отправки данных мониторинга в Azure Monitor.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Установка python. Эта статья использует [Python 3.7.0](https://www.python.org/downloads/), хотя более ранние версии, скорее всего, будет работать с незначительными изменениями.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Создание ресурса Insights приложений в Azure Monitor

Сначала необходимо создать ресурс Application Insights в Azure Monitor, который будет генерировать ключ приборов (ikey). Затем ikey используется для настройки OpenCensus SDK для отправки телеметрических данных в Azure Monitor.

1. Выберите **Создать ресурс** > **Разработчик инструментов** > **Application Insights**.

   ![Добавление ресурса «Исследования приложений»](./media/opencensus-python/0001-create-resource.png)

1. Отображается окно конфигурации. Используйте следующую таблицу для заполнения входных полей.

   | Параметр        | Значение           | Описание  |
   | ------------- |:-------------|:-----|
   | **Название**      | Глобально уникальное значение | Имя, идентифицирующее отслеживаемое приложение |
   | **Ресурсная группа**     | myResourceGroup      | Название для новой группы ресурсов для размещения данных Application Insights |
   | **Расположение** | Восточная часть США | Место рядом с вами или рядом с местом размещения приложения |

1. Выберите **Создать**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Инструмент с OpenCensus Python SDK для Azure Monitor

Установите экспортеров OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

Полный список пакетов и интеграций можно узнать в [пакетах OpenCensus.](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)

> [!NOTE]
> Команда `python -m pip install opencensus-ext-azure` предполагает, что для `PATH` установки Python у вас есть переменная среды. Если вы еще не настроили эту переменную, необходимо дать полный путь каталога к месту, где находится ваш Python, исполняемый. Результатом является команда, `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`как это: .

SDK использует трех экспортеров Azure Monitor для отправки различных типов телеметрии в Azure Monitor: трассировку, метрики и журналы. Для получения дополнительной информации об этих типах телеметрии [см.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) Используйте следующие инструкции для отправки этих типов телеметрии через трех экспортеров.

## <a name="telemetry-type-mappings"></a>Отображение типа телеметрии

Вот экспортеры, которые OpenCensus предоставляет отображены к типам телеметрии, которые вы увидите в Azure Monitor.

![Скриншот отображения типов телеметрии от OpenCensus до Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Трассировка

> [!NOTE]
> `Trace`в OpenCensus относится к [распределенной трассировки](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). Отправки `AzureExporter` `requests` и `dependency` телеметрия в Azure Monitor.

1. Во-первых, давайте создаем некоторые данные о следах локально. В Python IDLE, или ваш редактор по выбору, введите следующий код.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. При выполнении кода вам несколько раз будет предложено ввести значение. С каждой записью значение будет напечатано на оболочке, а модуль `SpanData`OpenCensus Python будет генерировать соответствующую часть . Проект OpenCensus определяет [трассировку в виде дерева диапазонов](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Хотя ввод значений полезен для демонстрационных целей, в конечном итоге мы хотим `SpanData` излучать в Azure Monitor. Передайте строку соединения непосредственно экспортеру, или вы `APPLICATIONINSIGHTS_CONNECTION_STRING`можете указать ее в переменной среды. Измените код с предыдущего шага на основе следующего образца кода:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Теперь, когда вы запустите сценарий Python, вам все равно следует ввести значения, но в оболочке печатается только значение. Созданный `SpanData` будет отправлен в Azure Monitor. Вы можете найти испускаемые данные диапазона под `dependencies`. Для получения более подробной информации об исходящих [запросах](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)см.
Для получения более подробной информации о [requests](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)входящих запросах см.

#### <a name="sampling"></a>Выборка

Для получения информации о выборке в OpenCensus, взгляните на [выборку в OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Корреляция трассировки

Для получения подробной информации о телеметрии корреляции в ваших данных трассировки, взгляните на OpenCensus Python [телеметрии корреляции](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python).

#### <a name="modify-telemetry"></a>Изменение телеметрии

Подробную информацию о том, как изменять гусеничной телеметрии перед отправкой в Azure Monitor, можно узнать об [процессорах телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python.

### <a name="metrics"></a>Метрики

1. Во-первых, давайте создаем некоторые локальные метрические данные. Мы создадим простую метрику для отслеживания количества нажатий пользователя Enter.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. Запуск кода будет неоднократно побуждать вас нажимать Enter. Для отслеживания количества нажатий Enter создается метрика. С каждой записью значение будет приравливать и метрическая информация будет отображаться в консоли. Информация включает текущее значение и текущую отметку времени при обновлении метрики.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Хотя ввод значений полезен для демонстрационных целей, в конечном итоге мы хотим излучать метрические данные в Azure Monitor. Передайте строку соединения непосредственно экспортеру, или вы `APPLICATIONINSIGHTS_CONNECTION_STRING`можете указать ее в переменной среды. Измените код с предыдущего шага на основе следующего образца кода:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. Экспортер будет отправлять метрические данные в Azure Monitor с фиксированным интервалом. По умолчанию каждые 15 секунд. Мы отслеживаем одну метрику, так что эти метрические данные, с любым значением и временным штампом, который он содержит, будут отправляться с каждым интервалом. Вы можете найти `customMetrics`данные под .

#### <a name="standard-metrics"></a>Стандартные метрики

По умолчанию экспортер метрик отправит набор стандартных метрик в Azure Monitor. Вы можете отключить это, `enable_standard_metrics` установив `False` флаг в конструкторе экспортера метрик.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Ниже приведен список стандартных метрик, которые в настоящее время отправлены:

- Доступная память (байты)
- Время процессора процессора (в процентах)
- Входящие Коэффициент запроса (в секунду)
- Среднее время выполнения запроса (миллисекунды)
- Исходящие коэффициенты запроса (в секунду)
- Использование процессора процесса (в процентах)
- Процесс частных байтов (байты)

Вы должны быть в состоянии `performanceCounters`видеть эти метрики в . Входящие ставки запроса будет под `customMetrics`. Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)

#### <a name="modify-telemetry"></a>Изменение телеметрии

Подробную информацию о том, как изменять гусеничной телеметрии перед отправкой в Azure Monitor, можно узнать об [процессорах телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python.

### <a name="logs"></a>Журналы

1. Во-первых, давайте создаем некоторые локальные данные журнала.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  Код будет постоянно запрашивать значение, подносиве себя. Запись журнала испускается для каждого введенного значения.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Хотя ввод значений полезен для демонстрационных целей, в конечном итоге мы хотим излучать данные журнала в Azure Monitor. Передайте строку соединения непосредственно экспортеру, или вы `APPLICATIONINSIGHTS_CONNECTION_STRING`можете указать ее в переменной среды. Измените код с предыдущего шага на основе следующего образца кода:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. Экспортер отправит данные журнала в Azure Monitor. Вы можете найти `traces`данные под . 

> [!NOTE]
> `traces`в этом контексте не `Tracing`то же самое, что . `traces`относится к типу телеметрии, которую вы увидите `AzureLogHandler`в Azure Monitor при использовании . `Tracing`относится к концепции в OpenCensus и относится к [распределенной трассировки](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Для формата сообщений журнала `formatters` можно использовать встроенный [API регистрации](https://docs.python.org/3/library/logging.html#formatter-objects)Python.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. Вы также можете добавить пользовательские свойства в свои сообщения журнала в *дополнительном* аргументе ключевого слова, используя поле custom_dimensions. Они будут отображаться в виде `customDimensions` пар с ключевым значением в Azure Monitor.
> [!NOTE]
> Чтобы эта функция работала, необходимо передать словарь в поле custom_dimensions. Если вы передаете аргументы любого другого типа, регистратор будет игнорировать их.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="sending-exceptions"></a>Отправка исключений

OpenCensus Python не отслеживает `exception` автоматически и не отправляет телеметрию. Они отправляются `AzureLogHandler` через с помощью исключений через библиотеку регистрации Python. Вы можете добавить пользовательские свойства так же, как с обычной регистрации.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
        logger.exception('Captured an exception.', extra=properties)
    ```
Поскольку необходимо явно регистрировать исключения, пользователь должен входить в систему необработанного исключения. OpenCensus не накладывал ограничений на то, как пользователь хочет это сделать, если он явно регистрирует телеметрию исключения.

#### <a name="sampling"></a>Выборка

Для получения информации о выборке в OpenCensus, взгляните на [выборку в OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Корреляция журналов

Подробную информацию о том, как обогатить журналы данными о контексте трассировки, можно найти [интеграцию журналов](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)OpenCensus Python.

#### <a name="modify-telemetry"></a>Изменение телеметрии

Подробную информацию о том, как изменять гусеничной телеметрии перед отправкой в Azure Monitor, можно узнать об [процессорах телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python.

## <a name="view-your-data-with-queries"></a>Просмотр данных с помощью запросов

Данные телеметрии, отправленные из приложения, можно просмотреть через вкладку **«Логи »Аналитика».**

![Скриншот панели обзора с "Логи (аналитика)" выбран в красной коробке](./media/opencensus-python/0010-logs-query.png)

В списке под **Active**:

- Для телеметрии, отправленной с экспортером отслеживания `requests`Azure Monitor, входящие запросы отображаются под . Исходящие или в процессе `dependencies`запросы отображаются под .
- Для телеметрии, отправленной с экспортером метрик `customMetrics`Azure Monitor, отправленные метрики отображаются под .
- Для телеметрии, отправленной с экспортером журналов `traces`Azure Monitor, журналы отображаются под . Исключения отображаются под `exceptions`.

Более подробную информацию об использовании запросов и журналов можно узнать [в журнале Журналы Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)

## <a name="learn-more-about-opencensus-for-python"></a>Подробнее об OpenCensus для Python

* [Открытый питон на GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Настройка](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Экспортеры Azure Monitor на GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Интеграция открытой переписи](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Примеры приложений Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Дальнейшие действия

* [Отслеживание входящих запросов](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Отслеживание исходящих запросов](./../../azure-monitor/app/opencensus-python-request.md)
* [Карта приложения](./../../azure-monitor/app/app-map.md)
* [Поиск и диагностика проблем производительности с помощью Azure Application Insights](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>видны узлы

* [Тесты доступности.](../../azure-monitor/app/monitor-web-app-availability.md) Создавайте тесты, позволяющие проверить, доступен ли ваш сайт в Интернете.
* [Интеллектуальная диагностика.](../../azure-monitor/app/proactive-diagnostics.md) Эти тесты выполняются автоматически, поэтому вам не нужно их настраивать. Благодаря ей вы узнаете о необычном количестве неудачных запросов.
* [Метрические оповещения:](../../azure-monitor/app/alerts.md)Установите оповещения, чтобы предупредить вас, если метрика пересекает порог. Их можно настроить для пользовательских метрик, добавляемых в код приложения.
