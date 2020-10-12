---
title: Отслеживание входящих запросов в Azure Application Insights с помощью Опенценсус Python | Документация Майкрософт
description: Отслеживайте вызовы запросов для приложений Python через Опенценсус Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: c94bc949f13ee19a9d2150c9d3c1b6a2bdb959b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850072"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Мониторинг входящих запросов с помощью Опенценсус Python

Данные входящего запроса собираются с помощью Опенценсус Python и различных интеграций. Отследите данные входящего запроса, отправляемые в веб-приложения, созданные на основе популярных веб-платформ `django` , `flask` и `pyramid` . Затем данные отправляются в Application Insights под Azure Monitor в качестве `requests` телеметрии.

Сначала выполните инструментирование приложения Python с помощью последнего [пакета SDK для Опенценсус Python](./opencensus-python.md).

## <a name="tracking-django-applications"></a>Отслеживание Django приложений

1. Скачайте и установите `opencensus-ext-django` из [PyPI](https://pypi.org/project/opencensus-ext-django/) и выполните инструментирование приложения с помощью по `django` промежуточного слоя. Входящие запросы, отправляемые в ваше `django` приложение, будут относиться к отслеживанию.

2. Включите `opencensus.ext.django.middleware.OpencensusMiddleware` в `settings.py` файл в `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Убедитесь, что Азурикспортер правильно настроен в вашей `settings.py` среде `OPENCENSUS` . Для запросов с URL-адресов, которые не нужно отслеживанию, добавьте их в `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Отслеживание Flask приложений

1. Скачайте и установите `opencensus-ext-flask` из [PyPI](https://pypi.org/project/opencensus-ext-flask/) и выполните инструментирование приложения с помощью по `flask` промежуточного слоя. Входящие запросы, отправляемые в ваше `flask` приложение, будут относиться к отслеживанию.

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

2. Вы также можете настроить `flask` приложение с помощью `app.config` . Для запросов с URL-адресов, которые не нужно отслеживанию, добавьте их в `BLACKLIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Отслеживание пирамидальных приложений

1. Скачайте и установите `opencensus-ext-django` из [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) и выполните инструментирование приложения с помощью `pyramid` анимации. Входящие запросы, отправляемые в ваше `pyramid` приложение, будут относиться к отслеживанию.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Вы можете настроить `pyramid` анимацию непосредственно в коде. Для запросов с URL-адресов, которые не нужно отслеживанию, добавьте их в `BLACKLIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Дальнейшие шаги

* [Схема сопоставления приложений](./app-map.md)
* [Доступность](./monitor-web-app-availability.md)
* [Поиск](./diagnostic-search.md)
* [Запрос к журналу (Analytics)](../log-query/log-query-overview.md)
* [Диагностика транзакции](./transaction-diagnostics.md)

