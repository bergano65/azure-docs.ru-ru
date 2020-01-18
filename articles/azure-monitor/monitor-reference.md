---
title: Что отслеживается Azure Monitor
description: Справочные материалы по всем службам и другим ресурсам, отслеживаемым Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: ac9ded703ed18abd9543b6e32e59afe6ccf711a9
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263427"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Что отслеживается Azure Monitor?
В этой статье описываются различные приложения и службы, которые отслеживаются Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Ценные сведения и основные решения
Основные аналитические сведения и решения считаются частью Azure Monitor и соответствуют соглашениям о поддержке и уровне обслуживания для Azure. Они поддерживаются во всех регионах Azure, где доступна Azure Monitor.

### <a name="insights"></a>Аналитика

Insights предоставляет настраиваемый интерфейс мониторинга для конкретных приложений и служб. Они собираются и анализируют журналы и метрики.

| Аналитические сведения | Description |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Расширяемая служба управления производительностью приложений (APM) для мониторинга активного веб-приложения на любой платформе. |
| [Azure Monitor для контейнеров](insights/container-insights-overview.md) | Отслеживает производительность рабочих нагрузок контейнера, развернутых в экземплярах контейнеров Azure или управляемых кластерах Kubernetes, размещенных в службе Kubernetes Azure (AKS). |
| [Azure Monitor для Cosmos DB (Предварительная версия)](insights/cosmosdb-insights-overview.md) | Общие сведения о производительности, сбоях, емкости и работоспособности всех Azure Cosmos DBных ресурсов в едином интерактивном интерфейсе. |
| [Azure Monitor для сетей (Предварительная версия)](insights/network-insights-overview.md) | Предоставляет исчерпывающее представление о работоспособности и метриках для всех сетевых ресурсов. Функция расширенного поиска помогает определить зависимости ресурсов, включая такие сценарии, как идентификация ресурсов, на которых размещен ваш веб-сайт, путем простого поиска имени веб-сайта. |
[Azure Monitor для групп ресурсов (Предварительная версия)](insights/resource-group-insights.md) |  Рассмотрение и диагностика любых проблем, возникающих при работе отдельных ресурсов, и предоставление контекста в качестве состояния работоспособности и производительности группы ресурсов в целом. |
| [Azure Monitor для хранилища (Предварительная версия)](insights/storage-insights-overview.md) | Обеспечивает полный мониторинг учетных записей хранения Azure, обеспечивая единое представление производительности, емкости и доступности служб хранилища Azure. |
| [Azure Monitor для виртуальных машин (Предварительная версия)](insights/container-insights-overview.md) | Отслеживает виртуальные машины Azure и масштабируемые наборы виртуальных машин в масштабе. Это решение анализирует производительность и работоспособность виртуальных машин Windows и Linux, отслеживает их процессы и зависимости на других ресурсах, а также внешние процессы. |

### <a name="core-solutions"></a>Основные решения

Решения основаны на запросах журналов и представлениях, настроенных для конкретного приложения или службы. Они собираются и анализируют только журналы и являются устаревшими с течением времени в пользу ценной информации.

| Решение | Description |
|:---|:---|
| [Работоспособность агента](insights/solution-agenthealth.md) | Анализ работоспособности и конфигурации агентов Log Analytics. |
| [Управление оповещениями](platform/alert-management-solution.md) | Анализ оповещений, собранных из System Center Operations Manager, Nagios или Zabbix. |
| [Схема услуги](insights/service-map.md) | Автоматически обнаруживает компоненты приложения в системах Windows и Linux и сопоставляет взаимодействие между службами. Те же функциональные возможности обеспечиваются в   |



## <a name="azure-services"></a>Службы Azure
В следующей таблице перечислены службы Azure и данные, которые они собираются в Azure Monitor. 

- Метрики — служба автоматически собирает метрики в Azure Monitor метрики. 
- Журналы. служба поддерживает параметры диагностики, которые могут выполнять сбор журналов и метрик платформы для Azure Monitor журналов.
- Insights — имеется аналитика, доступная для службы, которая предоставляет настраиваемый интерфейс мониторинга для службы.

