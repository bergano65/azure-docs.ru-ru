---
title: Что нового в документации по Azure Monitor?
description: Важные обновления добавляются в документацию по Azure Monitor каждый месяц.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: a2e6d2a459a6713aa9372496fc3a933c0a886ed9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802610"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Что нового в документации по Azure Monitor?
В этой статье приводятся списки новых или значительно измененных статей об Azure Monitor. Он будет обновляться в первую неделю каждого месяца, чтобы включать в себя обновления статей за предыдущий месяц.

## <a name="march-2020"></a>Март 2020 г.

### <a name="general"></a>Общие сведения
- [Общие сведения о службе Azure Monitor](overview.md) — добавлен видеообзор Azure Monitor.
- [Конфигурация ключей, управляемых клиентом, в Azure Monitor](platform/customer-managed-keys.md) — общие обновления.
- [Ссылка на данные Azure Monitor](/azure/azure-monitor/reference) — новый сайт.

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
- [Поддержка сопоставителя с исходным кодом для приложений JavaScript — Azure Monitor Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/source-map-support) — новая статья.


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
- [Устаревшие метрики диска на портале Azure](https://docs.microsoft.com/azure/azure-monitor/platform/portal-disk-metrics-deprecation) — новая статья.
- [Учебник. Создание диаграммы метрик в Azure Monitor](learn/tutorial-metrics-explorer.md) — добавлено видео.

### <a name="platform-logs"></a>Журналы платформы
- [Сбор и анализ журнала действий Azure в Azure Monitor](platform/activity-log-collect.md) — перезапись для лучшего объяснения сбора журнала активности с помощью параметров диагностики.

### <a name="virtual-machines"></a>Виртуальные машины
- [Мониторинг виртуальной машины Azure с помощью Azure Monitor](insights/monitor-vm-azure.md) — новая статья.
- [Краткое руководство. Мониторинг виртуальных машин Azure с помощью Azure Monitor](learn/quick-monitor-azure-vm.md) — обновлен для добавления Azure Monitor для виртуальных машин.
- [Оповещения от Azure Monitor для виртуальных машин](insights/vminsights-alerts.md) — новая статья.
- [Общие сведения о включении Azure Monitor для виртуальных машин](insights/vminsights-enable-overview.md) — обновлены ссылки для скачивания агентов.


Общие обновления для общей доступности Azure Monitor для виртуальных машин
- [Общие сведения об Azure Monitor для виртуальных машин](insights/vminsights-overview.md)
- [Azure Monitor для виртуальных машин (GA): вопросы и ответы](insights/vminsights-ga-release-faq.md) 
- [Включение Azure Monitor для виртуальных машин с помощью Политики Azure](insights/vminsights-enable-at-scale-policy.md) 
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
- [Хранение и просмотр диагностических данных в службе хранилища Azure](/azure/cloud-services/diagnostics-extension-to-storage) — полностью переписана и обновлена вся статья.
- [Расширение виртуальной машины с Log Analytics для Windows](../virtual-machines/extensions/oms-windows.md) — лучше объяснена связь с агентом Log Analytics.
- [Расширение виртуальной машины с Azure Monitor для Linux](../virtual-machines/extensions/oms-linux.md) — лучше объяснена связь с агентом Log Analytics.




### <a name="application-insights"></a>Application Insights
- [Строки подключения в Azure Application Insights](app/sdk-connection-string.md) — новая статья.

### <a name="insights-and-solutions"></a>Полезные сведения и решения

#### <a name="azure-monitor-for-containers"></a>Azure Monitor для контейнеров
- [Интеграция Azure Active Directory со Службой контейнеров Azure](../aks/azure-ad-integration.md) — добавлено примечание о создании клиентского приложения для поддержки кластера с поддержкой RBAC, обеспечивающего поддержку Azure Monitor для контейнеров.

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
- [Создание оповещений о производительности для контейнеров Azure Monitor](insights/container-insights-alerts.md) — пересмотрены сведения и обновлены инструкции для создания оповещения на основе данных о производительности, хранящихся в рабочей области, с помощью функции контекстных оповещений рабочей области.
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
- [Использование шаблонов Azure Resource Manager для создания и настройки рабочей области Log Analytics](platform/template-workspace-configuration.md) — в шаблон добавлены новые ценовые категории.


### <a name="platform-logs"></a>Журналы платформы
- [Сбор данных журнала действий Azure с помощью параметров диагностики в Azure Monitor](platform/diagnostic-settings-legacy.md) — добавлены дополнительные сведения об измененных свойствах.
- [Экспорт журнала действий Azure](platform/activity-log-export.md) — статья обновлена с учетом изменений пользовательского интерфейса. 





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
- [Пакеты NuGet для Azure Monitor и Application Insights ](app/nuget.md) — обновлены версии пакетов.
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
- [Azure Monitor для контейнеров: вопросы и ответы](insights/container-insights-faq.md) — добавлен вопрос о полях "Образ" и "Имя".
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

- [Архивация журналов ресурсов Azure в учетной записи хранения](platform/resource-logs-collect-storage.md)
- [Схема событий журнала действий Azure](platform/activity-log-schema.md)
- [Ограничения службы Azure Monitor](service-limits.md)
- [Сбор и анализ журналов действий Azure в рабочей области Log Analytics](platform/activity-log-collect.md)
- [Сбор данных журнала действий Azure с помощью параметров диагностики в Azure Monitor (предварительная версия)](platform/diagnostic-settings-legacy.md)
- [Передача журналов действий Azure в рабочую область Log Analytics для нескольких арендаторов](platform/activity-log-collect-tenants.md)
- [Передача журналов ресурсов Azure в рабочую область Log Analytics](platform/resource-logs-collect-workspace.md)
- [Создание параметра диагностики в Azure с помощью шаблона Resource Manager](platform/diagnostic-settings-template.md)
- [Создание параметра диагностики для сбора журналов и метрик в Azure](platform/diagnostic-settings.md)
- [Экспорт журнала действий Windows Azure](platform/activity-log-export.md)
- [Общие сведения о журналах платформы Azure](platform/platform-logs-overview.md)
- [Потоковая передача данных мониторинга Azure в концентратор событий](platform/stream-monitoring-data-event-hubs.md)
- [Потоковая передача журналов платформы Azure в концентратор событий](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Краткие руководства

- [Создание диаграммы метрик в Azure Monitor](learn/tutorial-metrics-explorer.md) — новая статья.
- [Сбор журналов ресурсов из ресурса Azure и их анализ в Azure Monitor](learn/tutorial-resource-logs.md) — новая статья.
- [Мониторинг ресурса Azure с помощью Azure Monitor](learn/quick-monitor-azure-resource.md) — новая статья.
   
## <a name="next-steps"></a>Дальнейшие действия

- Если вы хотите внести свой вклад в разработку документации по Azure Monitor, ознакомьтесь с [руководством для разработчиков документации](https://docs.microsoft.com/contribute/).