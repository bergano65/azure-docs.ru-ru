---
title: Поддерживаемые службы и схемы в журналах ресурсов Azure
description: Узнайте о поддерживаемых схемах служб и событий для журналов диагностики Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
author: rboucher
ms.author: robb
ms.openlocfilehash: 22521a3619482361c8f556b05436bb3b78c7dc9b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932339"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Поддерживаемые службы, схемы и категории для журналов ресурсов Azure

> [!NOTE]
> Журналы ресурсов ранее назывались журналами диагностики.

[Azure Monitor журналы ресурсов](../../azure-monitor/platform/resource-logs-overview.md) — это журналы, создаваемые службами Azure, которые описывают работу этих служб или ресурсов. Все журналы ресурсов, доступные через Azure Monitor, используют общую схему верхнего уровня, обеспечивая гибкость для каждой службы, чтобы создавать уникальные свойства для собственных событий.

Сочетание типа ресурса (доступного в свойстве `resourceId`) и свойства `category` является уникальным идентификатором схемы. В этой статье описывается схема верхнего уровня для журналов ресурсов и ссылки на Schemata для каждой службы.

## <a name="top-level-resource-logs-schema"></a>Схема журналов ресурсов верхнего уровня

| Name | Обязательный/необязательный | Описание |
|---|---|---|
| time | Обязательно для заполнения | Метка времени события (UTC). |
| ResourceId | Обязательно для заполнения | Идентификатор ресурса, создавшего событие. Для служб клиента он имеет формат "/tenants/ИД_клиента/providers/имя_поставщика". |
| tenantId | Требуется для журналов клиента | Идентификатор клиента Active Directory клиента, к которому привязано это событие. Это свойство используется только для журналов уровня клиентов и не отображается в журналах уровня ресурсов. |
| operationName | Обязательно для заполнения | Имя операции, которую представляет это событие. Если событие представляет операцию RBAC, то это имя операции RBAC (например, Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Обычно моделируются в виде операции Resource Manager, даже если они фактически не являются задокументированными операциями Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Необязательно | Версия API, связанного с операцией, если операция operationName выполнялась с помощью API (например, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Если для этой операции не существует соответствующего API, то версия представляет версию этой операции, чтобы в дальнейшем изменить свойства, связанные с операцией. |
| category | Обязательно для заполнения | Категория журнала для события. Категория — степень детализации, при которой можно включать или отключать журналы для определенного ресурса. Свойства, которые отображаются в свойствах BLOB-объекта события, одинаковы в пределах определенной категории журнала и типа ресурса. Типичными категориями журнала являются "Аудит", "Операционный", "Выполнение" и "Запрос". |
| resultType | Необязательно | Состояние события. Обычные значения: "Запущен", "Выполняется", "Успешно", "Сбой", "Активно", "Разрешено". |
| resultSignature | Необязательно | Дополнительное состояние события. Если эта операция соответствует вызову REST API, то это код состояния HTTP соответствующего вызова REST. |
| resultDescription | Необязательно | Статическое текстовое описание этой операции, например "Получить файл хранилища". |
| durationMs | Необязательно | Время выполнения операции в миллисекундах. |
| callerIpAddress | Необязательно | IP-адрес вызывающего объекта, если операция соответствует вызову API, который будет приходить с объекта с общедоступным IP-адресом. |
| correlationId | Необязательно | Идентификатор GUID, используемый для формирования набора связанных событий. Как правило, если два события имеют одно и то же значение параметра operationName, но имеют различные состояния (например, "Запущен" и "Успешно"), они совместно используют один и тот же идентификатор корреляции. Идентификатор может также представлять другие связи между событиями. |
| удостоверение | Необязательно | BLOB-объект типа JSON, описывающий идентификацию пользователя или приложения, выполнившего операцию. Обычно он включает авторизацию и утверждения или токен JWT из Active Directory. |
| уровень | Необязательно | Уровень серьезности события. Должен быть одним из следующих значений: "Информационное", "Предупреждение", "Ошибка" или "Критическое". |
| location | Необязательно | Регион ресурса, создавшего событие, например "Восточная часть США" или "Южная Франция" |
| properties | Необязательно | Любые расширенные свойства, связанные с этой конкретной категорией событий. Все пользовательские или уникальные свойства должны быть размещены внутри этой "части B" схемы. |

## <a name="service-specific-schemas-for-resource-logs"></a>Зависящие от службы схемы для журналов ресурсов
Схема для журналов диагностики ресурсов зависит от типа ресурса и категории журнала. В этом списке перечислены все службы, которые делают доступными журналы ресурсов и ссылки на схему службы и конкретной категории, где это возможно.

| Служба | Схемы и документы |
| --- | --- |
| Azure Active Directory | [Общие сведения](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [схема журнала аудита](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) и [схема входа](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Службы Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Управление API | [Журналы ресурсов управления API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Шлюзы приложений |[Ведение журнала для шлюза приложений](../../application-gateway/application-gateway-diagnostics.md) |
| Автоматизация Azure |[Log Analytics для службы автоматизации Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Пакетная служба Azure |[Ведение журнала пакетной службы Azure](../../batch/batch-diagnostics.md) |
| База данных Azure для MySQL | [Журналы сервера в базе данных Azure для MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| База данных Azure для PostgreSQL | [Журналы базы данных Azure для PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Журналы обозреватель данных Azure](../../data-explorer/using-diagnostic-logs.md) |
| Когнитивные сервисы | [Ведение журнала для Cognitive Services Azure](../../cognitive-services/diagnostic-logging.md) |
| Сеть кэширующих серверов | [Журналы Azure для CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB; | [Журнал ведения диагностики Azure Cosmos DB](../../cosmos-db/logging.md) |
| Фабрика данных | [Мониторинг фабрик данных с помощью Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Аналитика озера данных |[Доступ к журналам для Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Storage |[Доступ к журналам для Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Концентраторы событий |[Журналы концентраторов событий Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Схема недоступна. |
| Брандмауэр Azure | Схема недоступна. |
| Центр Интернета вещей | [Использование Azure Monitor](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Ведение журнала хранилища ключей Azure](../../key-vault/key-vault-logging.md) |
| Служба Kubernetes |[Ведение журнала Kubernetes для Azure](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Log Analytics для Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| приложения логики; |[Настраиваемая схема отслеживания сообщений B2B для приложений логики](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Группы безопасности сети |[Аналитика журналов для групп безопасности сети](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Защита от атак DDoS | [Управление службой "Защита от атак DDoS Azure" уровня "Стандартный"](../../virtual-network/manage-ddos-protection.md) |
| Power BI (цен. категория "Выделенный") | [Ведение журнала для Power BI Embedded в Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Службы восстановления | [Модель данных для Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| служба поиска. |[Включение и использование аналитики поискового трафика](../../search/search-traffic-analytics.md) |
| Служебная шина Azure |[Журналы служебной шины Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| База данных SQL | [Ведение журнала базы данных SQL Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Журналы заданий](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Диспетчер трафика | Схема журнала диспетчера трафика |
| Виртуальные сети | Схема недоступна. |
| Шлюзы виртуальной сети | Схема недоступна. |

## <a name="supported-log-categories-per-resource-type"></a>Поддерживаемые категории журнала для каждого типа ресурса
|Тип ресурса|Категория|Отображаемое имя категории|
|---|---|---|
|Microsoft. AAD/domainServices|системсекурити|системсекурити|
|Microsoft. AAD/domainServices|Пространство|Пространство|
|Microsoft. AAD/domainServices|логонлогофф|логонлогофф|
|Microsoft. AAD/domainServices|обжектакцесс|обжектакцесс|
|Microsoft. AAD/domainServices|полицичанже|полицичанже|
|Microsoft. AAD/domainServices|привилежеусе|привилежеусе|
|Microsoft. AAD/domainServices|детаилтраккинг|детаилтраккинг|
|Microsoft. AAD/domainServices|директорисервицеакцесс|директорисервицеакцесс|
|Microsoft. AAD/domainServices|аккаунтлогон|аккаунтлогон|
|microsoft.aadiam/tenants|Signin|Signin|
|Microsoft.AnalysisServices/servers|Двигатель|Двигатель|
|Microsoft.AnalysisServices/servers|Служба|Служба|
|Microsoft.ApiManagement/service|GatewayLogs|Журналы, относящихся к шлюзу ApiManagement.|
|Microsoft. Аппплатформ/пружина|аппликатионконсоле|Консоль приложения|
|Microsoft.Automation/automationAccounts|JobLogs|Журналы заданий|
|Microsoft.Automation/automationAccounts|JobStreams|Потоки заданий|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Состояние узла DSC.|
|Microsoft.Batch/batchAccounts|ServiceLog|Журналы служб|
|Microsoft. BatchAI/рабочие области|баиклустеревент|баиклустеревент|
|Microsoft. BatchAI/рабочие области|баиклустернодивент|баиклустернодивент|
|Microsoft. BatchAI/рабочие области|баижобевент|баижобевент|
|Microsoft. Блокчейн/Блоккчаинмемберс|блоккчаинаппликатион|Приложение блокчейн|
|Microsoft. Блокчейн/Блоккчаинмемберс|Прокси-сервер|Прокси-сервер|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Возвращает метрики конечной точки, например пропускную способность, исходящий трафик и т. д.|
|Microsoft.ClassicNetwork/networksecuritygroups|Событие потока правил группы безопасности сети|Событие потока правил группы безопасности сети|
|Microsoft.CognitiveServices/accounts|Аудит|Журналы аудита|
|Microsoft.CognitiveServices/accounts|RequestResponse|Журналы запросов и ответов|
|Microsoft.ContainerRegistry/registries|контаинеррегистрирепоситоревентс|Журналы Репоситоревент|
|Microsoft.ContainerRegistry/registries|контаинеррегистрилогиневентс|События входа|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Сервер API Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Диспетчер контроллеров Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Планировщик Kubernetes|
|Microsoft.ContainerService/managedClusters|KUBE — аудит|Аудит Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Средство автомасштабирования кластера Kubernetes|
|Microsoft. кирпичы и рабочие области|dBFS|Файловая система Databricks|
|Microsoft. кирпичы и рабочие области|clusters|Кластеры кирпичей|
|Microsoft. кирпичы и рабочие области|accounts|Учетные записи кирпичей|
|Microsoft. кирпичы и рабочие области|jobs|Задания Databricks|
|Microsoft. кирпичы и рабочие области|занятий|Записная книжка Databricks|
|Microsoft. кирпичы и рабочие области|ssh|SSH-сеансы|
|Microsoft. кирпичы и рабочие области|Рабочая область|Рабочая область Databricks|
|Microsoft. кирпичы и рабочие области|секретные коды|Секретные данные кирпичей|
|Microsoft. кирпичы и рабочие области|склпермиссионс|Склпермиссионсы блоков|
|Microsoft. кирпичы и рабочие области|инстанцепулс|Пулы экземпляров|
|Каталог Microsoft.|сканстатусложевент|сканстатус|
|Microsoft.DataFactory/factories;|ActivityRuns|Журнал выполнения действий конвейера|
|Microsoft.DataFactory/factories;|PipelineRuns|Журнал запусков конвейера|
|Microsoft.DataFactory/factories;|TriggerRuns|Журнал запусков триггера|
|Microsoft.DataLakeAnalytics/accounts|Аудит|Журналы аудита|
|Microsoft.DataLakeAnalytics/accounts|Requests (Запросы)|Журналы запросов|
|Microsoft.DataLakeStore/accounts|Аудит|Журналы аудита|
|Microsoft.DataLakeStore/accounts|Requests (Запросы)|Журналы запросов|
|Microsoft. файл или учетные записи|Общие папки|Общие папки|
|Microsoft. файл или учетные записи|шаресубскриптионс|Предоставление общего доступа к подпискам|
|Microsoft. файл или учетные записи|сентшареснапшотс|Отправленные моментальные снимки общих ресурсов|
|Microsoft. файл или учетные записи|рецеиведшареснапшотс|Получено моментальных снимков общих ресурсов|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Журналы сервера MySQL|
|Microsoft.DBforMySQL/servers|мисклаудитлогс|Журналы аудита MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Журналы сервера PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Статистика времени выполнения хранилища запросов PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Статистика ожидания хранилища запросов PostgreSQL|
|Microsoft. Дбфорпостгрескл/serversv2|PostgreSQLLogs|Журналы сервера PostgreSQL|
|Microsoft. Дбфорпостгрескл/serversv2|QueryStoreRuntimeStatistics|Статистика времени выполнения хранилища запросов PostgreSQL|
|Microsoft. Дбфорпостгрескл/serversv2|QueryStoreWaitStatistics|Статистика ожидания хранилища запросов PostgreSQL|
|Microsoft. Десктопвиртуализатион/рабочие области|Checkpoint|Checkpoint|
|Microsoft. Десктопвиртуализатион/рабочие области|Ошибка|Ошибка|
|Microsoft. Десктопвиртуализатион/рабочие области|Управление|Управление|
|Microsoft. Десктопвиртуализатион/рабочие области|Веб-канал|Веб-канал|
|Microsoft. Десктопвиртуализатион/Аппликатионграупс|Checkpoint|Checkpoint|
|Microsoft. Десктопвиртуализатион/Аппликатионграупс|Ошибка|Ошибка|
|Microsoft. Десктопвиртуализатион/Аппликатионграупс|Управление|Управление|
|Microsoft. Десктопвиртуализатион/Хостпулс|Checkpoint|Checkpoint|
|Microsoft. Десктопвиртуализатион/Хостпулс|Ошибка|Ошибка|
|Microsoft. Десктопвиртуализатион/Хостпулс|Управление|Управление|
|Microsoft. Десктопвиртуализатион/Хостпулс|Подключение|Подключение|
|Microsoft. Десктопвиртуализатион/Хостпулс|хострегистратион|хострегистратион|
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
|Microsoft.Devices/IotHubs|DirectMethods|прямые методы.|
|Microsoft.Devices/IotHubs|DistributedTracing|Распределенная трассировка (предварительная версия)|
|Microsoft.Devices/IotHubs|Конфигурации|Конфигурации|
|Microsoft.Devices/IotHubs|девицестреамс|Потоки устройств (Предварительная версия)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Операции с устройствами|
|Microsoft.Devices/provisioningServices|ServiceOperations|Операции со службой|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|партитионкэйстатистикс|партитионкэйстатистикс|
|Microsoft.DocumentDB/databaseAccounts|контролпланерекуестс|контролпланерекуестс|
|Microsoft. Ентерприсекновледжеграф/Services|AuditEvent|Журнал AuditEvent|
|Microsoft. Ентерприсекновледжеграф/Services|Ошибка в процессе|Журнал проблем|
|Microsoft. Ентерприсекновледжеграф/Services|Requests (Запросы)|Журнал конфигурации|
|Microsoft.EventHub/namespaces|ArchiveLogs|Журналы архивации|
|Microsoft.EventHub/namespaces|OperationalLogs|Журналы операций|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Журналы автомасштабирования.|
|Microsoft.EventHub/namespaces|кафкакурдинаторлогс|Журналы координатора Kafka|
|Microsoft.EventHub/namespaces|кафкаусереррорлогс|Журналы ошибок пользователя Kafka|
|Microsoft.EventHub/namespaces|евенсубвнетконнектионевент|Журналы подключений для фильтрации виртуальной сети и IP-адресов|
|Microsoft.EventHub/namespaces|кустомерманажедкэйусерлогс|Журналы управляемых ключей клиентов|
|Microsoft. Хеалскареапис/Services|AuditLogs|Журналы аудита|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Оценки автомасштабирования|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Действия автоматического увеличения масштаба|
|Microsoft.IoTSpaces/Graph|Трассировка|Трассировка|
|Microsoft.IoTSpaces/Graph|Рабочий режим|Рабочий режим|
|Microsoft.IoTSpaces/Graph|Аудит|Аудит|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Входящий трафик|Входящий трафик|
|Microsoft.IoTSpaces/Graph|Исходящие|Исходящие|
|Microsoft.KeyVault/vaults|AuditEvent|Журналы аудита|
|Microsoft.Kusto/Clusters|сукцеедединжестион|Успешные операции приема|
|Microsoft.Kusto/Clusters|фаилединжестион|Неудачные операции приема|
|Microsoft.Logic/workflows|WorkflowRuntime|События диагностики среды выполнения рабочего процесса|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integration Account track events|
|Microsoft.MachineLearningServices/workspaces|амлкомпутеклустеревент|амлкомпутеклустеревент|
|Microsoft.MachineLearningServices/workspaces|амлкомпутеклустернодивент|амлкомпутеклустернодивент|
|Microsoft.MachineLearningServices/workspaces|амлкомпутежобевент|амлкомпутежобевент|
|Microsoft. Media/mediaservices|кэйделиверирекуестс|Запросы на доставку ключей|
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
|Microsoft.Network/securegateways|AzureFirewallApplicationRule|Правило приложения службы "Брандмауэр Azure"|
|Microsoft.Network/securegateways|AzureFirewallNetworkRule|Правило сети службы "Брандмауэр Azure"|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Правило приложения службы "Брандмауэр Azure"|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Правило сети службы "Брандмауэр Azure"|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Журналы диагностики туннелей|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Журналы диагностики маршрутов|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Журналы диагностики IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Журналы диагностики P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Событие вывода результатов проверки работоспособности диспетчера трафика|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Журналы таблиц пиринга маршрутов|
|Microsoft. Network/Впнгатевайс|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|Microsoft. Network/Впнгатевайс|TunnelDiagnosticLog|Журналы диагностики туннелей|
|Microsoft. Network/Впнгатевайс|RouteDiagnosticLog|Журналы диагностики маршрутов|
|Microsoft. Network/Впнгатевайс|IKEDiagnosticLog|Журналы диагностики IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Журнал доступа Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Журнал брандмауэра веб-приложения Frontdoor|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|Журналы диагностики IKE|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|Журналы диагностики P2S|
|Microsoft. Network/Бастионхостс|бастионаудитлогс|Журналы аудита бастиона|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|События оповещения балансировщика нагрузки|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Состояние работоспособности балансировщика нагрузки|
|Microsoft.PowerBIDedicated/capacities|Двигатель|Двигатель|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Данные отчетов службы архивации Azure|
|Microsoft.RecoveryServices/Vaults|кореазуребаккуп|Основные Azure Backup данные|
|Microsoft.RecoveryServices/Vaults|аддоназуребаккупжобс|Azure Backup данных задания надстройки|
|Microsoft.RecoveryServices/Vaults|аддоназуребаккупалертс|Надстройка Azure Backup данные оповещений|
|Microsoft.RecoveryServices/Vaults|аддоназуребаккупполици|Данные политики Azure Backup надстроек|
|Microsoft.RecoveryServices/Vaults|аддоназуребаккупстораже|Надстройка Azure Backup данные хранилища|
|Microsoft.RecoveryServices/Vaults|аддоназуребаккуппротектединстанце|Надстройка Azure Backup данные защищенного экземпляра|
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
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Статистика времени ожидания базы данных|
|Microsoft.Sql/servers/databases|Время ожидания|Время ожидания|
|Microsoft.Sql/servers/databases|Blocks|Blocks|
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
|Microsoft.Sql/managedInstances/databases|Errors|Errors|
|Microsoft.Storage/storageAccounts/tableServices|сторажереад|сторажереад|
|Microsoft.Storage/storageAccounts/tableServices|сторажеврите|сторажеврите|
|Microsoft.Storage/storageAccounts/tableServices|сторажеделете|сторажеделете|
|Microsoft.Storage/storageAccounts/blobServices|сторажереад|сторажереад|
|Microsoft.Storage/storageAccounts/blobServices|сторажеврите|сторажеврите|
|Microsoft.Storage/storageAccounts/blobServices|сторажеделете|сторажеделете|
|Microsoft.Storage/storageAccounts/fileServices|сторажереад|сторажереад|
|Microsoft.Storage/storageAccounts/fileServices|сторажеврите|сторажеврите|
|Microsoft.Storage/storageAccounts/fileServices|сторажеделете|сторажеделете|
|Microsoft.Storage/storageAccounts/queueServices|сторажереад|сторажереад|
|Microsoft.Storage/storageAccounts/queueServices|сторажеврите|сторажеврите|
|Microsoft.Storage/storageAccounts/queueServices|сторажеделете|сторажеделете|
|Microsoft.StreamAnalytics/streamingjobs|Выполнение|Выполнение|
|Microsoft.StreamAnalytics/streamingjobs|Разработка|Разработка|
|Microsoft. Web/hostingenvironments|аппсервицеенвиронментплатформлогс|Журналы платформы Среда службы приложений|
|microsoft.web/sites|функтионапплогс|Журналы приложений функций|
|microsoft.web/sites|аппсервицехттплогс|Журналы HTTP|
|microsoft.web/sites|аппсервицеконсолелогс|Журналы консоли службы приложений|
|microsoft.web/sites|аппсервицеапплогс|Журналы приложения службы приложений|
|microsoft.web/sites|аппсервицефилеаудитлогс|Журналы аудита изменения содержимого сайта|
|microsoft.web/sites|аппсервицеаудитлогс|Доступ к журналам аудита|
|microsoft.web/sites/slots|функтионапплогс|Журналы приложений функций|
|microsoft.web/sites/slots|аппсервицехттплогс|Журналы HTTP|
|microsoft.web/sites/slots|аппсервицеконсолелогс|Журналы консоли|
|microsoft.web/sites/slots|аппсервицеапплогс|Журналы приложений|
|microsoft.web/sites/slots|аппсервицефилеаудитлогс|Журналы аудита изменения содержимого сайта|
|microsoft.web/sites/slots|аппсервицеаудитлогс|Доступ к журналам аудита|

## <a name="next-steps"></a>Следующие шаги

* [Дополнительные сведения о журналах ресурсов](../../azure-monitor/platform/resource-logs-overview.md)
* [Потоковая передача журналов ресурсов в **концентраторы событий**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Изменение параметров диагностики журнала ресурсов с помощью Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Сбор журналов и метрик для служб Azure для использования в Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
