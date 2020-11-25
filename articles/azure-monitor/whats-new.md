---
title: Что нового в документации по Azure Monitor?
description: Важные обновления добавляются в документацию по Azure Monitor каждый месяц.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/11/2020
ms.openlocfilehash: 18222573956f5ff3e9c617602902374b114328da
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555623"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Что нового в документации по Azure Monitor?

В этой статье приводятся списки новых или значительно измененных статей об Azure Monitor. Он будет обновляться в первую неделю каждого месяца, чтобы включать в себя обновления статей за предыдущий месяц.

## <a name="october-2020"></a>Октябрь 2020 г.

### <a name="general"></a>Общие сведения
- [Прекращение поддержки API Azure Monitor](platform/operationalinsights-api-retirement.md) — новая статья.

### <a name="agents"></a>Агенты
- [Что отслеживает Azure Monitor](monitor-reference.md) — добавлен раздел об агентах.

### <a name="alerts"></a>видны узлы
- [Создание групп действий и управление ими на портале Azure](platform/action-groups.md) — добавлен раздел о теге службы.
- [Примеры шаблонов Resource Manager для оповещений на основе метрик](samples/resource-manager-alerts-metric.md) — добавлен параметр сопоставления содержимого и расположения тестирования.
- [Устранение неполадок оповещений на основе метрик Azure](platform/alerts-troubleshoot-metric.md) — добавлены рекомендации по настройке правил.

### <a name="application-insights"></a>Application Insights
- [Подключаемый модуль Angular для пакета SDK JavaScript Application Insights](app/javascript-angular-plugin.md) — новая статья.
- [Azure Application Insight для приложений ASP.NET Core](app/asp-net-core.md) — добавлены вопросы и ответы о журналах ILogger.
- [Настройка мониторинга для ASP.NET с помощью Azure Application Insights](app/asp-net.md) — переписана статья.
- [Метрики на основе журнала и предварительно агрегированные метрики в Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) — добавлены таблицы с предварительно агрегированными метриками.
- [Мониторинг доступности и скорости реагирования любого веб-сайта](app/monitor-web-app-availability.md) — добавлен раздел о тегах заполнения расположения.
- [Мониторинг приложений Java в любом расположении (Azure Monitor Application Insights)](app/java-standalone-config.md) — добавлен пример конфигурации.
- [Мониторинг приложений Java в любом расположении (Azure Monitor Application Insights)](app/java-standalone-telemetry-processors.md) — новая статья.
- [Использование Анализа изменений в приложениях в Azure Monitor для поиска проблем с веб-приложениями](app/change-analysis.md) — добавлены разделы о виртуальных машинах и журнале действий.
  
### <a name="autoscale"></a>Автомасштабирование
- [Начало работы с автомасштабированием в Azure](platform/autoscale-get-started.md) — добавлен раздел о перемещении автомасштабирования в другой регион.

### <a name="containers"></a>Контейнеры
- [Настройка мониторинга PV с помощью Azure Monitor для контейнеров](insights/container-insights-persistent-volumes.md) — новая статья.
- [Управление Azure Monitor для агента контейнеров](insights/container-insights-manage-agent.md) — добавлена поддержка для кластера Kubernetes с включенной службой Azure Arc.
- [Оповещения о метриках от Azure Monitor для контейнеров](insights/container-insights-metric-alerts.md) — добавлена поддержка для кластера Kubernetes с Azure Arc.

### <a name="insights-and-solutions"></a>Полезные сведения и решения
- [Соединитель управления ИТ-услугами —безопасный экспорт в Azure Monitor](platform/it-service-management-connector-secure-webhook-connections.md) — добавлен раздел о ServiceNow.

### <a name="logs"></a>Журналы
- [Архивация данных из рабочей области Log Analytics в службу хранилища Azure с помощью приложения логики](platform/logs-export-logic-app.md) — новая статья.
- [Экспорт данных рабочей области Log Analytics в Azure Monitor (предварительная версия)](platform/logs-data-export.md) — добавлен пример текста запроса REST для концентратора событий.
- [Управление использованием и затратами для журналов Azure Monitor](platform/manage-cost-storage.md) — добавлена информация о связи между журналами Azure Monitor и выставлением счетов в Центре безопасности Azure. Добавлен запрос на получение количества узлов при использовании ценовой категории "За узел". 
- [Отслеживание работоспособности рабочей области Log Analytics в Azure Monitor](platform/monitor-workspace.md) — новая статья.
- [Запрос данных в Azure Monitor с помощью Azure Data Explorer (предварительная версия)](platform/azure-data-explorer-monitor-proxy.md) — новая статья.
- [Запрос экспортированных данных из Azure Monitor с помощью Azure Data Explorer (предварительная версия)](platform/azure-data-explorer-query-storage.md) — новая статья.

### <a name="networks"></a>Сети
- [Azure Monitor для сетей (предварительная версия)](insights/network-insights-overview.md) — добавлен раздел об устранении неполадок. Добавлен раздел о возможности подключения.

### <a name="platform-logs"></a>Журналы платформы
- [Схема событий Журнала действий Azure](platform/activity-log-schema.md) — добавлено описание уровней приоритетности.

### <a name="virtual-machines"></a>Виртуальные машины
- [Анализ изменений в Azure Monitor для виртуальных машин](insights/vminsights-change-analysis.md) — новая статья.
- [Общие сведения о включении Azure Monitor для виртуальных машин](insights/vminsights-enable-overview.md) — добавлены поддерживаемые регионы.
- [Обновление Azure Monitor для контейнеров метрик](insights/container-insights-update-metrics.md) — добавлена поддержка кластера Kubernetes с Azure Arc.



## <a name="september-2020"></a>Сентябрь 2020 г.

### <a name="general"></a>Общие сведения
- [Часто задаваемые вопросы по Azure Monitor](faq.md) — добавлен раздел по OpenTelemetry.

### <a name="agents"></a>Агенты
- [Общие сведения об агенте Azure Monitor](platform/azure-monitor-agent-overview.md) — добавлены факторы выбора для подключения к новому агенту.
- [Обзор агентов мониторинга Azure](platform/agents-overview.md) — добавлена поддержка для Windows 10.

### <a name="alerts"></a>видны узлы
- [Создание оповещения журнала с помощью шаблона ARM](platform/alerts-log-create-templates.md) — новая статья.
- [Устранение неполадок с оповещениями о метриках Azure](platform/alerts-troubleshoot-metric.md) — добавлен раздел об экспорте шаблона ARM для правила генерации оповещений о метриках.

### <a name="application-insights"></a>Application Insights
- [Создание ресурса на основе рабочей области Azure Monitor Application Insights](app/create-workspace-resource.md) — удалено обозначение предварительной версии.
- [Сохранение и хранение данных в Azure Application Insights](app/data-retention-privacy.md) — добавлены сведения о новой поддержке защиты от потери данных для Mac и Linux.
- [Счетчики событий в Application Insights](app/eventcounters.md) — добавлена заметка о счетчиках, собранных по умолчанию.
- [Метрики на основе журнала и предварительно агрегированные метрики в Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) — удалено обозначение предварительной версии.
- [Переход с классического ресурса Azure Monitor Application Insights на ресурс на основе рабочей области](app/convert-classic-resource.md) — новая статья.
- [Отслеживание приложений Java в любой среде — Azure Monitor Application Insights](app/java-in-process-agent.md) — обновление для новой предварительной версии агента.
- [Настройка аналитики веб-приложений для ASP.NET с помощью Azure Application Insights](app/asp-net.md) — переписана статья.
- [Каналы телеметрии в Azure Application Insights](app/telemetry-channels.md) — добавлены сведения о новой поддержке защиты от потери данных для Mac и Linux.
- [Устранение неполадок Azure Application Insights Snapshot Debugger](app/snapshot-debugger-troubleshoot.md) — добавлен раздел SSL для устранения неполадок Snapshot Debugger.
- [Использование Анализа изменений в приложениях в Azure Monitor для поиска проблем с веб-приложениями](app/change-analysis.md) — добавлены разделы о виртуальной машине и журнале действий.


