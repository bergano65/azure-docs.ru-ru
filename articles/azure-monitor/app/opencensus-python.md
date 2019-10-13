---
title: Мониторинг приложений Python с помощью Azure Monitor | Документация Майкрософт
description: Содержит инструкции по установлению связи Опенценсус Python с Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294003"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Настройка Azure Monitor для приложения Python

Azure Monitor поддерживает распределенную трассировку, сбор метрик и ведение журнала приложений Python с помощью интеграции с [опенценсус](https://opencensus.io). В этой статье описывается пошаговая процедура настройки Опенценсус для Python и получения данных мониторинга для Azure Monitor.

## <a name="prerequisites"></a>Предварительные требования

- Вам понадобится подписка Azure.
- Необходимо установить Python. в этой статье используется [Python 3.7.0](https://www.python.org/downloads/), хотя более ранние версии, скорее всего, будут работать с незначительными корректировками.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Создание Application Insights ресурса в Azure Monitor

Сначала необходимо создать ресурс Application Insights в Azure Monitor, который создаст ключ инструментирования (iKey). Затем iKey используется для настройки пакета SDK Опенценсус для отправки данных телеметрии в Azure Monitor.

1. Выберите **Создать ресурс** > **Средства разработчика** > **Application Insights**.

   ![Добавления ресурса Application Insights](./media/opencensus-python/0001-create-resource.png)

   Откроется окно настроек, в котором нужно заполнить все поля в соответствии с приведенной ниже таблицей.

    | Параметры        | Значение           | Описание  |
   | ------------- |:-------------|:-----|
   | **Name**      | Глобально уникальное значение | Имя, идентифицирующее отслеживаемое приложение |
   | **Группа ресурсов**     | myResourceGroup      | Имя новой группы ресурсов для размещения данных App Insights |
   | **Location** | East US | Выберите ближайшее расположение или расположение вблизи места размещения приложения |

2. Нажмите кнопку **Создать**.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Инструментирование с помощью пакета SDK для Опенценсус Python для Azure Monitor

1. Установите Опенценсус Azure Monitor:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` предполагает, что для установки Python у вас задана переменная среды PATH. Если это не настроено, необходимо предоставить полный путь каталога к расположению исполняемого файла Python, что приведет к выполнению команды, подобной `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Пакет SDK использует три Azure Monitor экспорта для отправки различных типов телеметрии в Azure Monitor: трассировка, метрики и журналы. Дополнительные сведения о различных типах см. в [обзоре платформы данных](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) . Следуйте приведенным ниже инструкциям, чтобы узнать, как отправить эти типы через три программы экспорта.

### <a name="trace"></a>Трассировка

1. Сначала давайте создадим данные трассировки локально. В режиме простоя Python или в любом редакторе введите следующий код.

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

2. При выполнении кода вам несколько раз будет предложено ввести значение. С каждой записью значение будет выводиться в оболочке, а модуль OpenCensus Python будет создавать соответствующий фрагмент **SpanData**. Проект OpenCensus определяет [_трассировку в виде дерева диапазонов_](https://opencensus.io/core-concepts/tracing/).
    
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

3. В конечном итоге для демонстрационных целей мы хотим выдать `SpanData` в Azure Monitor. Измените код с предыдущего шага на основе следующего примера кода:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
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

4. Теперь при запуске скрипта Python вам по-прежнему будет предложено ввести значения, но теперь только значение печатается в оболочке. Созданная `SpanData` будет отправлена в Azure Monitor. Выпущенные данные span можно найти в `dependencies`.

### <a name="metrics"></a>metrics

1. Сначала создадим данные локальной метрики. Мы создадим простую метрику, чтобы отвести количество нажатий клавиш ВВОД.

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
2. Выполнение кода приведет к многократному появлении запроса на нажатие клавиши ВВОД. Метрика создается для контроля числа нажатий клавиши ВВОД. При каждой записи значение будет увеличено, а сведения о метриках будут отображаться в консоли с текущим значением и текущей меткой времени при обновлении метрики.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. В конечном итоге для демонстрационных целей мы хотим создать данные метрики для Azure Monitor. Измените код с предыдущего шага на основе следующего примера кода:

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
    )
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

4. Программа экспорта отправит данные метрик в Azure Monitor с фиксированным интервалом, значение по умолчанию — каждые 15 секунд. Мы отслеживаем одну метрику таким образом, чтобы данные метрик с любым значением и меткой времени, которые она содержит, будут отправляться каждый интервал. Данные можно найти в разделе `customMetrics`.

### <a name="logs"></a>Журналы

1. Сначала создадим некоторые данные локального журнала.

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

2.  Код будет постоянно запрашивать ввод значения. Запись журнала создается для каждого введенного значения, содержащего значение «сказал».

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

3. В конечном итоге для демонстрационных целей мы хотим создать данные метрики для Azure Monitor. Измените код с предыдущего шага на основе следующего примера кода:

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

4. Программа экспорта отправляет данные журнала в Azure Monitor. Данные можно найти в разделе `traces`.

## <a name="start-monitoring-in-the-azure-portal"></a>Запуск мониторинга на портале Azure

1. Теперь можно повторно открыть страницу **Обзор** Application Insights на портале Azure для просмотра сведений о выполняющемся в данный момент приложении. Выберите **Live Metrics Stream**.

   ![Снимок экрана области "Обзор" с пунктом Live Metrics Stream в красном прямоугольнике](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Вернитесь на страницу **Обзор** и выберите **Схема приложений**, чтобы получить визуальный макет отношений зависимости и длительность вызовов между компонентами приложения.

    ![Снимок экрана с основной схемой приложений](./media/opencensus-python/0007-application-map.png)

    Так как мы выполняли трассировку только одного вызова метода, наша схема приложений не так интересна. Но схему приложений можно масштабировать для визуализации гораздо большего количества распределенных приложений:

   ![Схема приложений](media/opencensus-python/application-map.png)

3. Выберите **Анализ работы**, чтобы выполнить подробный анализ работы и определить первопричину низкой производительности.

    ![Снимок экрана с панелью производительности](./media/opencensus-python/0008-performance.png)

4. Если выбрать **Примеры**, а затем щелкнуть один из примеров, которые отображаются на панели справа, запустится интерфейс сведений о сквозной транзакции. В то время как в нашем примере приложения показано одно событие, в более сложном приложении можно изучить сквозную транзакцию до уровня стека вызовов отдельного события.

     ![Снимок экрана с интерфейсом сквозных транзакций](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Просмотр данных с помощью запросов

1. Данные телеметрии, отправленные из приложения, можно просмотреть на вкладке журналы (аналитика).

    ![Снимок экрана: панель "Обзор" с журналами (аналитика), выбранными в красном поле](./media/opencensus-python/0010-logs-query.png)

2. Для данных телеметрии, отправленных с помощью программы экспорта трассировки Azure Monitor, входящие запросы будут отображаться в разделе `requests`, а обработка исходящих и поступающих запросов будет отображаться в разделе `dependencies`.

3. Для телеметрии, отправленной с помощью программы экспорта метрик Azure Monitor, отправленные метрики будут отображаться в разделе `customMetrics`.

4. Для телеметрии, отправленной с помощью программы экспорта журналов Azure Monitor, журналы будут отображаться в разделе `traces`, а исключения будут отображаться в разделе `exceptions`.

5. Дополнительные сведения об использовании запросов и журналов см. в статьях [журналы в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) .

## <a name="opencensus-for-python"></a>Опенценсус для Python

* [Опенценсус Python на GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Настройка](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Интеграция с Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask).
* [Интеграция с Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django).
* [Интеграция с MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql).
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Следующие шаги

* [Сводка по API](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Сопоставление приложений](./../../azure-monitor/app/app-map.md)
* [Поиск и диагностика проблем производительности с помощью Azure Application Insights](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Предупреждения

* [Тесты доступности](../../azure-monitor/app/monitor-web-app-availability.md). Создавайте тесты, позволяющие проверить, доступен ли ваш сайт в Интернете.
* [Интеллектуальная диагностика](../../azure-monitor/app/proactive-diagnostics.md). Эти тесты выполняются автоматически, поэтому вам не нужно их настраивать. Благодаря ей вы узнаете о необычном количестве неудачных запросов.
* [Оповещения о метриках](../../azure-monitor/app/alerts.md). Настройте оповещения, чтобы предупредить вас, если метрика превышает пороговое значение. Их можно настроить для пользовательских метрик, добавляемых в код приложения.