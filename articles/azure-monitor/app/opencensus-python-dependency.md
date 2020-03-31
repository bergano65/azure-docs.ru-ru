---
title: Отслеживание зависимостей в Azure Application Insights с помощью OpenCensus Python (ru) Документы Майкрософт
description: Мониторинг зависимости требует ваших приложений Python через OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669936"
---
# <a name="track-dependencies-with-opencensus-python"></a>Отслеживайте зависимости с помощью OpenCensus Python

Зависимость — это внешний компонент, который вызывается вашим приложением. Данные о зависимости собираются с помощью OpenCensus Python и его различных интеграций. Затем данные отправляются в Application Insights `dependencies` под Azure Monitor в качестве телеметрии.

Во-первых, инструмент вашего приложения Python с [последними OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Зависимости в процессе

OpenCensus Python SDK для Azure Monitor позволяет отправлять телеметрию зависимости «в процессе процесса» (информация и логика, которая происходит в приложении). Зависимость от процессов будет `type` иметь `INPROC` поле, как и в аналитике.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Зависимости с интеграцией "запросов"

Отслеживайте исходящие запросы `requests` с помощью интеграции OpenCensus.

Скачать `opencensus-ext-requests` и установить из [PyPI](https://pypi.org/project/opencensus-ext-requests/) и добавить его в микроэлементы. Запросы, отправленные с помощью библиотеки [запросов](https://pypi.org/project/requests/) Python, будут отслеживаться.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Зависимости с интеграцией "httplib"

Отслеживайте исходящие запросы с помощью интеграции OpenCensus. `httplib`

Скачать `opencensus-ext-httplib` и установить из [PyPI](https://pypi.org/project/opencensus-ext-httplib/) и добавить его в микроэлементы. Запросы, отправленные с помощью [http.client](https://docs.python.org/3.7/library/http.client.html) для Python3 или [httplib](https://docs.python.org/2/library/httplib.html) для Python2, будут отслеживаться.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Зависимости с интеграцией «джанго»

Отслеживайте исходящие запросы Django с помощью интеграции OpenCensus. `django`

Скачать `opencensus-ext-django` и установить из [PyPI](https://pypi.org/project/opencensus-ext-django/) `MIDDLEWARE` и добавить следующую строку в разделе в файле Django. `settings.py`

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Дополнительная конфигурация может быть предоставлена, читать [настройки](https://github.com/census-instrumentation/opencensus-python#customization) для полной ссылки.

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

## <a name="dependencies-with-mysql-integration"></a>Зависимости с интеграцией "mysql"

Отслеживайте свои зависимости от MYS'L с помощью интеграции OpenCensus. `mysql` Эта интеграция поддерживает библиотеку [mysql-разъема.](https://pypi.org/project/mysql-connector-python/)

Скачать `opencensus-ext-mysql` и установить из [PyPI](https://pypi.org/project/opencensus-ext-mysql/) и добавить следующие строки в код.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Зависимости с интеграцией "pymysql"

Отслеживайте свои зависимости PyMyS'L с помощью интеграции OpenCensus. `pymysql`

Скачать `opencensus-ext-pymysql` и установить из [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) и добавить следующие строки в код.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Зависимости с интеграцией «postgresql»

Отслеживайте свои зависимости PostgreS'L с помощью интеграции OpenCensus. `postgresql` Эта интеграция поддерживает библиотеку [psycopg2.](https://pypi.org/project/psycopg2/)

Скачать `opencensus-ext-postgresql` и установить из [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) и добавить следующие строки в код.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Зависимости с интеграцией «pymongo»

Отслеживайте свои зависимости MongoDB `pymongo` с помощью интеграции OpenCensus. Эта интеграция поддерживает библиотеку [pymongo.](https://pypi.org/project/pymongo/)

Скачать `opencensus-ext-pymongo` и установить из [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) и добавить следующие строки в код.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Зависимости с интеграцией «sqlalchemy»

Отслеживайте свои зависимости с помощью `sqlalchemy` s'LAlchemy с помощью интеграции OpenCensus. Эта интеграция отслеживает использование пакета [sqlalchemy,](https://pypi.org/project/SQLAlchemy/) независимо от базовой базы данных.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Дальнейшие действия

* [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
* [Доступность](../../azure-monitor/app/monitor-web-app-availability.md)
* [Поиск](../../azure-monitor/app/diagnostic-search.md)
* [Запрос журнала (аналитика)](../../azure-monitor/log-query/log-query-overview.md)
* [Диагностика транзакции](../../azure-monitor/app/transaction-diagnostics.md)
