---
title: Поддерживаемые ресурсы для оповещений метрик в Azure Monitor
description: Справочник по поддерживаемым метрикам и журналам для оповещений метрик в Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 9/30/2020
ms.subservice: alerts
ms.openlocfilehash: d99e37171f2fb5b86bee897caca55c3e8782f92e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108819"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Поддерживаемые ресурсы для оповещений метрик в Azure Monitor

Azure Monitor теперь поддерживает [новый тип оповещений о метриках](./alerts-overview.md), который имеет ряд преимуществ перед [классическими оповещениями](./alerts-classic.overview.md). Метрики доступны для [большого числа служб Azure](./metrics-supported.md). Список типов ресурсов, поддерживаемых новыми оповещениями, постоянно расширяется. В этой статье рассматриваются новые возможности.

Вы также можете использовать новые оповещения метрики для популярных данных журналов, хранящихся в Log Analytics рабочей области, извлеченной в качестве метрик. Дополнительные сведения см. в статье [Создание оповещений о метриках для журналов в Azure Monitor](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Поддержка портала, PowerShell, CLI и REST
Сейчас можно создавать новые оповещения метрик только в [шаблонах](./alerts-metric-create-templates.md)портал Azure, [REST API](/rest/api/monitor/metricalerts/)или диспетчер ресурсов. Возможность настройки новых оповещений с помощью PowerShell и Azure CLI версии 2.0 и выше будет реализована в ближайшее время.

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Новые оповещения на основе метрик поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor](./metrics-charts.md).

Вот полный список источников метрик Azure Monitor, которые поддерживаются в новых оповещениях:

|Тип ресурса  |Поддерживаемые измерения |Оповещения о нескольких ресурсах| Доступные метрики|
|---------|---------|-----|----------|
|Microsoft. Аадиам/Азуреадметрикс | Да | Нет | |
|Microsoft.ApiManagement/service | Да | Нет | [Управление API](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Да | Нет | [Конфигурация приложений](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | Да | Нет | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | Да| Нет | [Учетные записи автоматизации](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/Приватеклаудс | Нет | Нет | |
|Microsoft.Batch/batchAccounts | Да | Нет | [Ученые записи пакетной службы](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis; | Да | Да | [Кэш Azure для Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | Нет | Нет | [Классические облачные службы](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.classicСompute/virtualMachines | Нет | Нет | [Классические виртуальные машины](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | Да | Нет | [Учетные записи хранения (классические)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Да | Нет | |
|Microsoft.ClassicStorage/storageAccounts/fileServices | Да | Нет | |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Да | Нет | |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Да | Нет | |
|Microsoft.CognitiveServices/accounts | Да | Нет | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Да | Да<sup>1</sup> | [Виртуальные машины](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets; | Да | Нет |[Масштабируемые наборы виртуальных машин](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Да| Нет | [Группы контейнеров](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | Нет | Нет | [Реестры контейнеров](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Да | Нет | [Управляемые кластеры](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Да | Да | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Да| Нет | [Фабрики данных V1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories; |Да | Нет | [Фабрики данных V2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Да | Нет | |
|Microsoft.DBforMariaDB/servers | Нет | Нет | [База данных для MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Нет | Нет |[База данных для MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/flexibleServers | Да | Нет | |
|Microsoft.DBforPostgreSQL/servers | Нет | Нет | [База данных для PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | Нет | Нет | [База данных для PostgreSQL v2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. Дбфорпостгрескл/синглесерверс | Нет | Нет | [База данных для PostgreSQL (одиночные серверы)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft.Devices/IotHubs | Да | Нет |[Центр Интернета вещей](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Да | Нет | [Службы подготовки устройств](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. Дигиталтвинс/Дигиталтвинсинстанцес | Да | Нет | |
|Microsoft.DocumentDB/databaseAccounts | Да | Нет | [База данных Cosmos](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | Да | Нет | [Домены Сетки событий](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/Системтопикс | Да | Нет | [Статьи по системе сетки событий](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |Да | Нет | [Разделы Сетки событий](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Да| Нет | [Кластеры концентраторов событий](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Да| Нет | [Центры событий](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Да | Нет | [Кластеры HDInsight](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | Да | Нет | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Да |Да |[Хранилища](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Да |Нет |[Кластеры обозреватель данных](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Да | Нет |[Среды службы интеграции](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Нет | Нет |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Да | Нет | [Машинное обучение](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | Да | Нет | [Сопоставление учетных записей](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | Нет | Нет | [Службы мультимедиа](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Да | Нет | [Конечные точки потоковой передачи служб мультимедиа](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Да | Да | [Пулы ресурсов Azure NetApp](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Да | Да | [Тома NetApp для Azure](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Да | Нет | [Шлюзы приложений](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Да | Нет | [Брандмауэры](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Нет | Нет | [Зоны DNS](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Недоступно | Нет |[Каналы ExpressRoute](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (только для SKU "Стандартный")| Да| Нет | [Подсистемы балансировки нагрузки](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/Натгатевайс| Нет | Нет | |
|Microsoft. Network/Приватиндпоинтс| Нет | Нет | |
|Microsoft.Network/privateLinkServices| Нет | Нет |
|Microsoft.Network/publicipaddresses; | Нет | Нет |[общедоступные IP-адреса](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Да | Нет | [Профили диспетчера трафика](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Да | Нет | [Рабочие области Log Analytics](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. пиринг/пиринг | Да | Нет | [Пиринги](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. пиринг/Пирингсервицес | Да | Нет | [Службы пиринга](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | Нет | Нет | [Производительность](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Да | Нет | [Ретрансляторы](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | Нет | Нет | [Службы поиска](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Да | Нет | [Служебная шина](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | Нет | Да | [Управляемые экземпляры SQL](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Нет | Да | [Базы данных SQL](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | Нет | Да | [Эластичные пулы SQL](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Да | Нет | [Учетные записи хранения](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Да| Нет | [Службы BLOB-объектов](./metrics-supported.md#microsoftstoragestorageaccountsblobservices), [службы файлов](./metrics-supported.md#microsoftstoragestorageaccountsfileservices), [службы очередей](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) и [службы таблиц](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StorageCache/caches | Да | Нет | |
|Microsoft. StorageSync/Сторажесинксервицес | Да | Нет | [Службы синхронизации хранилища](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Да | Нет | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. Вмвареклаудсимпле/virtualMachines | Да | Нет | [Виртуальные машины CloudSimple](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Да | Нет | [Среда службы приложений пулов с несколькими ролями](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Да | Нет | [Пулы рабочих ролей Среда службы приложений](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Да | Нет | [Планы службы приложений](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Да | Нет | [Службы приложений](./metrics-supported.md#microsoftwebsites-excluding-functions) и [Функции](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Да | Нет | [Слоты Службы приложений](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> не поддерживается для метрик сети виртуальной машины (общая сеть, общий объем сети, входящие потоки, исходящие потоки, максимальная частота создания входящих потоков, максимальная скорость создания исходящих потоков) и пользовательские метрики.

## <a name="payload-schema"></a>Схема полезных данных

> [!NOTE]
> Вы также можете использовать [общую схему предупреждений](./alerts-common-schema.md), которая предоставляет преимущества единого расширяемого и унифицированного набора полезных данных оповещений во всех службах предупреждений в Azure Monitor для интеграции веб-перехватчика. [Сведения об общих определениях схемы предупреждений.](./alerts-common-schema-definitions.md)


Если используется соответствующим образом настроенная [группа действий](./action-groups.md), то операция POST содержит следующие полезные данные и схему JSON для всех новых оповещений на основе метрик:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Получите дополнительные сведения об [интерфейсе оповещений](./alerts-overview.md).
* Ознакомьтесь со сведениями об [оповещениях журналов в Azure](./alerts-unified-log.md).
* Сведения об [оповещениях в Azure](./alerts-overview.md).