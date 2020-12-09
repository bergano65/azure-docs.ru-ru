---
title: Поддерживаемые службы и категории журналов Azure Monitor ресурсов
description: Справочник по Azure Monitor сведения о поддерживаемых службах и схеме событий для журналов ресурсов Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: c7b2d48b40843930bba78f54d2294769d952daf6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931237"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Поддерживаемые категории для журналов ресурсов Azure

> [!NOTE]
> Журналы ресурсов ранее назывались журналами диагностики. Имя было изменено в октябре 2019, так как типы журналов, собранные Azure Monitor перемещены, чтобы включать больше, чем просто ресурс Azure.

[Azure Monitor журналы ресурсов](./platform-logs-overview.md) — это журналы, создаваемые службами Azure, которые описывают работу этих служб или ресурсов. Все журналы ресурсов, доступные через Azure Monitor, используют общую схему верхнего уровня, обеспечивая гибкость для каждой службы, чтобы создавать уникальные свойства для собственных событий.

Сочетание типа ресурса (доступного в свойстве `resourceId`) и свойства `category` является уникальным идентификатором схемы. Существует общая схема для всех журналов ресурсов с полями для конкретных служб, которые затем добавляются для различных категорий журналов. Дополнительные сведения см. в разделе [Common and Service зависящая схема для журналов ресурсов Azure]() .


## <a name="costs"></a>Затраты

