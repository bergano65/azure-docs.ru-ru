---
title: Поддерживаемые службы и категории журналов Azure Monitor ресурсов
description: Справочник по Azure Monitor сведения о поддерживаемых службах и схеме событий для журналов ресурсов Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 81f79b81c03e7996d7f6d45b002d8160740c3c14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318306"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Поддерживаемые категории для журналов ресурсов Azure

> [!NOTE]
> Журналы ресурсов ранее назывались журналами диагностики. Имя было изменено в октябре 2019, так как типы журналов, собранные Azure Monitor перемещены, чтобы включать больше, чем просто ресурс Azure.

[Azure Monitor журналы ресурсов](./platform-logs-overview.md) — это журналы, создаваемые службами Azure, которые описывают работу этих служб или ресурсов. Все журналы ресурсов, доступные через Azure Monitor, используют общую схему верхнего уровня, обеспечивая гибкость для каждой службы, чтобы создавать уникальные свойства для собственных событий.

Сочетание типа ресурса (доступного в свойстве `resourceId`) и свойства `category` является уникальным идентификатором схемы. Существует общая схема для всех журналов ресурсов с полями для конкретных служб, которые затем добавляются для различных категорий журналов. Дополнительные сведения см. в разделе [Common and Service зависящая схема для журналов ресурсов Azure]() .

## <a name="supported-log-categories-per-resource-type"></a>Поддерживаемые категории журнала для каждого типа ресурса

Ниже приведен список типов журналов, доступных для каждого типа ресурсов. 

