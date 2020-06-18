---
title: Мониторинг приложений Python с помощью Azure Monitor (предварительная версия) | Документация Майкрософт
description: Содержит инструкции по подключению OpenCensus для Python к Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6b8343d08962d8ce749e1160b0226b68571571f8
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815729"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Настройка Azure Monitor для приложения Python

Azure Monitor теперь поддерживает распределенную трассировку, сбор метрик и ведение журнала приложений Python за счет интеграции с [OpenCensus](https://opencensus.io). Эта статья содержит пошаговое описание процесса установки OpenCensus для Python и отправки данных мониторинга в Azure Monitor.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Установка Python. В этой статье идет речь [Python 3.7.0](https://www.python.org/downloads/), хотя более ранние версии, скорее всего, будут работать с незначительными изменениями.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Создание ресурса Application Insights в Azure Monitor

Сначала необходимо создать ресурс Application Insights в Azure Monitor, который создаст ключ инструментирования (ikey). Затем ikey используется, чтобы настроить пакет SDK OpenCensus для отправки данных телеметрии в Azure Monitor.

1. Последовательно выберите **Создать ресурс** > **Средства разработчика** > **Application Insights**.

   ![Добавление ресурса Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Появится окно настроек. Используйте таблицу ниже для заполнения полей ввода.

   | Параметр        | Значение           | Описание  |
   | ------------- |:-------------|:-----|
   | **имя**;      | Глобально уникальное значение | Имя, идентифицирующее отслеживаемое приложение |
   | **Группа ресурсов**     | myResourceGroup      | Имя новой группы ресурсов для размещения данных Application Insights |
   | **Расположение** | Восточная часть США | Ближайшее к вам расположение или расположение рядом с местом размещения приложения |

1. Нажмите кнопку **создания**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Инструментирование с помощью пакета SDK OpenCensus для Python для Azure Monitor

Установите средства экспорта OpenCensus Azure Monitor.

```console
python -m pip install opencensus-ext-azure
```

Полный список пакетов и интеграций см. в разделе [Пакеты OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Команда `python -m pip install opencensus-ext-azure` предполагает, что для установки Python у вас задана переменная среды `PATH`. Если эта переменная не настроена, необходимо предоставить полный путь каталога к расположению исполняемого файла Python. Результатом будет команда вроде следующей: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

Пакет SDK использует три средства экспорта Azure Monitor для отправки различных типов телеметрии в Azure Monitor: трассировок, метрик и журналов. Дополнительные сведения об этих типах телеметрии см. в [обзоре платформы данных](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Используйте следующие инструкции для отправки этих типов телеметрии с помощью трех средств экспорта.

## <a name="telemetry-type-mappings"></a>Сопоставление типов данных телеметрии

Ниже приведены средства экспорта, предоставляемые OpenCensus, сопоставленные с типами данных телеметрии, которые вы увидите в Azure Monitor.

![Снимок экрана: сопоставление типов телеметрии из OpenCensus с Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Трассировка

> [!NOTE]
> `Trace` в OpenCensus относится к [распределенной трассировке](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). `AzureExporter` отправляет данные телеметрии `requests` и `dependency` в Azure Monitor.

1. Сначала давайте создадим данные трассировки локально. В Python IDLE или любом выбранном текстовом редакторе введите следующий код.

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

2. При выполнении кода вам несколько раз будет предложено ввести значение. С каждой записью значение будет выводиться в оболочке, а модуль OpenCensus для Python будет создавать соответствующий фрагмент `SpanData`. Проект OpenCensus определяет [трассировку в виде дерева диапазонов](https://opencensus.io/core-concepts/tracing/).
    
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

3. Хотя ввод значений полезен в демонстрационных целях, в конечном счете мы хотим выдать `SpanData` для Azure Monitor. Передайте строку подключения непосредственно в средство экспорта или укажите ее в переменной среды `APPLICATIONINSIGHTS_CONNECTION_STRING`. Измените код из предыдущего шага, отталкиваясь от следующего примера кода.

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

4. Теперь при запуске сценария Python вы по-прежнему будете получать запрос на ввод значений, но только значение будет выводиться в оболочке. Созданный `SpanData` будет отправлен в Azure Monitor. Выданные данные диапазонов можно найти в разделе `dependencies`. Дополнительные сведения об исходящих запросах см. в разделе о [зависимостях](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency) OpenCensus для Python.
Дополнительные сведения о входящих запросах см. в разделе о [запросах](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request) OpenCensus для Python.

#### <a name="sampling"></a>Выборка

Сведения о выборке в OpenCensus см. в разделе о [выборке в OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Корреляция трассировки

Дополнительные сведения о корреляции телеметрии в данных трассировки см. в разделе о [корреляции телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python) OpenCensus для Python.

#### <a name="modify-telemetry"></a>Изменение телеметрии

Дополнительные сведения об изменении отслеживаемой телеметрии перед отправкой в Azure Monitor см. в разделе об [обработчиках телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) OpenCensus для Python.

### <a name="metrics"></a>Метрики

1. Сначала давайте создадим данные метрик локально. Мы создадим простую метрику для отслеживания числа нажатий клавиши ВВОД.

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
2. При выполнении кода вам несколько раз будет предложено нажать клавишу ВВОД. Будет создана метрика для отслеживания числа нажатий клавиши ВВОД. При каждом нажатии ее значение будет увеличиваться, и сведения метрики будут отображаться в консоли. Эти сведения включают текущее значение и текущую отметку времени при обновлении метрики.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Хотя ввод значений полезен в демонстрационных целях, в конечном счете мы хотим выдать данные метрики для Azure Monitor. Передайте строку подключения непосредственно в средство экспорта или укажите ее в переменной среды `APPLICATIONINSIGHTS_CONNECTION_STRING`. Измените код из предыдущего шага, отталкиваясь от следующего примера кода.

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

4. Программа экспорта будет отсылать данные метрики в Azure Monitor через фиксированные интервалы. Значение по умолчанию — каждые 15 секунд. Мы отслеживаем одну метрику, поэтому данные этой метрики, вне зависимости от содержащихся значений и отметок времени, будут отправляться через каждый интервал. Эти данные можно найти в `customMetrics`.

#### <a name="standard-metrics"></a>Стандартные метрики

По умолчанию средство экспорта метрик будет отсылать набор стандартных метрик для Azure Monitor. Это можно отключить, задав для флага `enable_standard_metrics` значение `False` в конструкторе средства экспорта метрик.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```
Ниже приведен список стандартных метрик, которые отправляются в настоящее время.

- Объем доступной памяти (в байтах)
- Процессорное время ЦП (в процентах)
- Частота входящих запросов (в секунду)
- Среднее время выполнения входящего запроса (в миллисекундах)
- Частота исходящих запросов (в секунду)
- Использования ЦП процессом (процент)
- Количество байтов исключительного использования процесса (в байтах)

Эти метрики должны быть доступны в `performanceCounters`. Частота входящих запросов будет в `customMetrics`. Дополнительные сведения см. в статье [Счетчики производительности](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Изменение телеметрии

Дополнительные сведения об изменении отслеживаемой телеметрии перед отправкой в Azure Monitor см. в разделе об [обработчиках телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) OpenCensus для Python.

### <a name="logs"></a>Журналы

1. Сначала давайте создадим данные метрик локально.

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

2.  Код будет постоянно запрашивать ввод значения. Для каждого введенного значения выдается запись журнала.

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

3. Хотя ввод значений полезен в демонстрационных целях, в конечном счете мы хотим выдать данные журнала для Azure Monitor. Передайте строку подключения непосредственно в средство экспорта или укажите ее в переменной среды `APPLICATIONINSIGHTS_CONNECTION_STRING`. Измените код из предыдущего шага, отталкиваясь от следующего примера кода.

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

4. Средство экспорта отправит данные журнала в Azure Monitor. Эти данные можно найти в `traces`. 

    > [!NOTE]
    > `traces` в этом контексте не совпадает с `Tracing`. `traces` относится к типу телеметрии, который будет отображаться в Azure Monitor при использовании `AzureLogHandler`. `Tracing` относится к концепции в OpenCensus и связан с [распределенной трассировкой](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Чтобы отформатировать сообщения журнала, можно использовать `formatters` во встроенном API-интерфейсе [ведения журнала](https://docs.python.org/3/library/logging.html#formatter-objects) Python.

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

6. Можно также добавлять пользовательские свойства в сообщения журнала в аргументе ключевого слова *extra* с помощью поля custom_dimensions. В Azure Monitor они будут отображаться как пары «ключ-значение» в `customDimensions`.
    > [!NOTE]
    > Чтобы эта функция работала, необходимо передать словарь в поле custom_dimensions. При передаче аргументов любого другого типа средство ведения журнала будет игнорировать их.

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

OpenCensus для Python не выполняет автоматическое отслеживание и отправку данных телеметрии `exception`. Они отправляются через `AzureLogHandler` путем использования исключений через библиотеку ведения журнала Python. Вы можете добавлять пользовательские свойства так же, как и для обычного ведения журнала.

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
Так как необходимо регистрировать исключения явным образом, пользователь должен иметь возможность определять подходящий способ регистрации необработанных исключений. Пока телеметрия исключений явно заносится в журнал, OpenCensus не накладывает ограничений на то, как пользователь хочет делать это.

#### <a name="sampling"></a>Выборка

Сведения о выборке в OpenCensus см. в разделе о [выборке в OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Корреляция журнала

Дополнительные сведения о том, как обогатить журналы данными контекста трассировки, см. в разделе [интеграции журналов](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation) OpenCensus для Python.

#### <a name="modify-telemetry"></a>Изменение телеметрии

Дополнительные сведения об изменении отслеживаемой телеметрии перед отправкой в Azure Monitor см. в разделе об [обработчиках телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) OpenCensus для Python.

## <a name="view-your-data-with-queries"></a>Фильтрация данных с помощью запросов

Данные телеметрии, отправленные из приложения, можно просмотреть на вкладке **Журналы (аналитика)** .

![Снимок экрана панели обзора со вкладкой «Журналы (аналитика)», выделенной красной рамкой](./media/opencensus-python/0010-logs-query.png)

В списке в разделе **Активные**

- Для данных телеметрии, отправляемых с помощью средства экспорта трассировки Azure Monitor, входящие запросы отображаются в разделе `requests`. Исходящие или внутрипроцессные запросы отображаются в разделе `dependencies`.
- Для данных телеметрии, отправляемых с помощью средства экспорта метрик Azure Monitor, отправленные метрики отображаются в разделе `customMetrics`.
- Для данных телеметрии, отправляемых с помощью средства экспорта журналов Azure Monitor, журналы отображаются в разделе `traces`. Исключения отображаются в `exceptions`.

Более подробные сведения об использовании запросов и журналов см. в [Журналы в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Дополнительные сведения об OpenCensus для Python

* [OpenCensus для Python на GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Настройка](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Средства экспорта Azure Monitor Exporter на GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Интеграции OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Примеры приложений Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Дальнейшие действия

* [Отслеживание входящих запросов](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Отслеживание исходящих запросов](./../../azure-monitor/app/opencensus-python-request.md)
* [Сопоставление приложений](./../../azure-monitor/app/app-map.md)
* [Поиск и диагностика проблем производительности с помощью Azure Application Insights](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>видны узлы

* [Тесты доступности](../../azure-monitor/app/monitor-web-app-availability.md). Создавайте тесты, позволяющие проверить, доступен ли ваш сайт в Интернете.
* [Интеллектуальная диагностика](../../azure-monitor/app/proactive-diagnostics.md). Эти тесты выполняются автоматически, поэтому вам не нужно их настраивать. Благодаря ей вы узнаете о необычном количестве неудачных запросов.
* [Оповещения о метриках](../../azure-monitor/platform/alerts-log.md). Настройте оповещения, чтобы получать уведомления в случае, если метрика превысила пороговое значение. Их можно настроить для пользовательских метрик, добавляемых в код приложения.