Существуют затраты, связанные с отправкой и хранением данных в Log Analytics, службе хранилища Azure и (или) концентраторе событий. Вы можете платить за затраты на получение данных в эти места и на их хранение.  Журналы ресурсов — это один из типов данных, которые можно отправить в эти расположения. [Экспорт некоторых категорий журналов ресурсов](https://azure.microsoft.com/pricing/details/monitor/) в эти места может быть дополнительным, а другие — без затрат на экспорт. В таблице ниже перечислены особенности экспорта затрат.

## <a name="supported-log-categories-per-resource-type"></a>Поддерживаемые категории журнала для каждого типа ресурса

Ниже приведен список типов журналов, доступных для каждого типа ресурсов. 

Некоторые категории могут поддерживаться только для определенных типов ресурсов. Если вы считаете, что ресурс отсутствует, см. документацию для конкретного ресурса. Например, категории Microsoft. SQL/Servers/databases недоступны для всех типов баз данных. Дополнительные сведения см. [в разделе сведения о журнале диагностики базы данных SQL](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Если все еще нет, можно открыть комментарий GitHub в нижней части этой статьи.
## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Подсистема|Подсистема|
|Служба|Служба|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|GatewayLogs|Журналы, относящихся к шлюзу ApiManagement.|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|аппликатионконсоле|Консоль приложения|
|системлогс|Системные журналы|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|DscNodeStatus|Состояние узла DSC.|
|JobLogs|Журналы заданий|
|JobStreams|Потоки заданий|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|ServiceLog|Журналы служб|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|баиклустеревент|баиклустеревент|
|баиклустернодивент|баиклустернодивент|
|баижобевент|баижобевент|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|блоккчаинаппликатион|Приложение блокчейн|
|фабрикордерер|Структурный заказ|
|фабрикпир|Одноранговый узел структуры|
|Proxy (Прокси)|Proxy (Прокси)|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Блокчейн/Кордамемберс

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|блоккчаинаппликатион|Приложение блокчейн|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/кднвебаппликатионфиреваллполиЦиес

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|вебаппликатионфиревалллогс|Журналы брандмауэра веб-работающие|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|азурекднакцесслог|Журнал доступа Azure CDN|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|CoreAnalytics|Возвращает метрики конечной точки, например пропускную способность, исходящий трафик и т. д.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Событие потока правил группы безопасности сети|Событие потока правил группы безопасности сети|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Аудит|Журналы аудита|
|RequestResponse|Журналы запросов и ответов|
|Трассировка|Журналы трассировки|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|контаинеррегистрилогиневентс|События входа|
|контаинеррегистрирепоситоревентс|Журналы Репоситоревент|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|cluster-autoscaler|Средство автомасштабирования кластера Kubernetes|
|kube-apiserver|Сервер API Kubernetes|
|KUBE — аудит|Аудит Kubernetes|
|kube-controller-manager|Диспетчер контроллеров Kubernetes|
|kube-scheduler|Планировщик Kubernetes|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. Кустомпровидерс/ресаурцепровидерс

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|AuditLogs|Журналы аудита для вызовов Минирп|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. кирпичы и рабочие области

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
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


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|ActivityRuns|Журнал выполнения действий конвейера|
|PipelineRuns|Журнал запусков конвейера|
|TriggerRuns|Журнал запусков триггера|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Аудит|Журналы аудита|
|Requests|Журналы запросов|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|рецеиведшареснапшотс|Получено моментальных снимков общих ресурсов|
|сентшареснапшотс|Отправленные моментальные снимки общих ресурсов|
|Shares (Общие папки)|Shares (Общие папки)|
|шаресубскриптионс|Предоставление общего доступа к подпискам|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|мисклаудитлогс|Журналы аудита MariaDB|
|MySqlSlowLogs|Журналы сервера MariaDB|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|мисклаудитлогс|Журналы аудита MySQL|
|MySqlSlowLogs|Замедляют журналы MySQL|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|мисклаудитлогс|Журналы аудита MySQL|
|MySqlSlowLogs|Журналы сервера MySQL|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|PostgreSQLLogs|Журналы сервера PostgreSQL|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|PostgreSQLLogs|Журналы сервера PostgreSQL|
|QueryStoreRuntimeStatistics|Статистика времени выполнения хранилища запросов PostgreSQL|
|QueryStoreWaitStatistics|Статистика ожидания хранилища запросов PostgreSQL|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|PostgreSQLLogs|Журналы сервера PostgreSQL|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. Десктопвиртуализатион/аппликатионграупс

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Контрольная точка|Контрольная точка|
|Error|Error|
|Управление|Управление|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. Десктопвиртуализатион/хостпулс

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Контрольная точка|Контрольная точка|
|Подключение|Подключение|
|Error|Error|
|HostRegistration|HostRegistration|
|Управление|Управление|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. Десктопвиртуализатион/рабочие области

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Контрольная точка|Контрольная точка|
|Error|Error|
|Веб-канал|Веб-канал|
|Управление|Управление|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
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

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|DeviceOperations|Операции с устройствами|
|ServiceOperations|Операции служб|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|CassandraRequests|CassandraRequests|
|контролпланерекуестс|контролпланерекуестс|
|DataPlaneRequests|DataPlaneRequests|
|гремлинрекуестс|гремлинрекуестс|
|MongoRequests|MongoRequests|
|партитионкэйруконсумптион|партитионкэйруконсумптион|
|партитионкэйстатистикс|партитионкэйстатистикс|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|деливерифаилурес|Журналы сбоев доставки|
|публишфаилурес|Публикация журналов сбоев|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/Системтопикс

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|деливерифаилурес|Журналы сбоев доставки|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|деливерифаилурес|Журналы сбоев доставки|
|публишфаилурес|Публикация журналов сбоев|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|ArchiveLogs|Журналы архивации|
|AutoScaleLogs|Журналы автомасштабирования.|
|кустомерманажедкэйусерлогс|Журналы управляемых ключей клиентов|
|евенсубвнетконнектионевент|Журналы подключений для фильтрации виртуальной сети и IP-адресов|
|кафкакурдинаторлогс|Журналы координатора Kafka|
|кафкаусереррорлогс|Журналы ошибок пользователя Kafka|
|OperationalLogs|Журналы операций|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|AuditLogs|Журналы аудита|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|AutoscaleEvaluations|Оценки автомасштабирования|
|AutoscaleScaleActions|Действия автоматического увеличения масштаба|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
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


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|AuditEvent|Журналы аудита|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Команда|Команда|
|фаилединжестион|Неудачные операции приема|
|инжестионбатчинг|Пакетная обработка приема|
|Запрос|Запрос|
|сукцеедединжестион|Успешные операции приема|
|табледетаилс|Сведения о таблице|
|таблеусажестатистикс|Статистика использования таблиц|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|IntegrationAccountTrackingEvents|Integration Account track events|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|WorkflowRuntime|События диагностики среды выполнения рабочего процесса|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|амлкомпутеклустеревент|амлкомпутеклустеревент|
|амлкомпутеклустернодивент|амлкомпутеклустернодивент|
|амлкомпутекпугпуутилизатион|амлкомпутекпугпуутилизатион|
|амлкомпутежобевент|амлкомпутежобевент|
|амлрунстатусчанжедевент|амлрунстатусчанжедевент|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|кэйделиверирекуестс|Запросы на доставку ключей|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|ApplicationGatewayAccessLog|Журнал доступа к шлюзу приложений|
|ApplicationGatewayFirewallLog|Журнал брандмауэра шлюза приложений|
|ApplicationGatewayPerformanceLog|Журнал производительности шлюза приложений|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|AzureFirewallApplicationRule|Правило приложения службы "Брандмауэр Azure"|
|AzureFirewallNetworkRule|Правило сети службы "Брандмауэр Azure"|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/Бастионхостс

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|бастионаудитлогс|Журналы аудита бастиона|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|PeeringRouteLog|Журналы таблиц пиринга маршрутов|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|FrontdoorAccessLog|Журнал доступа Frontdoor|
|FrontdoorWebApplicationFirewallLog|Журнал брандмауэра веб-приложения Frontdoor|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|LoadBalancerAlertEvent|События оповещения балансировщика нагрузки|
|LoadBalancerProbeHealthStatus|Состояние работоспособности балансировщика нагрузки|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|NetworkSecurityGroupEvent|Событие группы безопасности сети|
|NetworkSecurityGroupFlowEvent|Событие потока правил группы безопасности сети|
|NetworkSecurityGroupRuleCounter|Счетчик правил группы безопасности сети|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|DDoSMitigationFlowLogs|Журналы потоков принятия решений по защите от атак DDoS|
|DDoSMitigationReports|Отчеты о защите от атак DDoS|
|DDoSProtectionNotifications|Уведомления о защите от атак DDoS|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|ProbeHealthStatusEvents|Событие вывода результатов проверки работоспособности диспетчера трафика|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|IKEDiagnosticLog|Журналы диагностики IKE|
|P2SDiagnosticLog|Журналы диагностики P2S|
|RouteDiagnosticLog|Журналы диагностики маршрутов|
|TunnelDiagnosticLog|Журналы диагностики туннелей|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|VMProtectionAlerts|Оповещения о защите виртуальной машины|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Подсистема|Подсистема|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
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

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|хибридконнектионсевент|События Хибридконнектионс|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|OperationLogs|Журналы операций|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|OperationalLogs|Журналы операций|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|алллогс|Журналы службы SignalR Azure.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|девопсоператионсаудит|Журналы аудита операций Devops|
|ResourceUsageStats|Статистика использования ресурсов|
|SQLSecurityAuditEvents|Событие аудита безопасности SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|ошибки|ошибки|
|QueryStoreRuntimeStatistics|Статистика среды выполнения хранилища запросов|
|QueryStoreWaitStatistics|Статистика времени ожидания хранилища запросов|
|SQLInsights|Аналитика SQL|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
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

Стоимость: оплачивается в разделе "журналы платформы" на [странице цен на Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/) 

|Категория |Отображаемое имя категории|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

Стоимость: оплачивается в разделе "журналы платформы" на [странице цен на Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/) 

|Категория |Отображаемое имя категории|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

Стоимость: оплачивается в разделе "журналы платформы" на [странице цен на Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Категория |Отображаемое имя категории|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

Стоимость: оплачивается в разделе "журналы платформы" на [странице цен на Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Категория |Отображаемое имя категории|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|Разработка|Разработка|
|Выполнение|Выполнение|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|буилтинсклрексендед|Завершено запросов встроенного пула SQL|
|гатевайапирекуестс|Запросы API шлюза синапсе|
|SQLSecurityAuditEvents|Событие аудита безопасности SQL|
|синапсербакоператионс|Операции синапсе RBAC|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. синапсе/workspaces/Бигдатапулс

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|бигдатапулаппсендед|Приложения пула больших данных завершены|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. синапсе/workspaces/Склпулс

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|DmsWorkers|Рабочие роли DMS|
|ExecRequests|Выполняющиеся запросы|
|RequestSteps|Действия, из которых состоит запрос|
|SqlRequests|Запросы SQL|
|SQLSecurityAuditEvents|Событие аудита безопасности SQL|
|Ожидания|Ожидания|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

Стоимость: Бесплатная 

|Категория |Отображаемое имя категории|
|---|---|
|аппсервицеенвиронментплатформлогс|Журналы платформы Среда службы приложений|


## <a name="microsoftwebsites"></a>microsoft.web/sites

Стоимость: Бесплатная 


|Категория |Отображаемое имя категории|
|---|---|
|аппсервицеапплогс|Журналы приложения службы приложений|
|аппсервицеаудитлогс|Доступ к журналам аудита|
|аппсервицеконсолелогс|Журналы консоли службы приложений|
|аппсервицефилеаудитлогс|Журналы аудита изменения содержимого сайта|
|аппсервицехттплогс|Журналы HTTP|
|функтионапплогс|Журналы приложений функций|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

Стоимость: Бесплатная 


|Категория |Отображаемое имя категории|
|---|---|
|аппсервицеапплогс|Журналы приложения службы приложений|
|аппсервицеаудитлогс|Доступ к журналам аудита|
|аппсервицеконсолелогс|Журналы консоли службы приложений|
|аппсервицефилеаудитлогс|Журналы аудита изменения содержимого сайта|
|аппсервицехттплогс|Журналы HTTP|
|функтионапплогс|Журналы приложений функций|


## <a name="next-steps"></a>Next Steps

* [Дополнительные сведения о журналах ресурсов](./platform-logs-overview.md)
* [Потоковая передача журналов ресурсов в **концентраторы событий**](./resource-logs.md#send-to-azure-event-hubs)
* [Изменение параметров диагностики журнала ресурсов с помощью Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Сбор журналов и метрик для служб Azure для использования в Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