### <a name="containers"></a>Контейнеры
- [Настройка кластера Kubernetes с поддержкой Azure Arc с помощью Azure Monitor для контейнеров](insights/container-insights-enable-arc-enabled-clusters.md) — добавлено руководство по активации мониторинга с помощью субъекта-службы.
- [Сбор метрик развертывания и HPA с помощью Azure Monitor для контейнеров](insights/container-insights-deployment-hpa-metrics.md) — новая статья.

### <a name="insights-and-solutions"></a>Полезные сведения и решения
- [Azure Monitor для Кэша Azure для Redis](insights/redis-cache-insights-overview.md) — удалено обозначение предварительной версии.
- [Azure Monitor для сетей (предварительная версия)](insights/network-insights-overview.md) — добавлены разделы о возможности подключения и трафике.
- [Соединитель ITSM — безопасный экспорт в Azure Monitor](platform/it-service-management-connector-secure-webhook-connections.md) — новая статья.
- [Соединитель ITSM в Azure Monitor](platform/itsmc-connections.md) — добавлены сведения об интеграции Cherwell и Provance ITSM.
- [Мониторинг Key Vault с помощью Azure Monitor для Key Vault](insights/key-vault-insights-overview.md) — удалено обозначение предварительной версии.

### <a name="logs"></a>Журналы
- [Запросы на аудит в запросах журнала Azure Monitor](log-query/query-audit.md) — новая статья.
- [Ключ Azure Monitor, управляемый клиентом](platform/customer-managed-keys.md) — добавлена клиентская блокировка.
- [Кластеры Журналов Azure Monitor уровня "Выделенный"](log-query/logs-dedicated-clusters.md) — новая статья.
- [Проектирование развертывания Журналов Azure Monitor](platform/design-logs-deployment.md) — добавлен раздел об ограничении скорости тома для масштабирования и приема.
- [Область действия запроса журнала в Azure Monitor Log Analytics](log-query/scope.md) — статья обновлена и теперь содержит сведения о приложениях на основе рабочей области.
- [Журналы в Azure Monitor](platform/data-platform-logs.md) — статья обновлена и теперь содержит сведения о приложениях на основе рабочей области.
- [Стандартные столбцы в записях журнала Azure Monitor](platform/log-standard-columns.md) — статья обновлена и теперь содержит сведения о приложениях на основе рабочей области.
- [Ограничения службы Azure Monitor](service-limits.md) — обновлены ограничения для регулирования количества пользовательских запросов.
- [Использование управляемых клиентом учетных записей хранения в Azure Monitor Log Analytics](platform/private-storage.md) — статья переписана.
- [Просмотр и анализ данных в Azure Log Analytics](./platform/data-platform-logs.md) — статья обновлена и теперь содержит сведения о приложениях на основе рабочей области.


### <a name="platform-logs"></a>Журналы платформы
- [Схема событий Журнала действий Azure — Azure Monitor](platform/activity-log-schema.md) — добавлены уровни приоритетности.
- [Примеры шаблонов Resource Manager для параметров диагностики](samples/resource-manager-diagnostic-settings.md) — добавлен пример для учетной записи хранения Azure.

### <a name="visualizations"></a>Визуализации
- [Визуализации диаграмм книги Azure Monitor](platform/workbooks-chart-visualizations.md) — новая статья.
- [Составная панель отрисовщика книги Azure Monitor](platform/workbooks-composite-bar.md) — новая статья.
- [Визуализации графов книги Azure Monitor](platform/workbooks-graph-visualizations.md) — новая статья.
- [Визуализации сетки книги Azure Monitor](platform/workbooks-grid-visualizations.md) — новая статья.
- [Визуализации сотовой конструкции книги Azure Monitor](platform/workbooks-honey-comb.md) — новая статья.
- [Визуализации текста книги Azure Monitor](platform/workbooks-text-visualizations.md) — новая статья.
- [Визуализации фрагмента книги Azure Monitor](platform/workbooks-tile-visualizations.md) — новая статья.
- [Визуализации дерева книги Azure Monitor](platform/workbooks-tree-visualizations.md) — новая статья.




## <a name="august-2020"></a>Август 2020 г.

### <a name="general"></a>Общие сведения

- [Что отслеживается службой Azure Monitor](monitor-reference.md) — статья обновлена и теперь содержит сведения об агенте Azure Monitor.


### <a name="agents"></a>Агенты
- [Общие сведения об агенте Azure Monitor](platform/azure-monitor-agent-overview.md) — новая статья.
- [Использование Azure Monitor для гибридной среды](insights/vminsights-enable-hybrid.md) — обновленная версия агента зависимостей.
- [Общие сведения об агентах Azure Monitor](platform/agents-overview.md) — добавлены агент Azure Monitor и объединенная таблица поддержки ОС.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Новые и измененные статьи, посвященные реструктуризации содержимого агента
- [Общие сведения о включении Azure Monitor для виртуальных машин](insights/vminsights-enable-overview.md)
- [Установка агента Log Analytics на компьютерах Linux](platform/agent-linux.md)
- [Установка агента Log Analytics на компьютерах Windows](platform/agent-windows.md)
- [Общие сведения об агенте Log Analytics](platform/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insight для веб-приложений JavaScript](app/javascript.md) — добавлен раздел со сведениями о корреляции сервера клиента и конфигурации для корреляции CORS.
- [Создание ресурса Azure Monitor Application Insights на основе рабочей области](app/create-workspace-resource.md) — добавлены возможности, предоставляемые приложениями на основе рабочей области.
- [IP-адреса, используемые Application Insights и Log Analytics](app/ip-addresses.md) — обновлены IP-адреса для Live Metrics Stream.
- [Отслеживание приложений Java в любой среде — Azure Monitor Application Insights](app/java-in-process-agent.md) — добавлена таблица поддерживаемых пользовательских данных телеметрии.
- [Подключаемый модуль React Native для пакета SDK JavaScript для Application Insights](app/javascript-react-native-plugin.md) — новая статья.
- [Подключаемый модуль React для пакета SDK JavaScript для Application Insights](app/javascript-react-plugin.md) — новая статья.
- [Примеры шаблонов Resource Manager для создания приложений-функций Azure с мониторингом Application Insights](samples/resource-manager-function-app.md) — новая статья.
- [Примеры шаблонов Resource Manager для создания веб-приложений Службы приложений Azure с мониторингом Application Insights](samples/resource-manager-web-app.md) — новая статья.
- [Анализ использования с помощью Application Insight](app/usage-overview.md) — добавлено видео.

### <a name="autoscale"></a>Автомасштабирование
- [Начало работы с автомасштабированием в Azure](platform/autoscale-get-started.md) — добавлен раздел о маршрутизации в работоспособных экземплярах для Службы приложений Azure.

### <a name="data-collection"></a>Сбор данных
- [Настройка сбора данных для агента Azure Monitor (предварительная версия)](platform/data-collection-rule-azure-monitor-agent.md) — новая статья.
- [Правила сбора данных в службе Azure Monitor (предварительная версия)](platform/data-collection-rule-overview.md) — новая статья.


### <a name="containers"></a>Контейнеры
- [Сбор метрик развертывания и HPA с помощью Azure Monitor для контейнеров](insights/container-insights-deployment-hpa-metrics.md) — новая статья.

### <a name="insights"></a>Аналитика
- [Решения для мониторинга в Azure Monitor](insights/solutions.md) — обновлена для нового пользовательского интерфейса.
- [Решение "Монитор производительности сети" в Azure](insights/network-performance-monitor.md) — добавлены поддерживаемые регионы рабочей области.