Некоторые категории могут поддерживаться только для определенных типов ресурсов. Если вы считаете, что ресурс отсутствует, см. документацию для конкретного ресурса. Например, категории Microsoft. SQL/Servers/databases недоступны для всех типов баз данных. Дополнительные сведения см. [в разделе сведения о журнале диагностики базы данных SQL](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Если все еще нет, можно открыть комментарий GitHub в нижней части этой статьи.

## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|Категория|Отображаемое имя категории|
|---|---|
|Signin|Signin|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Категория|Отображаемое имя категории|
|---|---|
|Подсистема|Подсистема|
|Служба|Служба|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Категория|Отображаемое имя категории|
|---|---|
|GatewayLogs|Журналы, относящихся к шлюзу ApiManagement.|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Категория|Отображаемое имя категории|
|---|---|
|аппликатионконсоле|Консоль приложения|
|системлогс|Системные журналы|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Категория|Отображаемое имя категории|
|---|---|
|JobLogs|Журналы заданий|
|JobStreams|Потоки заданий|
|DscNodeStatus|Состояние узла DSC.|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Категория|Отображаемое имя категории|
|---|---|
|ServiceLog|Журналы служб|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Категория|Отображаемое имя категории|
|---|---|
|баиклустеревент|баиклустеревент|
|баиклустернодивент|баиклустернодивент|
|баижобевент|баижобевент|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Категория|Отображаемое имя категории|
|---|---|
|блоккчаинаппликатион|Приложение блокчейн|
|Proxy (Прокси)|Proxy (Прокси)|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Блокчейн/Кордамемберс

|Категория|Отображаемое имя категории|
|---|---|
|блоккчаинаппликатион|Приложение блокчейн|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/кднвебаппликатионфиреваллполиЦиес

|Категория|Отображаемое имя категории|
|---|---|
|вебаппликатионфиревалллогс|Журналы брандмауэра веб-приложения|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Категория|Отображаемое имя категории|
|---|---|
|азурекднакцесслог|Журнал доступа Azure CDN|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|Категория|Отображаемое имя категории|
|---|---|
|CoreAnalytics|Возвращает метрики конечной точки, например пропускную способность, исходящий трафик и т. д.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|Категория|Отображаемое имя категории|
|---|---|
|Событие потока правил группы безопасности сети|Событие потока правил группы безопасности сети|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Категория|Отображаемое имя категории|
|---|---|
|Аудит|Журналы аудита|
|RequestResponse|Журналы запросов и ответов|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Категория|Отображаемое имя категории|
|---|---|
|контаинеррегистрилогиневентс|События входа|
|контаинеррегистрирепоситоревентс|Журналы Репоситоревент|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Категория|Отображаемое имя категории|
|---|---|
|cluster-autoscaler|Средство автомасштабирования кластера Kubernetes|
|kube-apiserver|Сервер API Kubernetes|
|KUBE — аудит|Аудит Kubernetes|
|kube-controller-manager|Диспетчер контроллеров Kubernetes|
|kube-scheduler|Планировщик Kubernetes|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. Кустомпровидерс/ресаурцепровидерс

|Категория|Отображаемое имя категории|
|---|---|
|AuditLogs|Журналы аудита для вызовов Минирп|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. кирпичы и рабочие области

|Категория|Отображаемое имя категории|
|---|---|
|accounts|Учетные записи кирпичей|
|clusters|Кластеры кирпичей|
|dBFS|Файловая система Databricks|
|инстанцепулс|Пулы экземпляров|
|jobs|Задания кирпичей|
|записная книжка|Записная книжка Databricks|
|секретные коды|Секретные данные кирпичей|
|склпермиссионс|Склпермиссионсы блоков|
|ssh|SSH-сеансы|
|Рабочая область|Рабочая область "кирпичи"|


## <a name="microsoftdatacatalogdatacatalogs"></a>Каталог Microsoft.

|Категория|Отображаемое имя категории|
|---|---|
|сканстатусложевент|сканстатус|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Категория|Отображаемое имя категории|
|---|---|
|ActivityRuns|Журнал выполнения действий конвейера|
|PipelineRuns|Журнал запусков конвейера|
|TriggerRuns|Журнал запусков триггера|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Категория|Отображаемое имя категории|
|---|---|
|Аудит|Журналы аудита|
|Requests|Журналы запросов|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Категория|Отображаемое имя категории|
|---|---|
|мисклаудитлогс|Журналы аудита MariaDB|
|MySqlSlowLogs|Журналы сервера MariaDB|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Категория|Отображаемое имя категории|
|---|---|
|мисклаудитлогс|Журналы аудита MySQL|
|MySqlSlowLogs|Журналы сервера MySQL|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Категория|Отображаемое имя категории|
|---|---|
|PostgreSQLLogs|Журналы сервера PostgreSQL|
|QueryStoreRuntimeStatistics|Статистика времени выполнения хранилища запросов PostgreSQL|
|QueryStoreWaitStatistics|Статистика ожидания хранилища запросов PostgreSQL|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Категория|Отображаемое имя категории|
|---|---|
|PostgreSQLLogs|Журналы сервера PostgreSQL|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. Дбфорпостгрескл/синглесерверс

|Категория|Отображаемое имя категории|
|---|---|
|PostgreSQLLogs|Журналы сервера PostgreSQL|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. Десктопвиртуализатион/аппликатионграупс

|Категория|Отображаемое имя категории|
|---|---|
|Контрольная точка|Контрольная точка|
|Error|Error|
|Управление|Управление|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. Десктопвиртуализатион/хостпулс

|Категория|Отображаемое имя категории|
|---|---|
|Контрольная точка|Контрольная точка|
|Подключение|Подключение|
|Error|Error|
|HostRegistration|HostRegistration|
|Управление|Управление|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. Десктопвиртуализатион/рабочие области

|Категория|Отображаемое имя категории|
|---|---|
|Контрольная точка|Контрольная точка|
|Error|Error|
|Веб-канал|Веб-канал|
|Управление|Управление|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Категория|Отображаемое имя категории|
|---|---|
|C2DCommands|Команды, отправляемые из облака на устройство|
|C2DTwinOperations|Операции переноса из облака на двойник устройства|
|Конфигурации|Конфигурации|
|Соединения|Соединения|
|Операции переноса с двойника устройства в облако|Операции переноса с двойника устройства в облако|
|DeviceIdentityOperations|Операции с удостоверениями устройства|
|девицестреамс|Потоки устройств (Предварительная версия)|
|DeviceTelemetry|Телеметрия устройства|
|DirectMethods|Прямые методы|
|DistributedTracing|Распределенная трассировка (предварительная версия)|
|FileUploadOperations|Операции отправки файлов|
|JobsOperations|Операции заданий|
|Маршруты|Маршруты|
|TwinQueries|Запросы к двойникам|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Категория|Отображаемое имя категории|
|---|---|
|DeviceOperations|Операции с устройствами|
|ServiceOperations|Операции служб|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Категория|Отображаемое имя категории|
|---|---|
|CassandraRequests|CassandraRequests|
|контролпланерекуестс|контролпланерекуестс|
|DataPlaneRequests|DataPlaneRequests|
|MongoRequests|MongoRequests|
|партитионкэйруконсумптион|партитионкэйруконсумптион|
|партитионкэйстатистикс|партитионкэйстатистикс|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Категория|Отображаемое имя категории|
|---|---|
|AuditEvent|Журнал AuditEvent|
|Ошибка в процессе|Журнал проблем|
|Requests|Журнал конфигурации|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Категория|Отображаемое имя категории|
|---|---|
|деливерифаилурес|Журналы сбоев доставки|
|публишфаилурес|Публикация журналов сбоев|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/Системтопикс

|Категория|Отображаемое имя категории|
|---|---|
|деливерифаилурес|Журналы сбоев доставки|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Категория|Отображаемое имя категории|
|---|---|
|деливерифаилурес|Журналы сбоев доставки|
|публишфаилурес|Публикация журналов сбоев|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Категория|Отображаемое имя категории|
|---|---|
|ArchiveLogs|Журналы архивации|
|AutoScaleLogs|Журналы автомасштабирования.|
|кустомерманажедкэйусерлогс|Customer-Managed журналов ключей|
|евенсубвнетконнектионевент|Журналы подключений для фильтрации виртуальной сети и IP-адресов|
|кафкакурдинаторлогс|Журналы координатора Kafka|
|кафкаусереррорлогс|Журналы ошибок пользователя Kafka|
|OperationalLogs|Журналы операций|


## <a name="microsofthealthcareapisservices"></a>Microsoft. Хеалскареапис/Services

|Категория|Отображаемое имя категории|
|---|---|
|AuditLogs|Журналы аудита|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Категория|Отображаемое имя категории|
|---|---|
|AutoscaleEvaluations|Оценки автомасштабирования|
|AutoscaleScaleActions|Действия автоматического увеличения масштаба|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Категория|Отображаемое имя категории|
|---|---|
|аппаваилабилитиресултс|Результаты доступности|
|аппбровсертимингс|Время браузера|
|аппдепенденЦиес|Зависимости|
|аппевентс|События|
|аппексцептионс|Исключения|
|аппметрикс|Метрики|
|апппажевиевс|Просмотры страниц|
|аппперформанцекаунтерс|Счетчики производительности|
|аппрекуестс|Requests|
|аппсистемевентс|Системные события|
|апптрацес|Трассировки|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|Категория|Отображаемое имя категории|
|---|---|
|Аудит|Аудит|
|Исходящие|Исходящие|
|Входящий трафик|Входящий трафик|
|Операционный|Операционный|
|Трассировка|Трассировка|
|UserDefinedFunction|UserDefinedFunction|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Категория|Отображаемое имя категории|
|---|---|
|AuditEvent|Журналы аудита|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Категория|Отображаемое имя категории|
|---|---|
|фаилединжестион|Неудачные операции приема|
|сукцеедединжестион|Успешные операции приема|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

|Категория|Отображаемое имя категории|
|---|---|
|IntegrationAccountTrackingEvents|Integration Account track events|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Категория|Отображаемое имя категории|
|---|---|
|WorkflowRuntime|События диагностики среды выполнения рабочего процесса|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Категория|Отображаемое имя категории|
|---|---|
|амлкомпутеклустеревент|амлкомпутеклустеревент|
|амлкомпутеклустернодивент|амлкомпутеклустернодивент|
|амлкомпутекпугпуутилизатион|амлкомпутекпугпуутилизатион|
|амлкомпутежобевент|амлкомпутежобевент|
|амлрунстатусчанжедевент|амлрунстатусчанжедевент|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Категория|Отображаемое имя категории|
|---|---|
|кэйделиверирекуестс|Запросы на доставку ключей|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Категория|Отображаемое имя категории|
|---|---|
|ApplicationGatewayAccessLog|Журнал доступа к шлюзу приложений|
|ApplicationGatewayFirewallLog|Журнал брандмауэра шлюза приложений|
|ApplicationGatewayPerformanceLog|Журнал производительности шлюза приложений|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Категория|Отображаемое имя категории|
|---|---|
|AzureFirewallApplicationRule|Правило приложения службы "Брандмауэр Azure"|
|AzureFirewallNetworkRule|Правило сети службы "Брандмауэр Azure"|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/Бастионхостс

|Категория|Отображаемое имя категории|
|---|---|
|бастионаудитлогс|Журналы аудита бастиона|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Категория|Отображаемое имя категории|
|---|---|
|PeeringRouteLog|Журналы таблиц пиринга маршрутов|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Категория|Отображаемое имя категории|
|---|---|
|FrontdoorAccessLog|Журнал доступа Frontdoor|
|FrontdoorWebApplicationFirewallLog|Журнал брандмауэра веб-приложения Frontdoor|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Категория|Отображаемое имя категории|
|---|---|
|LoadBalancerAlertEvent|События оповещения балансировщика нагрузки|
|LoadBalancerProbeHealthStatus|Состояние работоспособности балансировщика нагрузки|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Категория|Отображаемое имя категории|
|---|---|
|NetworkSecurityGroupEvent|Событие группы безопасности сети|
|NetworkSecurityGroupFlowEvent|Событие потока правил группы безопасности сети|
|NetworkSecurityGroupRuleCounter|Счетчик правил группы безопасности сети|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Категория|Отображаемое имя категории|
|---|---|
|DDoSMitigationFlowLogs|Журналы потоков принятия решений по защите от атак DDoS|
|DDoSMitigationReports|Отчеты о защите от атак DDoS|
|DDoSProtectionNotifications|Уведомления о защите от атак DDoS|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Категория|Отображаемое имя категории|
|---|---|
|ProbeHealthStatusEvents|Событие вывода результатов проверки работоспособности диспетчера трафика|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Категория|Отображаемое имя категории|
|---|---|
|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|IKEDiagnosticLog|Журналы диагностики IKE|
|P2SDiagnosticLog|Журналы диагностики P2S|
|RouteDiagnosticLog|Журналы диагностики маршрутов|
|TunnelDiagnosticLog|Журналы диагностики туннелей|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Категория|Отображаемое имя категории|
|---|---|
|VMProtectionAlerts|Оповещения о защите виртуальной машины|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Категория|Отображаемое имя категории|
|---|---|
|Подсистема|Подсистема|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Категория|Отображаемое имя категории|
|---|---|
|аддоназуребаккупалертс|Надстройка Azure Backup данные оповещений|
|аддоназуребаккупжобс|Azure Backup данных задания надстройки|
|аддоназуребаккупполици|Данные политики Azure Backup надстроек|
|аддоназуребаккуппротектединстанце|Надстройка Azure Backup данные защищенного экземпляра|
|аддоназуребаккупстораже|Надстройка Azure Backup данные хранилища|
|AzureBackupReport|Данные отчетов службы архивации Azure|
|AzureSiteRecoveryEvents|События Azure Site Recovery|
|AzureSiteRecoveryJobs|Задания Azure Site Recovery|
|AzureSiteRecoveryProtectedDiskDataChurn|Обновление данных защищенного диска Azure Site Recovery|
|AzureSiteRecoveryRecoveryPoints|Точки восстановления Azure Site Recovery|
|AzureSiteRecoveryReplicatedItems|Реплицированные элементы Azure Site Recovery|
|AzureSiteRecoveryReplicationDataUploadRate|Периодичность отправки данных репликации Azure Site Recovery|
|AzureSiteRecoveryReplicationStats|Статистика репликации Azure Site Recovery|
|кореазуребаккуп|Основные Azure Backup данные|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Категория|Отображаемое имя категории|
|---|---|
|хибридконнектионсевент|События Хибридконнектионс|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Категория|Отображаемое имя категории|
|---|---|
|OperationLogs|Журналы операций|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Категория|Отображаемое имя категории|
|---|---|
|OperationalLogs|Журналы операций|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Категория|Отображаемое имя категории|
|---|---|
|алллогс|Журналы службы SignalR Azure.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Категория|Отображаемое имя категории|
|---|---|
|девопсоператионсаудит|Журналы аудита операций Devops|
|ResourceUsageStats|Статистика использования ресурсов|
|SQLSecurityAuditEvents|Событие аудита безопасности SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Категория|Отображаемое имя категории|
|---|---|
|ошибки|ошибки|
|QueryStoreRuntimeStatistics|Статистика среды выполнения хранилища запросов|
|QueryStoreWaitStatistics|Статистика времени ожидания хранилища запросов|
|SQLInsights|Аналитика SQL|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Категория|Отображаемое имя категории|
|---|---|
|AutomaticTuning|Автоматическая настройка|
|Blocks|Blocks|
|DatabaseWaitStatistics|Статистика времени ожидания базы данных|
|Взаимоблокировки|Взаимоблокировки|
|девопсоператионсаудит|Журналы аудита операций Devops|
|DmsWorkers|Рабочие роли DMS|
|ошибки|ошибки|
|ExecRequests|Выполняющиеся запросы|
|QueryStoreRuntimeStatistics|Статистика среды выполнения хранилища запросов|
|QueryStoreWaitStatistics|Статистика времени ожидания хранилища запросов|
|RequestSteps|Действия, из которых состоит запрос|
|SQLInsights|Аналитика SQL|
|SqlRequests|Запросы SQL|
|SQLSecurityAuditEvents|Событие аудита безопасности SQL|
|Время ожидания|Время ожидания|
|Ожидания|Ожидания|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Категория|Отображаемое имя категории|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Категория|Отображаемое имя категории|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Категория|Отображаемое имя категории|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Категория|Отображаемое имя категории|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Категория|Отображаемое имя категории|
|---|---|
|Разработка|Разработка|
|Выполнение|Выполнение|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Категория|Отображаемое имя категории|
|---|---|
|аппсервицеенвиронментплатформлогс|Журналы платформы Среда службы приложений|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Категория|Отображаемое имя категории|
|---|---|
|аппсервицеапплогс|Журналы приложения службы приложений|
|аппсервицеаудитлогс|Доступ к журналам аудита|
|аппсервицеконсолелогс|Журналы консоли службы приложений|
|аппсервицефилеаудитлогс|Журналы аудита изменения содержимого сайта|
|аппсервицехттплогс|Журналы HTTP|
|функтионапплогс|Журналы приложений функций|
|сканлогс|Журналы антивирусной проверки|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots


|Категория|Отображаемое имя категории|
|---|---|
|аппсервицеапплогс|Журналы приложения службы приложений|
|аппсервицеаудитлогс|Доступ к журналам аудита|
|аппсервицеконсолелогс|Журналы консоли службы приложений|
|аппсервицефилеаудитлогс|Журналы аудита изменения содержимого сайта|
|аппсервицехттплогс|Журналы HTTP|
|функтионапплогс|Журналы приложений функций|
|сканлогс|Журналы антивирусной проверки|


## <a name="next-steps"></a>Следующие шаги

* [Дополнительные сведения о журналах ресурсов](./platform-logs-overview.md)
* [Потоковая передача журналов ресурсов в **концентраторы событий**](./resource-logs.md#send-to-azure-event-hubs)
* [Изменение параметров диагностики журнала ресурсов с помощью Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Сбор журналов и метрик для служб Azure для использования в Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

