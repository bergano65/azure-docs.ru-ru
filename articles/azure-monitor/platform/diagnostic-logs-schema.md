---
title: Журналы ресурсов Azure поддерживали службы и схемы
description: Понимание поддерживаемых служб и схемы событий для журналов ресурсов Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 8abd8767d9bb7e3c4336f6600b94f6b3f4ea48f1
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380518"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Поддерживаемые службы, схемы и категории для журналов ресурсов Azure

> [!NOTE]
> Системы ресурсов ранее были известны как диагностические журналы.

[Журналы ресурсов Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md) представляют журналы, излучаемые службами Azure, описывающие работу этих служб или ресурсов. Все журналы ресурсов, доступные через Azure Monitor, имеют общую схему верхнего уровня, с гибкостью для каждой службы, чтобы излучать уникальные свойства для своих собственных событий.

Сочетание типа ресурса (доступного в свойстве `resourceId`) и свойства `category` является уникальным идентификатором схемы. В этой статье описывается схема верхнего уровня для журналов ресурсов и ссылки на схему для каждой службы.

## <a name="top-level-resource-logs-schema"></a>Схема журналов ресурсов верхнего уровня

| Имя | Обязательный/необязательный | Описание |
|---|---|---|
| time | Обязательно | Метка времени события (UTC). |
| resourceId | Обязательно | Идентификатор ресурса, создавшего событие. Для служб клиента он имеет формат "/tenants/ИД_клиента/providers/имя_поставщика". |
| tenantId | Требуется для журналов клиента | Идентификатор клиента Active Directory клиента, к которому привязано это событие. Это свойство используется только для журналов уровня клиентов и не отображается в журналах уровня ресурсов. |
| operationName | Обязательно | Имя операции, которую представляет это событие. Если событие представляет операцию RBAC, то это имя операции RBAC (например, Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Обычно моделируются в виде операции Resource Manager, даже если они фактически не являются задокументированными операциями Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Необязательно | Версия API, связанного с операцией, если операция operationName выполнялась с помощью API (например, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Если для этой операции не существует соответствующего API, то версия представляет версию этой операции, чтобы в дальнейшем изменить свойства, связанные с операцией. |
| категория | Обязательно | Категория журнала для события. Категория — степень детализации, при которой можно включать или отключать журналы для определенного ресурса. Свойства, которые отображаются в свойствах BLOB-объекта события, одинаковы в пределах определенной категории журнала и типа ресурса. Типичными категориями журналов являются "Аудит", "Оперативный" "Исполнение" и "Запрос". |
| resultType | Необязательно | Состояние события. Обычные значения: "Запущен", "Выполняется", "Успешно", "Сбой", "Активно", "Разрешено". |
| resultSignature | Необязательно | Дополнительное состояние события. Если эта операция соответствует вызову REST API, то это код состояния HTTP соответствующего вызова REST. |
| resultDescription | Необязательно | Статическое текстовое описание этой операции, например "Получить файл хранения." |
| durationMs | Необязательно | Время выполнения операции в миллисекундах. |
| callerIpAddress | Необязательно | IP-адрес вызывающего объекта, если операция соответствует вызову API, который будет приходить с объекта с общедоступным IP-адресом. |
| correlationId | Необязательно | Идентификатор GUID, используемый для формирования набора связанных событий. Как правило, если два события имеют одно и то же значение параметра operationName, но имеют различные состояния (например, "Начатый" и "Успешный"), они имеют один и тот же идентификатор корреляции. Идентификатор может также представлять другие связи между событиями. |
| identity | Необязательно | BLOB-объект типа JSON, описывающий идентификацию пользователя или приложения, выполнившего операцию. Обычно он включает авторизацию и утверждения или токен JWT из Active Directory. |
| Level | Необязательно | Уровень серьезности события. Должен быть одним из следующих значений: "Информационное", "Предупреждение", "Ошибка" или "Критическое". |
| location | Необязательно | Регион ресурса, создавшего событие, например "Восток США" или "Франция на юге" |
| properties | Необязательно | Любые расширенные свойства, связанные с этой конкретной категорией событий. Все пользовательские/уникальные свойства должны быть помещены в эту "Часть B" схемы. |

## <a name="service-specific-schemas-for-resource-logs"></a>Схемы, специфичные для журналов ресурсов
Схема для журналов диагностики ресурсов зависит от типа ресурса и категории журнала. В этом списке показаны все службы, которые предоставляют журналы ресурсов и ссылки на службу и схему, относящуюся к категории, где это возможно.

| Служба | Схемы и документы |
| --- | --- |
| Azure Active Directory | [Обзор](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Схема журнала аудита](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) и [схема входа](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Службы Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Управление API | [Логи ресурсов управления API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Шлюзы приложений |[Регистрация для шлюза приложений](../../application-gateway/application-gateway-diagnostics.md) |
| Служба автоматизации Azure |[Аналитика журналов для автоматизации Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Пакетная служба Azure |[Регистрация пакетов Azure](../../batch/batch-diagnostics.md) |
| База данных Azure для MySQL | [Журналы сервера в базе данных Azure для MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| База данных Azure для PostgreSQL | [База данных Azure для журналов PostgreS'L](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Журналы исследователя данных Azure](/azure/data-explorer/using-diagnostic-logs) |
| Cognitive Services | [Регистрация для когнитивных служб Azure](../../cognitive-services/diagnostic-logging.md) |
| Реестр контейнеров | [Регистрация для реестра контейнеров Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Сеть доставки содержимого | [Лазурные журналы для CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Журнал ведения диагностики Azure Cosmos DB](../../cosmos-db/logging.md) |
| Фабрика данных | [Мониторинг фабрик данных с помощью Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Доступ к журналам для аналитики озер данных Azure](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Storage |[Доступ к журналам для магазина Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Центры событий |[Журналы концентраторов Azure Event](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Схема недоступна. |
| Брандмауэр Azure | Схема недоступна. |
| Центр Интернета вещей | [Использование Azure Monitor](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Лазурный ключ Vault Регистрация](../../key-vault/key-vault-logging.md) |
| Служба Kubernetes |[Лазурный Кuberнетлес Лесозаготовки](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Служба анализа журналов для балансировщика нагрузки Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Настраиваемая схема отслеживания Logic Apps B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| группы сетевой безопасности; |[Аналитика журналов для групп безопасности сети](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Защита от атак DDoS | [Управление службой "Защита от атак DDoS Azure" уровня "Стандартный"](../../virtual-network/manage-ddos-protection.md) |
| Power BI (цен. категория "Выделенный") | [Регистрация для Power BI, встроенная в Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Службы восстановления | [Модель данных для резервного копирования Azure](../../backup/backup-azure-reports-data-model.md)|
| Поиск |[Включение и использование аналитики поискового трафика](../../search/search-traffic-analytics.md) |
| Служебная шина |[Журналы автобусов Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| База данных SQL | [Регистрация базы данных Azure S'L](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Рабочие журналы](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Диспетчер трафика | [Схема журнала менеджера трафика](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Виртуальные сети | Схема недоступна. |
| Шлюзы виртуальной сети | Схема недоступна. |

## <a name="supported-log-categories-per-resource-type"></a>Поддерживаемые категории журнала для каждого типа ресурса

Некоторые категории могут поддерживаться только для определенных типов ресурсов. Это список всех, которые доступны в той или иной форме.  Например, категории Microsoft.Sql/серверов/баз данных доступны не для всех типов баз данных. Для получения дополнительной информации, [см.](../../sql-database/sql-database-metrics-diag-logging.md) 

|Тип ресурса|Категория|Отображаемое имя категории|
|---|---|---|
|Microsoft.AAD/domainServices|СистемаБезопасность|СистемаБезопасность|
|Microsoft.AAD/domainServices|AccountManagement|AccountManagement|
|Microsoft.AAD/domainServices|ЛогонЛогофф|ЛогонЛогофф|
|Microsoft.AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft.AAD/domainServices|ПолитикаИзменение|ПолитикаИзменение|
|Microsoft.AAD/domainServices|ПривилегияИспользование|ПривилегияИспользование|
|Microsoft.AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft.AAD/domainServices|КаталогServiceСервисДоступ|КаталогServiceСервисДоступ|
|Microsoft.AAD/domainServices|Учетная записьЛогон|Учетная записьЛогон|
|microsoft.aadiam/tenants|Signin|Signin|
|Microsoft.AnalysisServices/servers|Подсистема|Подсистема|
|Microsoft.AnalysisServices/servers|Служба|Служба|
|Microsoft.ApiManagement/service|GatewayLogs|Журналы, относящихся к шлюзу ApiManagement.|
|Microsoft.AppPlatform/Весна|ПриложениеКонсоль|Консоль приложений|
|Microsoft.Automation/automationAccounts|JobLogs|Журналы заданий|
|Microsoft.Automation/automationAccounts|JobStreams|Потоки заданий|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Состояние узла DSC.|
|Microsoft.Batch/batchAccounts|ServiceLog|Журналы служб|
|Microsoft.BatchAI/рабочие области|BaiClusterEvent|BaiClusterEvent|
|Microsoft.BatchAI/рабочие области|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft.BatchAI/рабочие области|БайДжоджоусвон|БайДжоджоусвон|
|Microsoft.Blockchain/blockchainMembers|БлокчейнПриложение|Блокчейн-приложение|
|Microsoft.Blockchain/blockchainMembers|Прокси-сервер|Прокси-сервер|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Возвращает метрики конечной точки, например пропускную способность, исходящий трафик и т. д.|
|Microsoft.ClassicNetwork/networksecuritygroups|Событие потока правил группы безопасности сети|Событие потока правил группы безопасности сети|
|Microsoft.CognitiveServices/accounts|Аудит|Журналы аудита|
|Microsoft.CognitiveServices/accounts|RequestResponse|Журналы запросов и ответов|
|Microsoft.ContainerRegistry/registries|КонтейнерНаяреестрReпоторияСобытия|РепозиторийEvent журналы (Предварительный просмотр)|
|Microsoft.ContainerRegistry/registries|КонтейнерРеестрЛогиНСобытия|События входа (Предварительный просмотр)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Сервер API Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Диспетчер контроллеров Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Планировщик Kubernetes|
|Microsoft.ContainerService/managedClusters|кубе-аудит|Кубернетес Аудит|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Средство автомасштабирования кластера Kubernetes|
|Microsoft.Databricks/рабочие области|dbfs|Файловая система Databricks|
|Microsoft.Databricks/рабочие области|clusters|Кластеры Databricks|
|Microsoft.Databricks/рабочие области|accounts|Счета Databricks|
|Microsoft.Databricks/рабочие области|jobs|Рабочие места в Датабриксе|
|Microsoft.Databricks/рабочие области|записная книжка|Записная книжка Databricks|
|Microsoft.Databricks/рабочие области|ssh|Databricks SSH|
|Microsoft.Databricks/рабочие области|Рабочая область|Рабочее пространство Databricks|
|Microsoft.Databricks/рабочие области|секретные коды|Секреты Databricks|
|Microsoft.Databricks/рабочие области|квлПозволены|Databricks S'LPermissions|
|Microsoft.Databricks/рабочие области|instancePools|Бассейны инстанций|
|Microsoft.DataCatalog/Datacatalogs|ScanStatusLogEvent|Сканирование|
|Microsoft.DataFactory/factories;|ActivityRuns|Журнал выполнения действий конвейера|
|Microsoft.DataFactory/factories;|PipelineRuns|Журнал запусков конвейера|
|Microsoft.DataFactory/factories;|TriggerRuns|Журнал запусков триггера|
|Microsoft.DataLakeAnalytics/accounts|Аудит|Журналы аудита|
|Microsoft.DataLakeAnalytics/accounts|Requests|Журналы запросов|
|Microsoft.DataLakeStore/accounts|Аудит|Журналы аудита|
|Microsoft.DataLakeStore/accounts|Requests|Журналы запросов|
|Microsoft.DataShare/учетные записи|Общие папки|Общие папки|
|Microsoft.DataShare/учетные записи|Доля Подписки|Поделиться подписками|
|Microsoft.DataShare/учетные записи|SentShareСнимки|Отправленные снимки общего обмена|
|Microsoft.DataShare/учетные записи|ReceivedShareSnapshots|Полученные снимки общего обмена|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Журналы сервера MySQL|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|Аудиторские журналы MyS'L|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Журналы сервера PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Статистика выгона магазина запросов PostgreS'L|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|PostgreS'L Статистика ожидания магазина запросов|
|Microsoft.DBforPostgreS'L/serversv2|PostgreSQLLogs|Журналы сервера PostgreSQL|
|Microsoft.DBforPostgreS'L/serversv2|QueryStoreRuntimeStatistics|Статистика выгона магазина запросов PostgreS'L|
|Microsoft.DBforPostgreS'L/serversv2|QueryStoreWaitStatistics|PostgreS'L Статистика ожидания магазина запросов|
|Microsoft.DesktopVirtualization/рабочие пространства|Контрольная точка|Контрольная точка|
|Microsoft.DesktopVirtualization/рабочие пространства|Error|Error|
|Microsoft.DesktopVirtualization/рабочие пространства|Управление|Управление|
|Microsoft.DesktopVirtualization/рабочие пространства|Веб-канал|Веб-канал|
|Microsoft.DesktopVirtualization/applicationGroups|Контрольная точка|Контрольная точка|
|Microsoft.DesktopVirtualization/applicationGroups|Error|Error|
|Microsoft.DesktopVirtualization/applicationGroups|Управление|Управление|
|Microsoft.DesktopVirtualization/hostPools|Контрольная точка|Контрольная точка|
|Microsoft.DesktopVirtualization/hostPools|Error|Error|
|Microsoft.DesktopVirtualization/hostPools|Управление|Управление|
|Microsoft.DesktopVirtualization/hostPools|Соединение|Соединение|
|Microsoft.DesktopVirtualization/hostPools|ХостРегистрация|ХостРегистрация|
|Microsoft.Devices/IotHubs|Соединения|Соединения|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Телеметрия устройства|
|Microsoft.Devices/IotHubs|C2DCommands|Команды, отправляемые из облака на устройство|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Операции с удостоверениями устройства|
|Microsoft.Devices/IotHubs|FileUploadOperations|Операции отправки файлов|
|Microsoft.Devices/IotHubs|Маршруты|Маршруты|
|Microsoft.Devices/IotHubs|Операции переноса с двойника устройства в облако|Операции переноса с двойника устройства в облако|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Операции переноса из облака на двойник устройства|
|Microsoft.Devices/IotHubs|TwinQueries|Запросы к двойникам|
|Microsoft.Devices/IotHubs|JobsOperations|Операции заданий|
|Microsoft.Devices/IotHubs|DirectMethods|Прямые методы|
|Microsoft.Devices/IotHubs|DistributedTracing|Распределенная трассировка (предварительная версия)|
|Microsoft.Devices/IotHubs|Конфигурации|Конфигурации|
|Microsoft.Devices/IotHubs|Потоки устройств|Потоки устройств (Предварительный просмотр)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Операции с устройствами|
|Microsoft.Devices/provisioningServices|ServiceOperations|Операции служб|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|РазделКистатистика|РазделКистатистика|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft.EnterpriseKnowledgeGraph/услуги|AuditEvent|Журнал AuditEvent|
|Microsoft.EnterpriseKnowledgeGraph/услуги|ДанныеВыпуск|Журнал DataIssue|
|Microsoft.EnterpriseKnowledgeGraph/услуги|Requests|Регистрация конфигурации|
|Microsoft.EventHub/namespaces|ArchiveLogs|Журналы архивации|
|Microsoft.EventHub/namespaces|OperationalLogs|Журналы операций|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Журналы автомасштабирования.|
|Microsoft.EventHub/namespaces|КафкаКоординаторЛоги|Кафка Координатор журналы|
|Microsoft.EventHub/namespaces|KafkaUserОшибкаЛогы|Входы в ошибки пользователей Kafka|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|VNet/IP Фильтрация Подключение журналы|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|Управляемые клиентами ключевые журналы|
|Microsoft.HealthcareApis/услуги|AuditLogs|Журналы аудита|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Оценки автомасштабирования|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Действия автоматического увеличения масштаба|
|Microsoft.IoTSpaces/Graph|Трассировка|Трассировка|
|Microsoft.IoTSpaces/Graph|Операционный|Операционный|
|Microsoft.IoTSpaces/Graph|Аудит|Аудит|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Входящий трафик|Входящий трафик|
|Microsoft.IoTSpaces/Graph|Исходящие|Исходящие|
|Microsoft.KeyVault/vaults|AuditEvent|Журналы аудита|
|Microsoft.Kusto/Clusters|Успешное|Успешные операции по глотоку|
|Microsoft.Kusto/Clusters|Неудачное|Неудачные операции по глотоку|
|Microsoft.Logic/workflows|WorkflowRuntime|События диагностики среды выполнения рабочего процесса|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integration Account track events|
|Microsoft.MachineLearningServices/workspaces|AmlComputeКластерСобытие|AmlComputeКластерСобытие|
|Microsoft.MachineLearningServices/workspaces|AmlComputeКластерНодеВент|AmlComputeКластерНодеВент|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/mediaservices|KeyDeliveryRequests|Запросы на доставку ключей|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Событие группы безопасности сети|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Счетчик правил группы безопасности сети|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Событие потока правил группы безопасности сети|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Уведомления о защите от атак DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Журналы потоков принятия решений по защите от атак DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Отчеты о защите от атак DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Оповещения о защите виртуальной машины|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Журнал доступа к шлюзу приложений|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Журнал производительности шлюза приложений|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Журнал брандмауэра шлюза приложений|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Правило приложения службы "Брандмауэр Azure"|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Правило сети службы "Брандмауэр Azure"|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Журналы диагностики туннелей|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Журналы диагностики маршрутов|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Журналы диагностики IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Журналы диагностики P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Событие вывода результатов проверки работоспособности диспетчера трафика|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Журналы таблиц пиринга маршрутов|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|Журналы диагностики туннелей|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog|Журналы диагностики маршрутов|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|Журналы диагностики IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Журнал доступа Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Журнал брандмауэра веб-приложения Frontdoor|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|Журналы диагностики IKE|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|Журналы диагностики P2S|
|Microsoft.Network/бастионХоны|БастионАудитЫ|Брекционные журналы бастиона|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|События оповещения балансировщика нагрузки|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Состояние работоспособности балансировщика нагрузки|
|Microsoft.PowerBIDedicated/capacities|Подсистема|Подсистема|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Данные отчетов службы архивации Azure|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Данные резервного копирования Core Azure|
|Microsoft.RecoveryServices/Vaults|АддоназурЕБэкапДжобс|Данные о работе резервного копирования Addon Azure|
|Microsoft.RecoveryServices/Vaults|АддоназуреРепаупОконс|Данные резервного оповещения Addon Azure|
|Microsoft.RecoveryServices/Vaults|АддоназурЕБэкПолитики|Данные политики резервного копирования Addon Azure|
|Microsoft.RecoveryServices/Vaults|АддоназуреРеАусХранение|Данные резервного копирования Аддона Azure|
|Microsoft.RecoveryServices/Vaults|АддоназурЕБэкапЗащищенныйInstance|Защищенные данные резервного копирования Addon Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Задания Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|События Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Реплицированные элементы Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Статистика репликации Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Точки восстановления Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Периодичность отправки данных репликации Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Обновление данных защищенного диска Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Журналы операций|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Журналы операций|
|Microsoft.Sql/servers/databases|SQLInsights|Аналитика SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Автоматическая настройка|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Статистика среды выполнения хранилища запросов|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Статистика времени ожидания хранилища запросов|
|Microsoft.Sql/servers/databases|ошибки|ошибки|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Статистика времени ожидания базы данных|
|Microsoft.Sql/servers/databases|Время ожидания|Время ожидания|
|Microsoft.Sql/servers/databases|Блоки|Блоки|
|Microsoft.Sql/servers/databases|Взаимоблокировки|Взаимоблокировки|
|Microsoft.Sql/servers/databases|Аудит|Журналы аудита|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Событие аудита безопасности SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Рабочие роли DMS|
|Microsoft.Sql/servers/databases|ExecRequests|Выполняющиеся запросы|
|Microsoft.Sql/servers/databases|RequestSteps|Действия, из которых состоит запрос|
|Microsoft.Sql/servers/databases|SqlRequests|Запросы SQL|
|Microsoft.Sql/servers/databases|Ожидания|Ожидания|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Статистика использования ресурсов|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Событие аудита безопасности SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|Аналитика SQL|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Статистика среды выполнения хранилища запросов|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Статистика времени ожидания хранилища запросов|
|Microsoft.Sql/managedInstances/databases|ошибки|ошибки|
|Microsoft.Storage/storageAccounts/tableServices|ХранениеЧитатьЧитатьсячитать|ХранениеЧитатьЧитатьсячитать|
|Microsoft.Storage/storageAccounts/tableServices|ХранениеWrite|ХранениеWrite|
|Microsoft.Storage/storageAccounts/tableServices|ХранениеУдалить|ХранениеУдалить|
|Microsoft.Storage/storageAccounts/blobServices|ХранениеЧитатьЧитатьсячитать|ХранениеЧитатьЧитатьсячитать|
|Microsoft.Storage/storageAccounts/blobServices|ХранениеWrite|ХранениеWrite|
|Microsoft.Storage/storageAccounts/blobServices|ХранениеУдалить|ХранениеУдалить|
|Microsoft.Storage/storageAccounts/fileServices|ХранениеЧитатьЧитатьсячитать|ХранениеЧитатьЧитатьсячитать|
|Microsoft.Storage/storageAccounts/fileServices|ХранениеWrite|ХранениеWrite|
|Microsoft.Storage/storageAccounts/fileServices|ХранениеУдалить|ХранениеУдалить|
|Microsoft.Storage/storageAccounts/queueServices|ХранениеЧитатьЧитатьсячитать|ХранениеЧитатьЧитатьсячитать|
|Microsoft.Storage/storageAccounts/queueServices|ХранениеWrite|ХранениеWrite|
|Microsoft.Storage/storageAccounts/queueServices|ХранениеУдалить|ХранениеУдалить|
|Microsoft.StreamAnalytics/streamingjobs|Выполнение|Выполнение|
|Microsoft.StreamAnalytics/streamingjobs|Разработка|Разработка|
|microsoft.web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Платформы платформы для окружающей среды Службы App Service|
|microsoft.web/sites|ФункцииAppLogs|Логи функциональных приложений|
|microsoft.web/sites|AppServiceHTTPLogs|Http журналы|
|microsoft.web/sites|AppServiceConsoleLogs|Консольные журналы службы приложений|
|microsoft.web/sites|AppServiceAppLogs|Журналы приложений приложений|
|microsoft.web/sites|AppServiceFileAuditLogLogLogs|Ссылки на содержание сайта|
|microsoft.web/sites|AppServiceAuditLogLogs|Регистрации аудита доступа|
|microsoft.web/sites/slots|ФункцииAppLogs|Логи функциональных приложений|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Http журналы|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Консольные журналы|
|microsoft.web/sites/slots|AppServiceAppLogs|Журналы приложений|
|microsoft.web/sites/slots|AppServiceFileAuditLogLogLogs|Ссылки на содержание сайта|
|microsoft.web/sites/slots|AppServiceAuditLogLogs|Регистрации аудита доступа|

## <a name="next-steps"></a>Next Steps

* [Подробнее о журналах ресурсов](../../azure-monitor/platform/platform-logs-overview.md)
* [Поток журналов ресурсов ресурсов в **концентраторы событий**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Изменение параметров диагностики журналов ресурсов с помощью API Rest Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Сбор журналов и метрик для служб Azure для использования в Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