### <a name="logs"></a>Журналы
- [Azure Monitor: вопросы и ответы](faq.md) — добавлена запись об удалении данных из рабочей области. Добавлена запись для ответов 502 и 503.
  - [Проектирование развертывания журналов Azure Monitor](platform/design-logs-deployment.md) — обновлен раздел "Ограничение скорости тома для масштабирования и приема".
- [Управление использованием и затратами для журналов Azure Monitor](platform/manage-cost-storage.md) — изменены запросы на использование для более эффективного формата запросов.
- [Оптимизация запросов журналов в Azure Monitor](log-query/query-optimization.md) — добавлены конкретные значения идентификаторов производительности.
- [Примеры шаблонов Resource Manager для параметров диагностики в Azure Monitor](samples/resource-manager-diagnostic-settings.md) — добавлен пример для журналов аудита запросов к журналам.


### <a name="platform-logs"></a>Журналы платформы
- [Создание параметров диагностики для отправки журналов платформы и метрик в различные места назначения](platform/diagnostic-settings.md) — добавлено региональное требование для параметров диагностики.

### <a name="visualizations"></a>Визуализации
- [Общие сведения о Workbooks Azure Monitor](platform/workbooks-overview.md) — добавлено видео.
- [Перемещение шаблона Azure Workbook в другой регион](platform/workbook-templates-move-region.md) — новая статья.
- [Перемещение Azure Workbook в другой регион](platform/workbooks-move-region.md) — новая статья.



## <a name="july-2020"></a>Июль 2020 г.

### <a name="general"></a>Общие сведения
- [Развертывание Azure Monitor](deploy-scale.md) — реструктуризация содержимого подключения Azure Monitor для виртуальных машин.
- [Использование Приватного канала Azure для безопасного подключения сетей к Azure Monitor](platform/private-link-security.md) — добавлен раздел об ограничениях.

### <a name="alerts"></a>видны узлы
- [Правила действий для оповещений Azure Monitor](platform/alerts-action-rules.md) — добавлены процессы CLI.
- [Создание групп действий и управление ими на портале Azure](platform/action-groups.md) — обновлено с учетом изменений в пользовательском интерфейсе.
- [Примеры запросов в Log Analytics Azure Monitor](log-query/example-queries.md) — новая статья.
- [Устранение неполадок, связанных с оповещениями журналов в Azure Monitor,](platform/alerts-troubleshoot-log.md) — добавлен раздел о квоте правила генерации оповещений.
- [Устранение неполадок с оповещениями о метриках Azure](platform/alerts-troubleshoot-metric.md) — добавлен раздел о правиле генерации оповещений для пользовательской метрики, которая еще не выдается.
- [Сведения о работе оповещений о метриках в Azure Monitor](platform/alerts-metric-overview.md) — добавлены рекомендации по выбору степени детализации агрегации.

### <a name="application-insights"></a>Application Insights
- [Заметки о выпуске расширения веб-приложения Azure — Application Insights](app/web-app-extension-release-notes.md) — новая статья.
- [Примеры шаблонов Resource Manager для ресурсов Application Insights](samples/resource-manager-app-resource.md) — новая статья.
- [Устранение неполадок с помощью Azure Application Insights Profiler](app/profiler-troubleshooting.md) — добавлено примечание об ошибке при запуске профилировщика для приложений ASP.NET Core в Службе приложений Azure. 

### <a name="containers"></a>Контейнеры
- [Оповещения журнала от Azure Monitor для контейнеров](insights/container-insights-log-alerts.md) — новая статья.
- [Оповещения о метриках от Azure Monitor для контейнеров](insights/container-insights-metric-alerts.md) — новая статья.

### <a name="logs"></a>Журналы
- [Ключ Azure Monitor, управляемый клиентом](platform/customer-managed-keys.md) — добавлено сообщение об ошибке и раздел с конфигурацией CMK для запросов.
- [API сборщика данных HTTP в Azure Monitor](platform/data-collector-api.md) — добавлен пример на языке Python 3.
- [Оптимизация запросов журналов в Azure Monitor](log-query/query-optimization.md) — добавлен раздел о том, как избежать множественных проверок данных при использовании вложенных запросов.
- [Руководство. Начало работы с запросами Log Analytics](log-query/get-started-portal.md) — добавлено видео.

### <a name="platform-logs"></a>Журналы платформы
- [Создание параметров диагностики для отправки журналов платформы и метрик в различные места назначения](platform/diagnostic-settings.md) — добавлено видео.
- [Примеры шаблонов Resource Manager для службы Azure Monitor](samples/resource-manager-samples.md) — добавлен пример ARM с типом назначения журналов. 

### <a name="solutions"></a>Решения
- [Решения для мониторинга в Azure Monitor](insights/solutions.md) — добавлены процессы CLI.
- [Решение для управления Office 365 в Azure](insights/solution-office-365.md) — изменена дата прекращения поддержки.

### <a name="virtual-machines"></a>Виртуальные машины

Новые и измененные статьи, посвященные реструктуризации содержимого Azure Monitor для виртуальных машин

- [Общие сведения об Azure Monitor для виртуальных машин](insights/vminsights-overview.md)
- [Настройка рабочей области Log Analytics для Azure Monitor для виртуальных машин](insights/vminsights-configure-workspace.md)
- [Подключение компьютеров Linux к Azure Monitor](platform/agent-linux.md)
- [Включение Azure Monitor для гибридной среды](insights/vminsights-enable-hybrid.md)
- [Включение Azure Monitor для одной виртуальной машины или масштабируемого набора виртуальных машин на портале Azure](insights/vminsights-enable-portal.md)
- [Включение Azure Monitor для виртуальных машин с помощью Политики Azure](./insights/vminsights-enable-policy.md)
- [Общие сведения о включении Azure Monitor для виртуальных машин](insights/vminsights-enable-overview.md)
- [Включение Azure Monitor для виртуальных машин с помощью PowerShell](insights/vminsights-enable-powershell.md)
- [Включение Azure Monitor для виртуальных машин с помощью шаблонов Resource Manager](insights/vminsights-enable-resource-manager.md)
- [Включение Azure Monitor для виртуальных машин с помощью PowerShell или шаблонов](./insights/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Визуализации
- [Обновление визуализаций на панели мониторинга Log Analytics](log-query/dashboard-upgrade.md) — обновлена частота обновления.
- [Визуализация данных из Azure Monitor](visualizations.md) — добавлено видео.


## <a name="june-2020"></a>Июнь 2020 г.

### <a name="general"></a>Общие сведения
- [Развертывание Azure Monitor](deploy-scale.md) — новая статья.
- [Ключ Azure Monitor, управляемый клиентом](platform/customer-managed-keys.md) — обновлено свойство billingtype. Добавлены команды PowerShell.

### <a name="agents"></a>Агенты
- [Общие сведения об агенте Log Analytics](platform/log-analytics-agent.md). Добавлено требование о наличии Python 2.

### <a name="alerts"></a>видны узлы
- [Как обновить правила генерации оповещений или правила действий при перемещении их целевого ресурса в другой регион Azure](platform/alerts-resource-move.md) — новая статья.
- [Устранение неполадок c оповещениями о метриках Azure](platform/alerts-troubleshoot-metric.md) — новая статья.
- [Устранение неполадок с оповещениями журналов в Azure Monitor](platform/alerts-troubleshoot-metric.md) — новая статья.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights и веб-приложения JavaScript](app/javascript.md) — изменен раздел о пакете SDK для JavaScript. Обновлен фрагмент кода для отправки уведомлений о сбоях загрузки.
- [Настройка поддержки BYOS (использование собственного хранилища) для Profiler и Snapshot Debugger](app/profiler-bring-your-own-storage.md) — новая статья.
- [Отслеживание входящих запросов в Azure Application Insights с помощью OpenCensus Python](app/opencensus-python-request.md) — изменены сведения о ведении журналов и конфигурации для OpenCensus.
- [Мониторинг активного веб-приложения ASP.NET с помощью Azure Application Insights ](app/monitor-performance-live-website-now.md) — обновлена дата прекращения поддержки для монитора состояния версии 1.
- [Мониторинг служб и приложений Node.js с помощью Application Insights](app/nodejs.md) — изменены некоторые сведения, в том числе о переходе с предыдущей версий и о конфигурации пакета SDK.
- [Мониторинг приложений Python с помощью Azure Monitor (предварительная версия)](app/opencensus-python.md) — добавлен раздел о настройке средств экспорта Azure Monitor.
- [Мониторинг приложений без изменения кода (автоматическое инструментирование для Azure Monitor Application Insights)](app/codeless-overview.md) — новая статья.
- [Устранение неполадок при загрузке пакета SDK для веб-приложений JavaScript](app/javascript-sdk-load-failure.md) — новая статья.

