---
title: Отслеживание зависимостей в Azure Application Insights с помощью Опенценсус Python | Документация Майкрософт
description: Отслеживайте вызовы зависимостей для приложений Python через Опенценсус Python.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 6217798f8175e7ecc1c1ec4068d7765444e4d2a2
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368295"
---
# <a name="track-dependencies-with-opencensus-python"></a>Мониторинг зависимостей с помощью Опенценсус Python

Зависимость — это внешний компонент, который вызывается приложением. Данные зависимостей собираются с помощью Опенценсус Python и различных интеграций. Затем данные отправляются в Application Insights в разделе Azure Monitor как `dependencies` телеметрии.

Сначала выполните инструментирование приложения Python с помощью последнего [пакета SDK для Опенценсус Python](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Обрабатываемые зависимости

Пакет SDK для Опенценсус Python для Azure Monitor позволяет отправить данные телеметрии зависимостей "внутрипроцессный" (сведения и логику, которые происходят в приложении). В зависимости от процессов поле `type` будет содержать `INPROC` в аналитике.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Зависимости с интеграцией "запросы"

Отследите за исходящими запросами с помощью интеграции `requests` Опенценсус.

Скачайте и установите `opencensus-ext-requests` из [PyPI](https://pypi.org/project/opencensus-ext-requests/) и добавьте его в интеграцию с трассировкой. Запросы, отправленные с помощью библиотеки [запросов](https://pypi.org/project/requests/) Python, будут записаны.

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

## <a name="dependencies-with-httplib-integration"></a>Зависимости с интеграцией "хттплиб"

Отследите исходящие запросы с помощью интеграции с Опенценсус `httplib`.

Скачайте и установите `opencensus-ext-httplib` из [PyPI](https://pypi.org/project/opencensus-ext-httplib/) и добавьте его в интеграцию с трассировкой. Запросы, отправленные с помощью [http. Client](https://docs.python.org/3.7/library/http.client.html) для Python3 или [хттплиб](https://docs.python.org/2/library/httplib.html) для python2, будут относиться.

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

## <a name="dependencies-with-django-integration"></a>Зависимости с интеграцией "Django"

Отследите за исходящими запросами Django с помощью интеграции Опенценсус `django`.

Скачайте и установите `opencensus-ext-django` из [PyPI](https://pypi.org/project/opencensus-ext-django/) и добавьте следующую строку в раздел `MIDDLEWARE` `settings.py` файла Django.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Можно указать дополнительную конфигурацию, прочитать [настройки](https://github.com/census-instrumentation/opencensus-python#customization) для полной ссылки.

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

## <a name="dependencies-with-mysql-integration"></a>Зависимости с интеграцией MySQL

Следите за зависимостями MYSQL с помощью интеграции `mysql` Опенценсус. Эта интеграция поддерживает библиотеку [соединителей MySQL](https://pypi.org/project/mysql-connector-python/) .

Скачайте и установите `opencensus-ext-mysql` из [PyPI](https://pypi.org/project/opencensus-ext-mysql/) и добавьте в код следующие строки.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Зависимости с интеграцией "пимискл"

Следите за зависимостями Пимискл с помощью интеграции Опенценсус `pymysql`.

Скачайте и установите `opencensus-ext-pymysql` из [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) и добавьте в код следующие строки.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Зависимости с интеграцией "PostgreSQL"

Следите за зависимостями PostgreSQL с помощью интеграции Опенценсус `postgresql`. Эта интеграция поддерживает библиотеку [psycopg2](https://pypi.org/project/psycopg2/) .

Скачайте и установите `opencensus-ext-postgresql` из [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) и добавьте в код следующие строки.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Зависимости с интеграцией "пимонго"

Следите за зависимостями MongoDB с помощью интеграции Опенценсус `pymongo`. Эта интеграция поддерживает библиотеку [пимонго](https://pypi.org/project/pymongo/) .

Скачайте и установите `opencensus-ext-pymongo` из [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) и добавьте в код следующие строки.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Зависимости с интеграцией "склалчеми"

Следите за зависимостями с помощью Склалчеми, используя интеграцию с Опенценсус `sqlalchemy`. Эта интеграция отслеживает использование пакета [склалчеми](https://pypi.org/project/SQLAlchemy/) независимо от базовой базы данных.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Следующие шаги

* [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
* [Доступность](../../azure-monitor/app/monitor-web-app-availability.md)
* [Поиск](../../azure-monitor/app/diagnostic-search.md)
* [Запрос к журналу (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Диагностика транзакций](../../azure-monitor/app/transaction-diagnostics.md)
