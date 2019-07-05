---
title: Мониторинг приложений Python с помощью Azure Application Insights | Документация Майкрософт
description: Инструкции для привязывания OpenCensus Python с помощью Application Insights
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541438"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Сбор распределенных трассировок из Python (предварительная версия)

Application Insights теперь поддерживает распределенные трассировки Python приложений за счет интеграции с [OpenCensus](https://opencensus.io). Эта статья содержит пошаговое описание процесса установки OpenCensus для Python и получение данных мониторинга в Application Insights.

## <a name="prerequisites"></a>Технические условия

- Вам понадобится подписка Azure.
- Должен быть установлен Python, в этой статье используется [Python 3.7.0](https://www.python.org/downloads/), хотя более ранние версии, скорее всего, будут работать с незначительной корректировкой.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Создание ресурса Application Insights

Сначала необходимо создать ресурс Application Insights, который создаст key(ikey) инструментирования. Ikey затем используется для настройки пакета SDK OpenCensus для отправки данных телеметрии в Application Insights.

1. Выберите **Создать ресурс** > **Средства разработчика** > **Application Insights**.

   ![Добавления ресурса Application Insights](./media/opencensus-python/0001-create-resource.png)

   Откроется окно настроек, в котором нужно заполнить все поля в соответствии с приведенной ниже таблицей.

    | Параметры        | Значение           | Описание  |
   | ------------- |:-------------|:-----|
   | **Имя**      | Глобально уникальное значение | Имя, идентифицирующее отслеживаемое приложение |
   | **Группа ресурсов**     | myResourceGroup      | Имя новой группы ресурсов для размещения данных App Insights |
   | **Location** | East US | Выберите ближайшее расположение или расположение вблизи места размещения приложения |

2. Нажмите кнопку **Создать**.

## <a name="install-opencensus-azure-monitor-exporters"></a>Установить монитор экспорта OpenCensus Azure

1. Установите монитор OpenCensus Azure экспорта:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` предполагает, что для установки Python у вас задана переменная среды PATH. Если это не настроено, необходимо предоставить полный путь каталога к расположению исполняемого файла Python, что приведет к выполнению команды, подобной `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Сначала давайте создадим данные трассировки локально. В Python IDLE или любом выбранном текстовом редакторе введите следующий код:

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

3. При выполнении кода вам несколько раз будет предложено ввести значение. С каждой записью значение будет выводиться в оболочке, а модуль OpenCensus Python будет создавать соответствующий фрагмент **SpanData**. Проект OpenCensus определяет [_трассировку в виде дерева диапазонов_](https://opencensus.io/core-concepts/tracing/).
    
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

4. Хотя очень полезны для демонстрационных целей, в конечном итоге нам нужно создать SpanData в Application Insights. Измените код на предыдущем шаге, в зависимости от в следующем образце кода:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. Теперь при выполнении сценарий Python, вам по-прежнему будет предложено ввести значения, но теперь только значение печатается в оболочке.

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

## <a name="opencensus-for-python"></a>OpenCensus для Python

* [Настройка](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Интеграция с Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask).
* [Интеграция с Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django).
* [Интеграция с MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql).
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Дальнейшие действия

* [OpenCensus Python в GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Сопоставление приложений](./../../azure-monitor/app/app-map.md)
* [Поиск и диагностика проблем производительности с помощью Azure Application Insights](./../../azure-monitor/learn/tutorial-performance.md)