### <a name="containers"></a>Контейнеры
- [Как отключить мониторинг гибридного кластера](insights/container-insights-optout-hybrid.md) — добавлен раздел для Kubernetes с поддержкой Arc.
- [Настройка кластера Kubernetes с поддержкой Azure Arc с помощью Azure Monitor для контейнеров](insights/container-insights-enable-arc-enabled-clusters.md) — новая статья.
- [Настройка Azure Red Hat OpenShift версии 4.x с помощью Azure Monitor для контейнеров](insights/container-insights-azure-redhat4-setup.md) — обновлены предварительные требования.
- [Настройка функции Live Data в Azure Monitor для контейнеров (предварительная версия)](insights/container-insights-livedata-setup.md) — удалено примечание о том, что функция недоступна в Azure для государственных организаций США.

### <a name="insights"></a>Аналитика
- [Вопросы и ответы по решению "Монитор производительности сети" (Azure)](insights/network-performance-monitor-faq.md) — добавлены вопросы и ответы по мониторингу ExpressRoute.

### <a name="logs"></a>Журналы
- [Удаление и восстановление рабочей области Azure Log Analytics](platform/delete-workspace.md) — добавлена команда PowerShell. Обновлены сведения об устранении неполадок.
- [Управление рабочими областями Log Analytics в Azure Monitor, добавленном](platform/manage-access.md) — добавлен пример для недопустимых таблиц в разделе о RBAC.
- [Управление использованием и затратами для журналов Azure Monitor](platform/manage-cost-storage.md) — дополнительные сведения о вычислении размера данных. Обновлены сведения о настройке оповещений о томе данных. Сведения о данных безопасности, собранных с помощью Azure Sentinel. Добавлено пояснение об ограничении объема данных.
- [Использование журналов Azure Monitor с Azure Logic Apps и Power Automate](platform/logicapp-flow-connector.md) — добавлены сведения об ограничениях соединителя.

### <a name="metrics"></a>Метрики
- [Поддерживаемые метрики Azure Monitor по типу ресурсов](platform/metrics-supported.md) — обновлены метрики SQL Server.


### <a name="platform-logs"></a>Журналы платформы

- [Примеры шаблонов Resource Manager для параметров диагностики в Azure Monitor](samples/resource-manager-diagnostic-settings.md) — исправлен параметр диагностики для журнала действий.
- [Отправка журнала действий Azure в рабочую область Log Analytics с помощью портала Azure](learn/quick-collect-activity-log-portal.md) — новая статья.
- [Отправка журнала действий Azure в рабочую область Log Analytics с помощью шаблона Azure Resource Manager](learn/quick-collect-activity-log-arm.md) — новая статья.

Новые и измененные статьи, посвященные реструктуризации и консолидации содержимого журнала платформы

