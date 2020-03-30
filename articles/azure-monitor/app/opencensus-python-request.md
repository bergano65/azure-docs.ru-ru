---
title: Отслеживание входящих запросов в Azure Application Insights с помощью OpenCensus Python (ru) Документы Майкрософт
description: Мониторинг запроса требует ваших приложений Python через OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669953"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Отслеживайте входящие запросы с помощью OpenCensus Python

Данные входящих запросов собираются с помощью OpenCensus Python и его различных интеграций. Отслеживайте данные о входящих запросах, отправленные в `django` `flask` веб-приложения, построенные поверх популярных веб-инфраструктур, и `pyramid`. Затем данные отправляются в Application Insights `requests` под Azure Monitor в качестве телеметрии.

Во-первых, инструмент вашего приложения Python с [последними OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Отслеживание приложений Django

1. Скачать `opencensus-ext-django` и установить из [PyPI](https://pypi.org/project/opencensus-ext-django/) и инструмент вашего приложения с `django` посредниками. Входящие запросы, `django` отправленные в ваше приложение, будут отслеживаться.

2. `opencensus.ext.django.middleware.OpencensusMiddleware` Включите `settings.py` в `MIDDLEWARE`файл под .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Убедитесь, что AzureExporter правильно `settings.py` `OPENCENSUS`настроен в под.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. Вы также можете добавить URL-адреса в `settings.py` соответствии `BLACKLIST_PATHS` с запросами, которые вы не хотите отслеживать.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Отслеживание приложений flask

1. Скачать `opencensus-ext-flask` и установить из [PyPI](https://pypi.org/project/opencensus-ext-flask/) и инструмент вашего приложения с `flask` посредниками. Входящие запросы, `flask` отправленные в ваше приложение, будут отслеживаться.

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

2. Вы можете настроить `flask` промежуточное программное обеспечение непосредственно в коде. Для запросов от URL-адресов, которые вы `BLACKLIST_PATHS`не хотите отслеживать, добавьте их в .

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

## <a name="tracking-pyramid-applications"></a>Отслеживание приложений Пирамиды

1. Скачать `opencensus-ext-django` и установить из [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) и инструмент вашего приложения с `pyramid` tween. Входящие запросы, `pyramid` отправленные в ваше приложение, будут отслеживаться.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Вы можете настроить `pyramid` tween непосредственно в коде. Для запросов от URL-адресов, которые вы `BLACKLIST_PATHS`не хотите отслеживать, добавьте их в .

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

## <a name="next-steps"></a>Дальнейшие действия

* [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
* [Доступность](../../azure-monitor/app/monitor-web-app-availability.md)
* [Поиск](../../azure-monitor/app/diagnostic-search.md)
* [Запрос журнала (аналитика)](../../azure-monitor/log-query/log-query-overview.md)
* [Диагностика транзакции](../../azure-monitor/app/transaction-diagnostics.md)
