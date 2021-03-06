---
title: Журналы в Azure Monitor | Документация Майкрософт
description: Описывает журналы в Azure Monitor, которые используются для расширенного анализа данных мониторинга.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 269744d5e9552d87c3fa619f33e02c833b3841be
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894170"
---
# <a name="logs-in-azure-monitor"></a>Журналы в Azure Monitor

> [!NOTE]
> Все данные, собранные Azure Monitor, помещаются в один из двух фундаментальных типов, метрик и журналов. В этой статье описываются журналы. В Azure Monitor подробное описание метрик и [мониторинг данных, собираемых Azure Monitor,](data-platform.md) см. в разделе [метрики](data-platform-metrics.md) .

Журналы в Azure Monitor особенно полезны для выполнения сложного анализа данных из различных источников. В этой статье описано, как структурированы журналы в Azure Monitor, что можно делать с данными, а также определить различные источники данных, которые хранят данные в журналах.

> [!NOTE]
> Важно различать журналы Azure Monitor и источники данных журнала в Azure. Например, события уровня подписки в Azure записываются в [Журнал действий](activity-logs-overview.md) , который можно просмотреть в меню Azure Monitor. Большинство ресурсов записывают операционную информацию в [Журнал ресурсов](resource-logs-overview.md) , который можно пересылать в разные расположения. Журналы Azure Monitor — это платформа данных журналов, которая собирает журналы действий и журналы ресурсов вместе с другими данными мониторинга для обеспечения детального анализа всего набора ресурсов.

## <a name="what-are-azure-monitor-logs"></a>Что такое журналы Azure Monitor?

Журналы Azure Monitor содержат различные типы данных, организованные в записи с разными наборами свойств для каждого типа. Журналы могут содержать числовые значения, такие как Azure Monitor метрики, но обычно содержат текстовые данные с подробными описаниями. Они отличаются от данных метрик в том, что они зависят от их структуры и часто не собираются с регулярными интервалами. Данные телеметрии, такие как события и трассировки, хранятся Azure Monitor журналов в дополнение к данным о производительности, чтобы их можно было объединить для анализа.

Общий тип записи журнала — это событие, которое собирается регулярно. События создаются приложением или службой и обычно содержат достаточно информации, чтобы предоставить полный контекст самостоятельно. Например, событие может указывать на то, что определенный ресурс был создан или изменен, новый узел был запущен в ответ на увеличение трафика или в работе приложения была обнаружена ошибка.

 Так как формат данных может различаться, приложения могут создавать пользовательские журналы, используя необходимую структуру. Данные метрик можно даже хранить в журналах, чтобы объединить их с другими данными мониторинга для анализа тенденций и других данных.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Что можно делать с Azure Monitor журналами?
В следующей таблице перечислены различные способы использования журналов в Azure Monitor.


