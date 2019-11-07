---
title: Отслеживание входящих запросов в Azure Application Insights с помощью Опенценсус Python | Документация Майкрософт
description: Отслеживайте вызовы запросов для приложений Python через Опенценсус Python.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: f6b06e7bb2bb8637ca28b2fa4185754f8686798e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587931"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Мониторинг входящих запросов с помощью Опенценсус Python

Данные входящего запроса собираются с помощью Опенценсус Python и различных интеграций. Записывайте данные входящего запроса, отправленные в веб-приложения, созданные на основе популярных веб-платформ `django`, `flask` и `pyramid`. Затем данные отправляются в Application Insights в разделе Azure Monitor как `requests` телеметрии.

Сначала выполните инструментирование приложения Python с помощью последнего [пакета SDK для Опенценсус Python](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Отслеживание Django приложений

1. Скачайте и установите `opencensus-ext-django` из [PyPI](https://pypi.org/project/opencensus-ext-django/) и выполните инструментирование приложения с помощью `django` по промежуточного слоя. Входящие запросы, отправленные в приложение `django`, будут относиться к отслеживанию.

2. Включите `opencensus.ext.django.middleware.OpencensusMiddleware` в файл `settings.py` в разделе `MIDDLEWARE`.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Убедитесь, что Азурикспортер правильно настроен в `settings.py` в разделе `OPENCENSUS`.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
        }
    }
    ```

4. Можно также добавить URL-адреса в `settings.py` в разделе `BLACKLIST_PATHS` для запросов, которые не нужно отслеживанию.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Отслеживание Flask приложений

1. Скачайте и установите `opencensus-ext-flask` из [PyPI](https://pypi.org/project/opencensus-ext-flask/) и выполните инструментирование приложения с помощью `flask` по промежуточного слоя. Входящие запросы, отправленные в приложение `flask`, будут относиться к отслеживанию.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Вы можете настроить по промежуточного слоя `flask` непосредственно в коде. Для запросов с URL-адресов, которые не нужно отслеживанию, добавьте их в `BLACKLIST_PATHS`.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Отслеживание пирамидальных приложений

1. Скачайте и установите `opencensus-ext-django` из [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) и выполните инструментирование приложения с помощью `pyramid` анимации. Входящие запросы, отправленные в приложение `pyramid`, будут относиться к отслеживанию.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Вы можете настроить `pyramid` анимации непосредственно в коде. Для запросов с URL-адресов, которые не нужно отслеживанию, добавьте их в `BLACKLIST_PATHS`.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    service_name='foobar',
                )''',
                'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
* [Доступность](../../azure-monitor/app/monitor-web-app-availability.md)
* [Поиск](../../azure-monitor/app/diagnostic-search.md)
* [Запрос к журналу (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Диагностика транзакций](../../azure-monitor/app/transaction-diagnostics.md)