- [Архивация журналов ресурсов Azure в учетной записи хранения](./platform/resource-logs.md#send-to-azure-storage)
- [Схема событий журнала действий Azure](platform/activity-log-schema.md)
- [Журнал действий Azure](platform/activity-log.md)
- [Примеры CLI для Azure Monitor](samples/cli-samples.md)
- [Примеры для Azure Monitor PowerShell](samples/powershell-samples.md)
- [Пошаговое руководство по REST API Azure Monitor](platform/rest-api-walkthrough.md)
- [Поддерживаемые службы и схемы в журналах ресурсов Azure](./platform/resource-logs-schema.md)
- [Журналы ресурсов Azure](platform/resource-logs.md)
- [Сбор и анализ журналов действий Azure в Azure Monitor](./platform/activity-log.md)
- [Передача журналов ресурсов Azure в рабочую область Log Analytics](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Создание параметров диагностики для отправки журналов платформы и метрик в различные места назначения](platform/diagnostic-settings.md)
- [Экспорт журнала действий Windows Azure](./platform/activity-log.md#legacy-collection-methods)
- [Общие сведения о журналах платформы Azure](platform/platform-logs-overview.md)
- [Потоковая передача журналов платформы Azure в концентратор событий](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Просмотр событий в журнале действий Azure (Azure Monitor)](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Виртуальные машины
- [Включение Azure Monitor для виртуальных машин на портале Azure](./insights/vminsights-enable-portal.md) — добавлены сведения об Azure Arc.
- [Общие сведения о включении Azure Monitor для виртуальных машин](insights/vminsights-enable-overview.md) — добавлены сведения об Azure Arc.
- [Общие сведения об Azure Monitor для виртуальных машин](insights/vminsights-overview.md) — добавлены сведения об Azure Arc.


### <a name="visualizations"></a>Визуализации
- [Источники данных для книг Azure Monitor](platform/workbooks-data-sources.md) — добавлены разделы об оповещениях и пользовательских конечных точках.
- [Устранение неполадок с аналитикой на основе книг Azure Monitor](insights/troubleshoot-workbooks.md) — новая статья.
- [Обновление визуализаций на панели мониторинга Log Analytics](log-query/dashboard-upgrade.md) — новая статьи.



## <a name="may-2020"></a>Май 2020 г.

### <a name="general"></a>Общие сведения

- [Часто задаваемые вопросы по Azure Monitor](faq.md) — добавлен раздел для метрик.
- [Управляемый клиентом ключ Azure Monitor](platform/customer-managed-keys.md) — различные изменения в подготовке к общедоступной версии.
- [Встроенные определения политик для Azure Monitor](./samples/policy-reference.md) — новая статья.
- [Учетные записи хранения, принадлежащие клиенту, для приема данных журнала](platform/private-storage.md) — новая статья.
- [Управление потреблением и затратами для журналов Azure Monitor](platform/manage-cost-storage.md) — добавлено выставление счетов пропорционально кластеру.
- [Использование Приватного канала Azure для безопасного подключения сетей к Azure Monitor](platform/private-link-security.md) — новая статья.


#### <a name="new-resource-manager-template-samples"></a>Примеры новых шаблонов Azure Resource Manager 
- [Примеры шаблонов Resource Manager для службы Azure Monitor](samples/resource-manager-samples.md)
- [Примеры шаблонов Resource Manager для групп действий](samples/resource-manager-action-groups.md)
- [Примеры шаблонов Resource Manager для агентов](samples/resource-manager-agent.md)
- [Примеры шаблонов Resource Manager для Azure Monitor для контейнеров](samples/resource-manager-container-insights.md)
- [Примеры шаблонов Resource Manager для службы Azure Monitor для виртуальных машин](samples/resource-manager-vminsights.md)
- [Примеры шаблонов Resource Manager для параметров диагностики](samples/resource-manager-diagnostic-settings.md)
- [Примеры шаблонов Resource Manager для рабочих областей Log Analytics](samples/resource-manager-workspace.md)
- [Примеры шаблонов Resource Manager для запросов журнала](samples/resource-manager-log-queries.md)
- [Примеры шаблонов Resource Manager для правил генерации оповещений о запросах журнала](samples/resource-manager-alerts-log.md)
- [Примеры шаблонов Resource Manager для правил генерации оповещений о метриках](samples/resource-manager-alerts-metric.md)
- [Примеры шаблонов Resource Manager для книг](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Агенты
- [Установка и настройка расширения "Диагностика Microsoft Azure" (WAD)](platform/diagnostics-extension-windows-install.md) — добавлены детали о настройке диагностики.
- [Обзор агента Log Analytics](platform/log-analytics-agent.md) — добавлены поддерживаемые версии Linux.

### <a name="application-insights"></a>Application Insights

- [Мониторинг приложений, выполняющихся в Функциях Azure, с помощью Application Insights (Azure Monitor)](app/monitor-functions.md) — новая статья.
- [Мониторинг служб Node.js с помощью Azure Application Insights](app/nodejs.md) — общие обновления, включая новый раздел о миграции с предыдущих версий.
- [IP-адреса, используемые Application Insights и Log Analytics](app/ip-addresses.md) — добавлены IP-адреса для веб-перехватчиков и государственных организаций США.
- [Мониторинг приложений, выполняющихся в службе Azure Kubernetes (AKS), с помощью Application Insights (Azure Monitor)](app/kubernetes-codeless.md) — новая статья.
- [Устранение неполадок при отсутствии данных — Application Insights для .NET](app/asp-net-troubleshoot-no-data.md) — добавлен раздел о сборе журналов с помощью dotnet-trace.
- [Используйте анализ изменений приложений в Azure Monitor для поиска проблем веб-приложений](app/change-analysis.md) — несколько обновлений в функции анализа изменений.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Новые и обновленные статьи для предварительной версии приложений на основе рабочей области
- [Схема ресурсов на основе рабочей области Azure Monitor Application Insights](app/apm-tables.md)
- [Создание ресурса на основе рабочей области Azure Monitor Application Insights](app/create-workspace-resource.md)
- [Выражение app() в запросах журнала Azure Monitor](log-query/app-expression.md)
- [Область запросов журнала в Azure Monitor Log Analytics](log-query/scope.md)
- [Запрос ресурсов с помощью Azure Monitor](log-query/cross-workspace-query.md)
- [Стандартные свойства в записях журнала Azure Monitor](./platform/log-standard-columns.md)
- [Структура журналов Azure Monitor](./platform/data-platform-logs.md)





### <a name="containers"></a>Контейнеры
- [Включение Azure Monitor для контейнеров](insights/container-insights-onboard.md) — обновленная таблица конфигурации брандмауэра.
- [Обновление Azure Monitor для контейнеров для метрик](insights/container-insights-update-metrics.md) — обновление для использования управляемых удостоверений для сбора метрик.
- [Затраты на мониторинг для Azure Monitor для контейнеров](insights/container-insights-cost.md) — новая статья.
- [Настройка Azure Monitor для динамических данных контейнеров (предварительная версия)](insights/container-insights-livedata-setup.md) — поддержка привязки новой роли кластера.

### <a name="insights"></a>Аналитика
- [Azure Monitor для Кэша Azure для Redis (предварительная версия)](insights/redis-cache-insights-overview.md) — новая статья.
- [Мониторинг Key Vault с помощью Azure Monitor для Key Vault (предварительная версия)](./insights/key-vault-insights-overview.md) — новая статья.

### <a name="logs"></a>Журналы
- [Создание и настройка Log Analytics с помощью PowerShell](platform/powershell-workspace-configuration.md) — добавлен раздел "Устранение неполадок".
- [Создание рабочей области Log Analytics на портале Azure](learn/quick-create-workspace.md) — добавлен раздел "Устранение неполадок".
- [Создание рабочей области Log Analytics с помощью Azure CLI](learn/quick-create-workspace-cli.md) — добавлен раздел "Устранение неполадок".
- [Удаление и восстановление рабочей области Azure Log Analytics](platform/delete-workspace.md) — обновлены сведения о восстановлении удаленной рабочей области.
- [Функции в запросах журнала Azure Monitor](log-query/functions.md) — удалено примечание о функциях, не содержащих другие функции.
- [Структура журналов Azure Monitor](./platform/data-platform-logs.md) — уточнены описания свойств для таблицы Application Insights.
- [Использование журналов Azure Monitor с Azure Logic Apps и Power Automate](platform/logicapp-flow-connector.md) — добавлен раздел об ограничениях.
- [Использование PowerShell для создания и настройки рабочей области Log Analytics](platform/powershell-workspace-configuration.md) — добавлен раздел "Устранение неполадок".


### <a name="metrics"></a>Метрики
- [Метрики, поддерживаемые Azure Monitor, по типу ресурса](platform/metrics-supported.md) — уточнены гостевые метрики и маршрутизация метрик. 

### <a name="solutions"></a>Решения
- [Оптимизация среды Active Directory с помощью Azure Monitor](insights/ad-assessment.md) — в поддерживаемые версии добавлен Windows Server 2019.
- [Оптимизация среды SQL Server с помощью Azure Monitor](insights/sql-assessment.md) — добавлены поддерживаемые версии SQL Server.


### <a name="virtual-machines"></a>Виртуальные машины
- [Включение Azure Monitor для виртуальных машин](insights/vminsights-enable-overview.md) — добавлены поддерживаемые версии Ubuntu Server. Добавлены поддерживаемые регионы для рабочей области Log Analytics.
- [Создание диаграммы производительности с помощью службы "Azure Monitor для виртуальных машин"](insights/vminsights-performance.md) — добавлен раздел ограничений для недоступных метрик.

### <a name="visualizations"></a>Визуализации
- [Книги Azure Monitor и шаблоны Azure Resource Manager](platform/workbooks-automate.md) — добавлено обновление Resource Manager для развертывания шаблона книги.
- [Группы книг Azure Monitor](platform/workbooks-groups.md) — новая статья.
- [Книги Azure Monitor — преобразование данных JSON с помощью JSONPath](platform/workbooks-jsonpath.md) — новая статья.


## <a name="april-2020"></a>Апрель 2020 г.

### <a name="general"></a>Общие сведения

- [Ключ, управляемый клиентом, в Azure Monitor](platform/customer-managed-keys.md) — добавлен раздел, посвященный асинхронным операциям.
- [Управление рабочими областями Log Analytics в Azure Monitor](platform/manage-access.md) — обновлены разделы, посвященные настраиваемым журналам.

### <a name="alerts"></a>видны узлы

- [Правила действий для оповещений Azure Monitor](platform/alerts-action-rules.md) — добавлено видео.
- [Обзор мониторинга оповещений и уведомлений в Azure](platform/alerts-overview.md) — добавлено видео.

### <a name="application-insights"></a>Application Insights

- [Схема сопоставления приложений в Azure Application Insights](app/app-map.md) — добавлена конфигурация имен облачных ролей для агента Java.
- [Справочник по API агента .NET для Azure Application Insights](app/status-monitor-v2-api-reference.md) — объединены справочники по API.
- [IP-адреса, используемые Application Insights и Log Analytics](app/ip-addresses.md) — обновлены IP-адреса для API App Insights и Log Analytics, веб-перехватчиков групп действий и региона Azure US Government.
- [Мониторинг приложений Java из любого расположения](app/java-standalone-config.md) — новая статья.
- [Мониторинг приложений Java в любой среде](app/java-in-process-agent.md) — новая статья.
- [Мониторинг приложений Java, выполняющихся в любой среде](app/java-standalone-arguments.md) — новая статья.
- [Мониторинг приложений Java, выполняющихся в локальной среде](app/java-on-premises.md) — новая статья.
- [Удаление Application Insights в Visual Studio](app/remove-application-insights.md) — новая статья.
- [Выборка данных телеметрии в Azure Application Insights](app/sampling.md) — исправление в примере Python для фиксированной частоты.

### <a name="containers"></a>Контейнеры

- [Настройка Azure Monitor для контейнеров в Azure Red Hat OpenShift 4.х](insights/container-insights-azure-redhat4-setup.md) — новая статья.
- [Как вручную исправить проблемы с синхронизацией ServiceNow](platform/itsmc-resync-servicenow.md) — новая статья.
- [Как отключить мониторинг кластера Azure и Red Hat OpenShift v4](insights/container-insights-optout-openshift-v4.md) — новая статья.
- [Как отключить мониторинг кластера Red Hat OpenShift версии 3 в Azure](insights/container-insights-optout-openshift-v3.md) — новая статья.
- [Как отключить мониторинг гибридного кластера Kubernetes](insights/container-insights-optout-hybrid.md) — новая статья.

### <a name="insights"></a>Аналитика

- [Мониторинг Azure Key Vault с помощью Azure Monitor для Key Vault (предварительная версия)](insights/key-vault-insights-overview.md) — новая статья.

### <a name="logs"></a>Журналы

- [Ограничения службы Azure Monitor](service-limits.md) — добавлено регулирование запросов пользователя.
- [Управление потреблением и затратами для журналов Azure Monitor](platform/manage-cost-storage.md) — добавлено выставление счетов для кластеров журналов. Добавлен запрос Kusto, который позволяет клиентам с устаревшими ценовыми категориями с оплатой по узлам определить, следует ли им переходить на уровень с оплатой за ГБ или с резервированием мощности.

### <a name="metrics"></a>Метрики

- [Расширенные возможности обозревателя метрик Azure](platform/metrics-charts.md) — добавлен раздел, посвященный агрегированию.

### <a name="workbooks"></a>Workbooks

- [Книги Azure Monitor книги и шаблоны Azure Resource Manager](platform/workbooks-automate.md) — добавлен шаблон Resource Manager для развертывания шаблона книги.

## <a name="march-2020"></a>Март 2020 г.

### <a name="general"></a>Общие сведения

- [Общие сведения о службе Azure Monitor](overview.md) — добавлен видеообзор Azure Monitor.
- [Конфигурация ключей, управляемых клиентом, в Azure Monitor](platform/customer-managed-keys.md) — общие обновления.
- [Ссылка на данные Azure Monitor](/azure/azure-monitor/reference/) — новый сайт.

### <a name="alerts"></a>видны узлы

- [Создание, просмотр и управление оповещениями журнала действий в Azure Monitor](platform/alerts-activity-log.md) — дополнительные пояснения к шаблону Resource Manager.
- [Сведения о работе оповещений о метриках в Azure Monitor](platform/alerts-metric-overview.md) — обновлены для поддержки государственных организаций.
- [Устранение неполадок оповещений и уведомлений Azure Monitor](platform/alerts-troubleshoot.md) — новая статья.

### <a name="application-insights"></a>Application Insights

- [Автоматизация Azure Application Insights с помощью PowerShell](app/powershell.md) — добавлены примеры ARMClient.
- [Непрерывный экспорт данных телеметрии из Application Insights](app/export-telemetry.md) — добавлена таблица со сведениями о структуре экспорта.
- [Включение Snapshot Debugger для приложений .NET в Службе приложений Azure](app/snapshot-debugger-appservice.md) — добавлен пример шаблона Resource Manager.
- [Управление использованием и затратами для Azure Application Insights](app/pricing.md) — добавлена информация об оповещениях об ограничениях данных.
- [Мониторинг приложений Python с помощью Azure Monitor (предварительная версия)](app/opencensus-python.md) — добавлены стандартные метрики.
- [Поддержка сопоставителя с исходным кодом для приложений JavaScript — Azure Monitor Application Insights](app/source-map-support.md) — новая статья.

### <a name="containers"></a>Контейнеры

- [Часто задаваемые вопросы Azure Monitor](faq.md) — обновление для контейнеров Azure Monitor.
- [Настройка мониторинга GPU с помощью Azure Monitor для контейнеров](insights/container-insights-gpu-monitoring.md) — новая статья.

### <a name="insights"></a>Аналитика

- [Решение для управления Office 365 в Azure](insights/solution-office-365.md) — обновленная дата устаревания.

### <a name="logs"></a>Журналы

- [Оптимизация запросов журналов в Azure Monitor](log-query/query-optimization.md) — добавлено условие ЦП для разбора XML и JSON.
- [Удаление и восстановление рабочей области Azure Log Analytics](platform/delete-workspace.md) — добавлено устранение неполадок.
- [Использование журналов Azure Monitor с Azure Logic Apps и Power Automate](platform/logicapp-flow-connector.md) — обновлено для нового соединителя Azure Monitor.

### <a name="metrics"></a>Метрики

- [Устаревшие метрики диска на портале Azure](platform/portal-disk-metrics-deprecation.md) — новая статья.
- [Учебник. Создание диаграммы метрик в Azure Monitor](learn/tutorial-metrics-explorer.md) — добавлено видео.

### <a name="platform-logs"></a>Журналы платформы

- [Сбор и анализ журнала действий Azure в Azure Monitor](./platform/activity-log.md) — перезапись для лучшего объяснения сбора журнала активности с помощью параметров диагностики.

### <a name="virtual-machines"></a>Виртуальные машины

- [Мониторинг виртуальной машины Azure с помощью Azure Monitor](insights/monitor-vm-azure.md) — новая статья.
- [Краткое руководство. Мониторинг виртуальных машин Azure с помощью Azure Monitor](learn/quick-monitor-azure-vm.md) — обновлен для добавления Azure Monitor для виртуальных машин.
- [Оповещения от Azure Monitor для виртуальных машин](insights/vminsights-alerts.md) — новая статья.
- [Общие сведения о включении Azure Monitor для виртуальных машин](insights/vminsights-enable-overview.md) — обновлены ссылки для скачивания агентов.

Общие обновления для общей доступности Azure Monitor для виртуальных машин

- [Общие сведения об Azure Monitor для виртуальных машин](insights/vminsights-overview.md)
- [Azure Monitor для виртуальных машин (GA): вопросы и ответы](insights/vminsights-ga-release-faq.md) 
- [Включение Azure Monitor для виртуальных машин с помощью Политики Azure](./insights/vminsights-enable-policy.md) 
- [Создание диаграммы производительности с помощью службы "Azure Monitor для виртуальных машин"](insights/vminsights-performance.md)
- [Как запрашивать журналы из Azure Monitor для виртуальных машин](insights/vminsights-log-search.md)
- [Просмотр зависимостей приложений с помощью службы "Azure Monitor для виртуальных машин"](insights/vminsights-maps.md) 

### <a name="visualizations"></a>Визуализации

- [Визуализация данных из Azure Monitor](visualizations.md) — обновлена для запланированных уведомлений о нерекомендуемой версии конструктора представлений.

## <a name="february-2020"></a>Февраль 2020 г.

### <a name="agents"></a>Агенты

Несколько обновлений в рамках перезаписи содержимого расширения диагностики.

- [Обзор агентов мониторинга Azure](platform/agents-overview.md) — реструктурированы таблицы для улучшения описания уникальных функций каждого агента.
- [Обзор расширения "Диагностика Azure"](platform/diagnostics-extension-overview.md) — полностью перезаписан.
- [Использование хранилища BLOB-объектов для IIS и хранилища таблиц для событий в Azure Monitor](platform/diagnostics-extension-logs.md) — внесены общие исправления для большей ясности и обновлены некоторые сведения.
- [Установка и настройка расширения "Диагностика Microsoft Azure" (WAD)](platform/diagnostics-extension-windows-install.md) — новая статья. 
- [Схема расширения "Диагностика Windows"](platform/diagnostics-extension-schema-windows.md) — реорганизовано содержимое.
- [Отправка данных из расширения "Диагностика Windows Azure" в Центры событий Azure](platform/diagnostics-extension-stream-event-hubs.md) — полностью переписана и обновлена вся статья.
- [Хранение и просмотр диагностических данных в службе хранилища Azure](../cloud-services/diagnostics-extension-to-storage.md) — полностью переписана и обновлена вся статья.
- [Расширение виртуальной машины с Log Analytics для Windows](../virtual-machines/extensions/oms-windows.md) — лучше объяснена связь с агентом Log Analytics.
- [Расширение виртуальной машины с Azure Monitor для Linux](../virtual-machines/extensions/oms-linux.md) — лучше объяснена связь с агентом Log Analytics.

### <a name="application-insights"></a>Application Insights

- [Строки подключения в Azure Application Insights](app/sdk-connection-string.md) — новая статья.

### <a name="insights-and-solutions"></a>Полезные сведения и решения

#### <a name="azure-monitor-for-containers"></a>Azure Monitor для контейнеров

- [Интеграция Azure Active Directory со Службой контейнеров Azure](../aks/azure-ad-integration-cli.md) — добавлено примечание о создании клиентского приложения для поддержки кластера с поддержкой RBAC, обеспечивающего поддержку Azure Monitor для контейнеров.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor для виртуальных машин

- [Часто задаваемые вопросы об Azure Monitor для виртуальных машин (общедоступная версия)](insights/vminsights-ga-release-faq.md) — изменение способа хранения данных производительности.

#### <a name="office-365"></a>Office 365

- [Решение для управления Office 365 в Azure](insights/solution-office-365.md) — обновленная дата устаревания.


### <a name="logs"></a>Журналы

- [Оптимизация запросов журналов в Azure Monitor](log-query/query-optimization.md) — новая статья.
- [Управление использованием и затратами для журналов Azure Monitor](platform/manage-cost-storage.md) — улучшены примеры запросов, помогающие понять использование.

### <a name="metrics"></a>Метрики

- [Метрики платформы Azure Monitor, экспортируемые с использованием параметров диагностики](platform/metrics-supported-export-diagnostic-settings.md) — добавлен раздел об изменении поведения для значений NULL и нулевых значениях.

### <a name="visualizations"></a>Визуализации

Несколько новых статей с инструкциями по переходу с конструктора представлений на книги.

- [Руководство по переходу с конструктора представлений Azure Monitor на книги](platform/view-designer-conversion-overview.md) — новая статья.
- [Варианты перехода с конструктора представлений Azure Monitor на книги](platform/view-designer-conversion-options.md) — новая статья.
- [Переход с плиток в конструкторе представлений Azure Monitor на книги](platform/view-designer-conversion-tiles.md) — новая статья.
- [Общие сведения о переходе с конструктора представлений Azure Monitor на книги и доступе к ним](platform/view-designer-conversion-access.md) — новая статья.
- [Общие задачи для перехода с конструктора представлений Azure Monitor на книги](platform/view-designer-conversion-tasks.md) — новая статья.
- [Примеры перехода с конструктора представлений Azure Monitor на книги](platform/view-designer-conversion-examples.md) — новая статья.

## <a name="january-2020"></a>Январь 2020 г.

### <a name="general"></a>Общие сведения

- [Что отслеживает Azure Monitor?](monitor-reference.md) — новая статья.

### <a name="agents"></a>Агенты

- [Сбор данных журнала с помощью агента Azure Log Analytics](platform/log-analytics-agent.md) — обновленная таблица требований к сетевому брандмауэру.

### <a name="alerts"></a>видны узлы

- [Создание групп действий и управление ими на портале Azure](platform/action-groups.md) — удален параметр для функций версии 2, который больше не требуется.
- [Создание оповещения метрики с помощью шаблона Resource Manager](platform/alerts-metric-create-templates.md) — добавлен пример для параметра *ignoreDataBefore*.  Добавлены ограничения для правил с несколькими условиями.
- [Использование REST API оповещений Log Analytics](platform/api-alerts.md) — исправлен пример JSON.

### <a name="application-insights"></a>Application Insights

- [IP-адреса, используемые Application Insights и Log Analytics](app/ip-addresses.md) — в раздел "Тестирование доступности" добавлено описание того, как добавить правило для входящих портов, чтобы разрешить трафик, с помощью групп безопасности сети Azure.
- [Устранение неполадок в Azure с помощью Application Insights Profiler](app/profiler-troubleshooting.md) — обновлены общие сведения об устранении неполадок.
- [Выборка данных телеметрии в Azure Application Insights](app/sampling.md) — статья обновлена и реструктурирована для повышения удобочитаемости на основе отзывов пользователей.

### <a name="data-security"></a>Безопасность данных

- [Конфигурация ключей, управляемых клиентом, в Azure Monitor](platform/customer-managed-keys.md) — новая статья.

### <a name="insights-and-solutions"></a>Полезные сведения и решения

#### <a name="azure-monitor-for-containers"></a>Azure Monitor для контейнеров

- [Настройка Azure Monitor для сбора данных агента контейнеров](insights/container-insights-agent-config.md) — добавлены сведения об обновлении агента в Azure Red Hat OpenShift и дополнительные сведения о различиях методов обновления агента.
- [Создание оповещений о производительности для контейнеров Azure Monitor](./insights/container-insights-log-alerts.md) — пересмотрены сведения и обновлены инструкции для создания оповещения на основе данных о производительности, хранящихся в рабочей области, с помощью функции контекстных оповещений рабочей области.
- [Использование мониторинга Kubernetes с Azure Monitor для контейнеров](insights/container-insights-analyze.md) — обновлены статьи с общими сведениями и анализом, посвященные поддержке кластеров Kubernetes Windows.
- [Настройка кластеров Azure Red Hat OpenShift с Azure Monitor для контейнеров](insights/container-insights-azure-redhat-setup.md) — добавлены сведения об обновлении агента в Azure Red Hat OpenShift и дополнительные сведения о различиях методов обновления агента.
- [Настройка гибридных кластеров Kubernetes с Azure Monitor для контейнеров](insights/container-insights-hybrid-setup.md) — в статью добавлено описание дополнительной поддержки защищенного порта 10250 с помощью Kubelet cAdvisor.
- [Как управлять Azure Monitor для агента контейнера](insights/container-insights-manage-agent.md) — обновлены сведения о поведении и конфигурации сбора метрик с помощью Azure Red Hat OpenShift в сравнении с другими типами кластеров Kubernetes.
- [Настройка Azure Monitor для интеграции контейнеров Prometheus](insights/container-insights-prometheus-integration.md) — обновлены сведения о поведении и конфигурации сбора метрик с помощью Azure Red Hat OpenShift в сравнении с другими типами кластеров Kubernetes.
- [Как обновить Azure Monitor для контейнеров метрик](insights/container-insights-update-metrics.md) — обновлены сведения о поведении и конфигурации сбора метрик с помощью Azure Red Hat OpenShift в сравнении с другими типами кластеров Kubernetes.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor для виртуальных машин

- [Azure Monitor для виртуальных машин (общедоступная версия): часто задаваемые вопросы](insights/vminsights-ga-release-faq.md) — добавлены сведения об обновлении рабочей области и агентов до новой версии.

#### <a name="office-365"></a>Office 365

- [Решение для управления Office 365 в Azure](insights/solution-office-365.md) — добавлены дополнительные сведения и часто задаваемые вопросы о переходе на решение Office 365 в Azure Sentinel. Удален раздел о подключении.

### <a name="logs"></a>Журналы

- [Управление рабочими областями Log Analytics в Azure Monitor](platform/manage-access.md) — обновлено описание Not Action.
- [Управление использованием и затратами на основе журналов Azure Monitor](platform/manage-cost-storage.md) — добавлено уточнение о вычислении объема данных в разделе "Модель ценообразования".
- [Использование шаблонов Azure Resource Manager для создания и настройки рабочей области Log Analytics](./samples/resource-manager-workspace.md) — в шаблон добавлены новые ценовые категории.

### <a name="platform-logs"></a>Журналы платформы

- [Сбор данных журнала действий Azure с помощью параметров диагностики в Azure Monitor](./platform/activity-log.md) — добавлены дополнительные сведения об измененных свойствах.
- [Экспорт журнала действий Azure](./platform/activity-log.md#legacy-collection-methods) — статья обновлена с учетом изменений пользовательского интерфейса. 

## <a name="december-2019"></a>Декабрь 2019 г.

### <a name="agents"></a>Агенты

- [Подключение компьютеров Linux к Azure Monitor](platform/agent-linux.md) — новая статья.

### <a name="alerts"></a>видны узлы

- [Создание оповещения метрики с помощью шаблона Resource Manager](platform/alerts-metric-create-templates.md) — добавлен пример для настраиваемой метрики.
- [Создание оповещений с динамическими пороговыми значениями в Azure Monitor](platform/alerts-dynamic-thresholds.md) — добавлен раздел, посвященный интерпретации диаграмм с динамическими порогами.
- [Обзор мониторинга оповещений и уведомлений в Azure](platform/alerts-overview.md) — обновлен запрос Resource Graph.
- [Поддерживаемые ресурсы для оповещений метрик в Azure Monitor](platform/alerts-metric-near-real-time.md) — обновлено описание поддерживаемых метрик и измерений.
- [Переключение с устаревших API оповещений Log Analytics на новые API оповещений Azure](platform/alerts-log-api-switch.md) — добавлено примечание для измененного имени оповещения.
- [Сведения о работе оповещений о метриках в Azure Monitor](platform/alerts-metric-overview.md) — добавлены поддерживаемые типы ресурсов для мониторинга в соответствующем масштабе.

### <a name="application-insights"></a>Application Insights

- [Использование Application Insights для приложений службы рабочих ролей (не HTTP-приложений)](app/worker-service.md) — в код C# добавлен уровень ведения журнала по умолчанию. Обновлена версия ссылки на пакет.
- [Справочник по файлу ApplicationInsights.config в Azure](app/configuration-with-applicationinsights-config.md) — обновлен пример кода.
- [Автоматизация Application Insights Azure с помощью PowerShell](app/powershell.md) — обновлен шаблон Resource Manager.
- [Создание ресурса Azure Application Insights](app/create-new-resource.md) — добавлено примечание для глобально уникального имени.
- [Диагностика с помощью Live Metrics Stream в Azure Application Insights](app/live-stream.md) — обновлена версия пакета SDK для ASP.NET Core.
- [Счетчики событий в Application Insights](app/eventcounters.md) — обновлены категория и таблица для customMetrics.
- [Изучение журналов трассировки Java в Azure Application Insights](app/java-trace-logs.md) — добавлена конфигурация для порога ведения журнала агента Java.
- [IP-адреса, используемые Application Insights и Log Analytics](app/ip-addresses.md) — обновлены IP-адреса для Live Metrics Stream.
- [Мониторинг производительности служб приложений Azure](app/azure-web-apps.md) — добавлена поддержка ASP.NET Core 3.0. 
- [Мониторинг приложений Python с помощью Azure Monitor (предварительная версия)](app/opencensus-python.md) — добавлены пояснения к сопоставлению схемы OpenCensus Python и схемы Azure Monitor.
- [Заметки о выпуске Azure Application Insights](app/release-notes.md) — добавлены заметки о прежних выпусках.
- [Каналы телеметрии в Azure Application Insights](app/telemetry-channels.md) — обновлено время, в течение которого данные не отклоняются при продолжительной потере подключения.
- [Выборка телеметрии в Azure Application Insights](app/sampling.md) — исправлен фрагмент кода для пользовательского TelemetryInitializer.
- [Устранение неполадок Application Insights в веб-проекте Java](app/java-troubleshoot.md) — удалено утверждение о неподдерживаемой коллекции зависимостей в JDK 9.

### <a name="insights-and-solutions"></a>Полезные сведения и решения

- [Azure Monitor для контейнеров: вопросы и ответы](./faq.md) — добавлен вопрос о полях "Образ" и "Имя".
- [Решение Аналитики Azure SQL в Azure Monitor](insights/azure-sql.md) — обновлена поддержка ожидания базой данных управляемого экземпляра.
- [Настройка Azure Monitor для сбора данных агента контейнера](insights/container-insights-agent-config.md) — добавлен параметр для enrich_container_logs.
- [Настройка гибридных кластеров Kubernetes с Azure Monitor для контейнеров](insights/container-insights-hybrid-setup.md) — добавлен раздел "Устранение неполадок".
- [Мониторинг состояния репликации Active Directory с помощью Azure Monitor](insights/ad-replication-status.md) — обновлены предварительные требования к .NET Framework.
- [Решение "Монитор производительности сети" в Azure](insights/network-performance-monitor.md) — добавлены поддерживаемые регионы.
- [Оптимизация среды Active Directory с помощью Azure Monitor](insights/ad-assessment.md) — обновлены предварительные требования к .NET Framework.
- [Оптимизация среды SQL Server с помощью Azure Monitor](insights/sql-assessment.md) — обновлены предварительные требования к .NET Framework.
- [Оптимизация среды System Center Operations Manager с помощью Azure Log Analytics](insights/scom-assessment.md) — обновлены предварительные требования к .NET Framework.
- [Поддерживаемые подключения с использованием Соединителя управления ИТ-услугами в Azure Log Analytics](platform/itsmc-connections.md) — в идентификатор и секрет клиента добавлено значение для Нью-Йорка.

### <a name="logs"></a>Журналы

- [Удаление и восстановление рабочей области Azure Log Analytics](platform/delete-workspace.md) — добавлен метод PowerShell.
- [Проектирование развертывания журналов Azure Monitor](platform/design-logs-deployment.md) — повышена скорость приема для рабочей области.

### <a name="metrics"></a>Метрики

- [Метрики платформы Azure Monitor, которые можно экспортировать с помощью раздела "Параметры диагностики"](platform/metrics-supported-export-diagnostic-settings.md) — новая статья.

### <a name="platform-logs"></a>Журналы платформы

Несколько статей были обновлены в ходе реструктуризации содержимого для журналов платформы с учетом новой функции настройки журнала действий с помощью параметров диагностики.

- [Архивация журналов ресурсов Azure в учетной записи хранения](./platform/resource-logs.md#send-to-azure-storage)
- [Схема событий журнала действий Azure](platform/activity-log-schema.md)
- [Ограничения службы Azure Monitor](service-limits.md)
- [Сбор и анализ журналов действий Azure в рабочей области Log Analytics](./platform/activity-log.md)
- [Сбор данных журнала действий Azure с помощью параметров диагностики в Azure Monitor (предварительная версия)](./platform/activity-log.md)
- [Передача журналов действий Azure в рабочую область Log Analytics для нескольких арендаторов](./platform/activity-log.md)
- [Передача журналов ресурсов Azure в рабочую область Log Analytics](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Создание параметра диагностики в Azure с помощью шаблона Resource Manager](./samples/resource-manager-diagnostic-settings.md)
- [Создание параметра диагностики для сбора журналов и метрик в Azure](platform/diagnostic-settings.md)
- [Экспорт журнала действий Windows Azure](./platform/activity-log.md#legacy-collection-methods)
- [Общие сведения о журналах платформы Azure](platform/platform-logs-overview.md)
- [Потоковая передача данных мониторинга Azure в концентратор событий](platform/stream-monitoring-data-event-hubs.md)
- [Потоковая передача журналов платформы Azure в концентратор событий](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Краткие руководства

- [Создание диаграммы метрик в Azure Monitor](learn/tutorial-metrics-explorer.md) — новая статья.
- [Сбор журналов ресурсов из ресурса Azure и их анализ в Azure Monitor](learn/tutorial-resource-logs.md) — новая статья.
- [Мониторинг ресурса Azure с помощью Azure Monitor](learn/quick-monitor-azure-resource.md) — новая статья.
   
## <a name="next-steps"></a>Дальнейшие действия

- Если вы хотите внести свой вклад в разработку документации по Azure Monitor, ознакомьтесь с [руководством для разработчиков документации](/contribute/).