|  |  |
|:---|:---|
| Анализ | Используйте [log Analytics](../log-query/get-started-portal.md) в портал Azure для записи [запросов журналов](../log-query/log-query-overview.md) и интерактивного анализа данных журнала с помощью мощного модуля обозреватель данных Analysis Engine.<br>Используйте [консоль Application Insights Analytics](../app/analytics.md) в портал Azure для записи запросов журнала и интерактивного анализа данных журнала из Application Insights. |
| Визуализация | Закрепление результатов запросов, отображаемых в виде таблиц или диаграмм на [панели мониторинга Azure](../../azure-portal/azure-portal-dashboards.md).<br>Создание [книги](../app/usage-workbooks.md) для объединения с несколькими наборами данных в интерактивном отчете. <br>Экспортировать результаты запроса в [Power BI](powerbi.md), чтобы можно было использовать разные типы визуализации и делиться этими результатами с пользователями за пределами Azure.<br>Экспортируйте результаты запроса в [Grafana](grafana-plugin.md) , чтобы использовать его панель мониторинга и объединить с другими источниками данных.|
| Оповещение | Настраивать [правила оповещения журнала](alerts-log.md), которые отправляют уведомления или выполняют [автоматические действия](action-groups.md), когда результаты запроса соответствуют определенному результату.<br>Настройка [правила генерации оповещений метрик](alerts-metric-logs.md) для определенных журналов данных журнала, извлеченных в качестве метрик. |
| Получение | Доступ к результатам запроса журнала из командной строки с помощью [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Доступ к результатам запроса журнала из командной строки с помощью [командлетов PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Доступ к результатам запроса журнала из пользовательского приложения с помощью [REST API](https://dev.loganalytics.io/). |
| Экспортировать | Создайте рабочий процесс для получения данных журнала и скопируйте их во внешнее расположение с помощью [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Как структурированы данные в журналах Azure Monitor?
Данные, собранные журналами Azure Monitor, хранятся в [log Analytics рабочей области](../platform/design-logs-deployment.md). Каждая Рабочая область содержит несколько таблиц, каждый из которых хранит данные из определенного источника. Хотя все таблицы имеют [Общие свойства](log-standard-properties.md), каждый из них имеет уникальный набор свойств в зависимости от типа хранимых данных. В новой рабочей области будет содержаться стандартный набор таблиц, а с помощью различных решений для мониторинга и других служб, записывающих данные в рабочую область, будут добавлены другие таблицы.

Данные журнала из Application Insights используют тот же механизм Log Analytics, что и рабочие области, но хранятся отдельно для каждого отслеживаемого приложения. Каждое приложение имеет стандартный набор таблиц для хранения таких данных, как запросы приложений, исключения и Просмотры страниц.

Запросы журнала будут использовать данные из Log Analytics рабочей области или приложения Application Insights. [Запрос между ресурсами](../log-query/cross-workspace-query.md) можно использовать для анализа данных приложения вместе с другими данными журнала или для создания запросов, включая несколько рабочих областей или приложений.

![Рабочие области](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Запросы журнала
Данные в журналах Azure Monitor извлекаются с помощью [запроса журнала](../log-query/log-query-overview.md) , написанного на [языке запросов Kusto](../log-query/get-started-queries.md), который позволяет быстро извлекать, консолидировать и анализировать собранные данные. Используйте [log Analytics](../log-query/portals.md) для записи и тестирования запросов журнала в портал Azure. Он позволяет интерактивно работать с результатами или закреплять их на панели мониторинга, чтобы просматривать их с другими визуализациями.

![Анализ журналов](media/data-platform-logs/log-analytics.png)

Откройте [log Analytics из Application Insights](../app/analytics.md) , чтобы проанализировать данные Application Insights.

![Аналитика Application Insights](media/data-platform-logs/app-insights-analytics.png)

Данные журнала также можно получить с помощью [log Analytics API](https://dev.loganalytics.io/documentation/overview) и [REST API Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Источники журналов Azure Monitor
Azure Monitor может собирать данные журнала из различных источников, размещенных как в Azure, так и в локальных ресурсах. В следующих таблицах перечислены различные источники данных, доступные из разных ресурсов, которые записывают данные в журналы Azure Monitor. У каждой из них есть ссылка на подробные сведения о любой требуемой конфигурации.

### <a name="azure-tenant-and-subscription"></a>Клиент и подписка Azure

| Данные | Описание |
|:---|:---|
| Журналы аудита Azure Active Directory | Настраивается с помощью параметров диагностики для каждого каталога. См. статью [Интеграция журналов Azure AD с Azure Monitor журналами](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Журналы действий | По умолчанию хранится отдельно и может использоваться для оповещений практически в реальном времени. Установите решение "аналитика журналов действий" для записи в Log Analytics рабочую область. См. статью [Получение и анализ журналов действий Azure в log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Ресурсы Azure

| Данные | Описание |
|:---|:---|
| Диагностика ресурсов | Настройте параметры диагностики для записи в диагностические данные, включая метрики в Log Analytics рабочей области. См. статью [потоковая передача журналов ресурсов Azure в log Analytics](resource-logs-collect-storage.md). |
| Решения для мониторинга | Решения для мониторинга записывают данные, которые они собираются в рабочую область Log Analytics. Список решений см. [в статье сведения о сборе данных для решений по управлению в Azure](../insights/solutions-inventory.md) . Дополнительные сведения об установке и использовании решений см. [в разделе мониторинг решений в Azure Monitor](../insights/solutions.md) . |
| Метрики | Отправка метрик платформы для ресурсов Azure Monitor в рабочую область Log Analytics, чтобы хранить данные журнала в течение длительного времени и выполнять сложный анализ с другими типами данных с помощью [языка запросов Kusto](/azure/kusto/query/). См. статью [потоковая передача журналов ресурсов Azure в log Analytics](resource-logs-collect-storage.md). |
| Хранилище таблиц Azure | Собирайте данные из службы хранилища Azure, где некоторые ресурсы Azure записывают данные мониторинга. [Дополнительные сведения о событиях с log Analytics см. в статье Использование хранилища BLOB-объектов Azure для IIS и хранилища таблиц Azure](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Виртуальные машины

| Данные | Описание |
|:---|:---|
|  Источники данных в агенте | Источниками данных, собранными из агентов [Windows](agent-windows.md) и [Linux](../learn/quick-collect-linux-computer.md) , являются события, данные производительности и пользовательские журналы. Список источников данных и сведения о конфигурации см. [в разделе источники данных агента в Azure Monitor](data-sources.md) . |
| Решения для мониторинга | Решения для мониторинга записывают данные, которые они собираются из агентов, в их Log Analytics рабочей области. Список решений см. [в статье сведения о сборе данных для решений по управлению в Azure](../insights/solutions-inventory.md) . Дополнительные сведения об установке и использовании решений см. [в разделе мониторинг решений в Azure Monitor](../insights/solutions.md) . |
| System Center Operations Manager | Подключите Operations Manager группу управления к Azure Monitor, чтобы получить данные о событиях и производительности из локальных агентов в журналы. Дополнительные сведения об этой конфигурации см. в разделе [Connect Operations Manager to log Analytics](om-agents.md) . |


### <a name="applications"></a>приложениям

| Данные | Описание |
|:---|:---|
| Запросы и исключения | Подробные данные о запросах приложений и исключениях находятся в таблицах _запросов_, _pageViews_и _исключений_ . Вызовы [внешних компонентов](../app/asp-net-dependencies.md) находятся в таблице _зависимостей_ . |
| Использование и производительность | Производительность приложения доступна в таблицах _запросы_, _бровсертимингс_ и _PerformanceCounter_ . Данные для [пользовательских метрик](../app/api-custom-events-metrics.md#trackevent) находятся в таблице _customMetrics_ .|
| Данные трассировки | Результаты [распределенной трассировки](../app/distributed-tracing.md) хранятся в таблице _трассировок_ . |
| Тесты доступности | Сводные данные из [тестов доступности](../app/monitor-web-app-availability.md) хранятся в таблице _availabilityResults_ . Подробные данные из этих тестов находятся в отдельном хранилище и доступны из Application Insights в портал Azure. |

### <a name="insights"></a>Аналитические сведения

| Данные | Описание |
|:---|:---|
| Azure Monitor для контейнеров | Данные инвентаризации и производительности, собираемые [Azure Monitor для контейнеров](../insights/container-insights-overview.md). Список таблиц см. в разделе [данные о контейнере — сведения о коллекции](../insights/container-insights-log-search.md#container-records) . |
| Azure Monitor для виртуальных машин | Данные о сопоставлении и производительности, собираемые [Azure Monitor для виртуальных машин](../insights/vminsights-overview.md). Дополнительные сведения о запросе этих данных см. в разделе [запросы к журналам Azure Monitor для виртуальных машин](../insights/vminsights-log-search.md) . |

### <a name="custom"></a>Пользовательские 

| Данные | Описание |
|:---|:---|
| REST API | Запись данных в Log Analytics рабочую область из любого клиента RESTFUL. Дополнительные сведения см. в разделе [Отправка данных журнала в Azure Monitor с помощью API сборщика данных HTTP](data-collector-api.md) .
| Логическое приложение | Запись данных в рабочую область Log Analytics из рабочего процесса приложения логики с помощью действия **сборщика данных log Analytics Azure** . |

### <a name="security"></a>Безопасность

| Данные | Описание |
|:---|:---|
| Центр безопасности Azure | [Центр безопасности Azure](/azure/security-center/) хранит собранные данные в log Analytics рабочей области, где их можно проанализировать с помощью других данных журнала. Дополнительные сведения о конфигурации рабочей области см. [в статье сбор данных в центре безопасности Azure](../../security-center/security-center-enable-data-collection.md) . |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) хранит данные из источников данных в log Analytics рабочей области. См. раздел [Подключение к источникам данных](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [платформе Azure Monitor Data Platform](data-platform.md).
- Сведения о [метриках в Azure Monitor](data-platform-metrics.md).
- Узнайте о [доступных данных мониторинга](data-sources.md) для различных источников в Azure.