| Служба | Метрики | Журналы | Аналитические сведения | Примечания |
|:---|:---|:---|:---|:---|
|Active Directory | Нет | Да | [Да](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Нет | Нет | Нет |  |
|Доменные службы Active Directory | Нет | Да | Нет |  |
|Журнал действий | Нет | Да | Нет | |
|Advanced Threat Protection | Нет | Нет | Нет |  |
|Помощник | Нет | Нет | Нет |  |
|AI Builder | Нет | Нет | Нет |  |
|Analysis Services | Да | Да | Нет |  |
|API для FHIR | Нет | Нет | Нет |  |
|Управление API | Да | Да | Нет |  |
|App Service | Да | Да | Нет |  |
|AppConfig | Нет | Нет | Нет |  |
|Шлюз приложений | Да | Да | Нет |  |
|Служба аттестации | Нет | Нет | Нет |  |
|Автоматизация | Да | Да | Нет |  |
|Azure Service Manager (RDFE) | Нет | Нет | Нет |  |
|Архивация | Нет | Да | Нет |  |
|Бастион | Нет | Нет | Нет |  |
|Пакетная служба | Да | Да | Нет |  |
|Модуль искусственного интеллекта пакетной службы | Нет | Нет | Нет |  |
|Служба "Блокчейн Azure" | Нет | Да | Нет |  |
|Blueprints | Нет | Нет | Нет |  |
|Служба программ-роботов | Нет | Нет | Нет |  |
|Облачные службы | Да | Да | Нет | Агент, необходимый для наблюдения за гостевой операционной системой и рабочими процессами.  |
|Cloud Shell | Нет | Нет | Нет |  |
|Когнитивные сервисы | Да | Да | Нет |  |
|Экземпляры контейнеров | Да | Нет | Нет |  |
|Реестр контейнеров | Да | Да | Нет |  |
|Сеть CDN | Нет | Да | Нет |  |
|Cosmos DB | Да | Да | [Да](insights/cosmosdb-insights-overview.md) |  |
|Управление затратами | Нет | Нет | Нет |  |
|Data Box | Нет | Нет | Нет |  |
|Gen2 каталога данных | Нет | Нет | Нет |  |
|Data Explorer | Да | Да | Нет |  |
|Фабрика данных | Да | Да | Нет |  |
|Фабрика данных версии 2 | Нет | Да | Нет |  |
|Data Share | Нет | Нет | Нет |  |
|База данных для MariaDB | Да | Да | Нет |  |
|База данных для MySQL | Да | Да | Нет |  |
|База данных для PostgreSQL | Да | Да | Нет |  |
|Служба миграции баз данных | Нет | Нет | Нет |  |
|Databricks | Нет | Да | Нет |  |
|Защита от атак DDoS | Да | Да | Нет |  |
|Разработка и операции | Нет | Нет | Нет |  |
|DNS | Да | Нет | Нет |  |
|Имена доменов | Нет | Нет | Нет |  |
|DPS | Нет | Нет | Нет |  |
|Участие клиентов Dynamics 365 | Нет | Нет | Нет |  |
|Финансы и операции Dynamics 365 | Нет | Нет | Нет |  |
|Служба "Сетка событий Azure" | Да | Нет | Нет |  |
|Концентраторы событий | Да | Да | Нет |  |
|ExpressRoute | Да | Да | Нет |  |
|Брандмауэр | Да | Да | Нет |  |
|Front Door | Да | Да | Нет |  |
|Функции | Да | Да | Нет |  |
|HDInsight | Нет | Да | Нет |  |
|HPC Cache | Нет | Нет | Нет |  |
|Information Protection | Нет | Да | Нет |  |
|Intune | Нет | Да | Нет |  |
|IoT Central | Нет | Нет | Нет |  |
|Центр Интернета вещей | Да | Да | Нет |  |
|Key Vault | Да | Да | Нет |  |
|Служба Kubernetes (AKS) | Нет | Нет | [Да](insights/container-insights-overview.md)  |  |
|Load Balancer | Да | Да | Нет |  |
|Приложения логики | Да | Да | Нет |  |
|Служба машинного обучения | Нет | Нет | Нет |  |
|Управляемые приложения  | Нет | Нет | Нет |  |
|Карты  | Нет | Нет | Нет |  |
|Службы мультимедиа | Да | Да | Нет |  |
|Microsoft Flow | Нет | Нет | Нет |  |
|Компьютеры, управляемые Майкрософт | Нет | Нет | Нет |  |
|Microsoft PowerApps | Нет | Нет | Нет |  |
|Microsoft Social Engagement | Нет | Нет | Нет |  |
|Microsoft Stream | Да | Да | Нет |  |
|Миграция | Нет | Нет | Нет |  |
|Многофакторная идентификация | Нет | Да | Нет |  |
|Наблюдатель за сетями | Да | Да | Нет |  |
|Концентраторы уведомлений | Да | Нет | Нет |  |
|Открытые наборы данных | Нет | Нет | Нет |  |
|Политика | Нет | Нет | Нет |  |
|Power BI | Да | Да | Нет |  |
|Power BI Embedded | Нет | Нет | Нет |  |
|Приватный канал | Нет | Нет | Нет |  |
|Платформа связи с очередью проекта | Нет | Нет | Нет |  |
|Red Hat OpenShift. | Нет | Нет | Нет |  |
|Кэш Redis | Да | Да | Нет |  |
|Resource Graph | Нет | Нет | Нет |  |
|Диспетчер ресурсов | Нет | Нет | Нет |  |
|Поиск в розничной торговле — по Bing | Нет | Нет | Нет |  |
|Поиск | Да | Да | Нет |  |
|Служебная шина | Да | Да | Нет |  |
|Service Fabric | Нет | Да | Нет | Агент, необходимый для наблюдения за гостевой операционной системой и рабочими процессами.  |
|Портал регистрации | Нет | Нет | Нет |  |
|Восстановление сайтов | Нет | Да | Нет |  |
|Пружинная облачная служба | Нет | Нет | Нет |  |
|Хранилище данных SQL | Да | Да | Нет |  |
|База данных SQL | Да | Да | Нет |  |
|SQL Server Stretch Database | Да | Да | Нет |  |
|Стек | Нет | Нет | Нет |  |
|Хранилище | Да | Нет | [Да](insights/storage-insights-overview.md) |  |
|Кэш хранилища | Нет | Нет | Нет |  |
|Службы синхронизации хранилища | Нет | Нет | Нет |  |
|Stream Analytics | Да | Да | Нет |  |
|Time Series Insights | Да | Да | Нет |  |
|TINA | Нет | Нет | Нет |  |
|Диспетчер трафика | Да | Да | Нет |  |
|Универсальная печать | Нет | Нет | Нет |  |
|Наборы для масштабирования виртуальных машин | Нет | Да | [Да](insights/vminsights-overview.md) | Агент, необходимый для наблюдения за гостевой операционной системой и рабочими процессами. |
|Виртуальные машины | Да | Да | [Да](insights/vminsights-overview.md) | Агент, необходимый для наблюдения за гостевой операционной системой и рабочими процессами. |
|Виртуальная сеть | Да | Да | [Да](insights/network-insights-overview.md) |  |
|Виртуальная сеть — журналы потоков NSG | Нет | Да | Нет |  |
|Шлюз VPN | Да | Да | Нет |  |
|Виртуальный рабочий стол Windows | Нет | Нет | Нет |  |


## <a name="product-integrations"></a>Интеграции продукта
Службы и решения в следующей таблице хранят свои данные в Log Analytics рабочей области, чтобы их можно было проанализировать с помощью других данных журнала, собираемых Azure Monitor.

| Продукт или служба | Description |
|:---|:---|
| [Служба автоматизации Azure](/azure/automation/) | Управление обновлениями операционной системы и отслеживание изменений на компьютерах Windows и Linux. См. раздел [Отслеживание изменений](../automation/change-tracking.md) и [Управление обновлениями](../automation/automation-update-management.md). |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | Классификация и дополнительно Защита документов и сообщений электронной почты. См. раздел [Central Reporting for Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Центр безопасности Azure](/azure/security-center/) | Собирайте и анализируйте события безопасности и проводите анализ угроз. См. раздел [сбор данных в центре безопасности Azure](/azure/security-center/security-center-enable-data-collection) . |
| [Azure Sentinel](/azure/sentinel/) | Подключается к различным источникам, включая Office 365 и Amazon Web Services Cloud. См. раздел [Подключение к источникам данных](/azure/sentinel/connect-data-sources). |
| [Аналитика Key Vault](insights/azure-key-vault.md) | Анализируйте журналы Azure Key Vault AuditEvent. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Создайте параметр диагностики для отправки журналов в Azure Monitor. См. статью [Отправка данных журнала в хранилище, концентраторы событий или log Analytics в Intune (Предварительная версия)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Сеть  | [Монитор производительности сети](insights/network-performance-monitor.md) — Отслеживайте сетевые подключения и производительность конечных точек служб и приложений.<br>[Шлюз приложений Azure](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) . анализ журналов и метрик из шлюза приложений Azure.<br>[Аналитика трафика](/azure/network-watcher/traffic-analytics) — анализирует журналы потоков группы безопасности сети (NSG) наблюдателя за сетями, чтобы получить представление о потоке трафика в облаке Azure. |
| [Office 365](insights/solution-office-365.md) | Мониторинг среды Office 365. Обновленная версия с улучшенной адаптации доступна через метку Azure. |
| [Аналитика SQL](insights/azure-sql.md) | Отслеживайте производительность баз данных SQL Azure, эластичных пулов и управляемых экземпляров при масштабировании и в нескольких подписках. |
| [Surface Hub](insights/surface-hubs.md) | Следите за работоспособностью и использованием устройств Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Собирайте данные от агентов Operations Manager, подключив их группу управления к Azure Monitor. См. раздел [Connect Operations Manager to Azure Monitor](platform/om-agents.md)<br> Оцените риски и работоспособность группы управления System Center Operations Manager с помощью решения для [оценки Operations Manager](insights/scom-assessment.md) . |
| [Комнаты Microsoft Teams](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Интегрированное комплексное управление Microsoft Teams комнаты. |
| [Центр приложений Visual Studio](https://docs.microsoft.com/appcenter/) | Создавайте, тестируйте и распространяйте приложения, а затем отслеживайте их состояние и использование. См. статью [Начало анализа мобильного приложения с помощью App Center и Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Центр обновления Windows соответствие требованиям](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) — Оценка обновлений настольных систем Windows.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) — интегрируется с Configuration Manager, чтобы предоставить подробные сведения и аналитические решения для принятия более обоснованных решений о подготовке обновлений для клиентов Windows. |



## <a name="other-solutions"></a>Другие решения
Доступны другие решения для мониторинга различных приложений и служб, но активная разработка была остановлена и может быть недоступна во всех регионах. Они охватываются соглашением об уровне обслуживания данных Azure Log Analytics.

| Решение | Description |
|:---|:---|
| [Оценка Active Directory](insights/ad-assessment.md) | Оцените риски и работоспособность Active Directoryных сред. |
| [Состояние репликации Active Directory](insights/ad-replication-status.md) | Регулярно контролируйте среду Active Directory для любых сбоев репликации. |
| [Анализ журнала действий](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | Анализ записей журнала действий с помощью стандартных запросов и представлений журнала. |
| [Аналитика DNS (Предварительная версия)](insights/dns-analytics.md) | Собирает, анализирует и сопоставляет журналы аналитики и аудита Windows DNS и другие связанные данные с DNS-серверов. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Собирайте, просматривайте и анализируйте метрики работоспособности и производительности Cloud Foundry в нескольких развертываниях. |
| [Контейнеры](insights/containers.md) | Просмотр узлов контейнеров DOCKER и Windows и управление ими. |
| [Оценки по запросу](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Оцените и оптимизируйте доступность, безопасность и производительность локальных, гибридных и облачных технологических сред Майкрософт. |
| [Оценка SQL](insights/sql-assessment.md) | Оцените риски и работоспособность SQL Serverных сред.  |
| [Wire Data](insights/wire-data.md) | Объединенные данные о сети и производительности, собранные с компьютеров, подключенных к ОС Windows, и с помощью агента Log Analytics. |


## <a name="third-party-integration"></a>Интеграция со сторонними производителями

| Решение | Description |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Соединитель управления ИТ-услугами (ITSMC) позволяет подключить Azure к поддерживаемым продуктам или службам управления ИТ-услугами.  |


## <a name="resources-outside-of-azure"></a>Ресурсы за пределами Azure
Azure Monitor может получать данные из ресурсов за пределами Azure с помощью методов, перечисленных в следующей таблице.

| Ресурс | Метод |
|:---|:---|
| Приложения | Отслеживайте веб-приложения за пределами Azure с помощью Application Insights. См. раздел [что такое Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Виртуальные машины | Используйте агент Log Analytics для получения данных из гостевой операционной системы виртуальных машин в других облачных средах или в локальной среде. См. раздел [Получение данных журнала с помощью агента log Analytics](platform/log-analytics-agent.md). |
| Клиент REST API | Для записи данных в журналы Azure Monitor и метрики из любого клиента REST API доступны отдельные API-интерфейсы. См. статью [Отправка данных журнала в Azure Monitor с помощью API сборщика данных HTTP](platform/data-collector-api.md) для журналов и [Отправка пользовательских метрик для ресурса Azure в хранилище метрик Azure Monitor с помощью REST API](platform/metrics-store-custom-rest-api.md) для метрик. |



## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [платформе данных Azure Monitor, в которой хранятся журналы и метрики, собранные аналитическими сведениями и решениями](platform/data-platform.md).
- Пройдите [руководство по мониторингу ресурсов Azure](learn/tutorial-resource-logs.md).
- Пройдите [учебник по написанию запроса журнала для анализа данных в журналах Azure Monitor](learn/tutorial-resource-logs.md).
- Пройдите [руководство по созданию диаграммы метрик для анализа данных в Azure Monitor метриках](learn/tutorial-metrics-explorer.md).

 
