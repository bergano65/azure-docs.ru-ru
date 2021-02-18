---
title: Поддерживаемые ресурсы для оповещений метрик в Azure Monitor
description: Справочник по поддерживаемым метрикам и журналам для оповещений метрик в Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: 7b4cdd37cefb628c03129a990d52ed348acd3ac1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622038"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Поддерживаемые ресурсы для оповещений метрик в Azure Monitor

Azure Monitor теперь поддерживает [новый тип оповещений о метриках](../platform/alerts-overview.md), который имеет ряд преимуществ перед [классическими оповещениями](./alerts-classic.overview.md). Метрики доступны для [большого числа служб Azure](../platform/metrics-supported.md). Список типов ресурсов, поддерживаемых новыми оповещениями, постоянно расширяется. В этой статье рассматриваются новые возможности.

Вы также можете использовать новые оповещения метрики для популярных данных журналов, хранящихся в Log Analytics рабочей области, извлеченной в качестве метрик. Дополнительные сведения см. в статье [Создание оповещений о метриках для журналов в Azure Monitor](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Поддержка портала, PowerShell, CLI и REST
Сейчас можно создавать новые оповещения метрик только в [шаблонах](./alerts-metric-create-templates.md)портал Azure, [REST API](/rest/api/monitor/metricalerts/)или диспетчер ресурсов. Возможность настройки новых оповещений с помощью PowerShell и Azure CLI версии 2.0 и выше будет реализована в ближайшее время.

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Новые оповещения на основе метрик поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor](../essentials/metrics-charts.md).

Ниже приведен полный список источников метрик Azure Monitor, поддерживаемых новыми оповещениями.

|Тип ресурса  |Поддерживаемые измерения |Оповещения о нескольких ресурсах| Доступные метрики|
|---------|---------|-----|----------|
|Microsoft. Аадиам/Азуреадметрикс | Да | Нет | |
|Microsoft.ApiManagement/service | Да | Нет | [Управление API](../platform/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Да | Нет | [Конфигурация приложений](../platform/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | Да | Нет | [Azure Spring Cloud](../platform/metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | Да| Нет | [Учетные записи автоматизации](../platform/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/Приватеклаудс | Нет | Нет | [Решение Azure VMware](../platform/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Да | Нет | [Ученые записи пакетной службы](../platform/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis; | Да | Да | [Кэш Azure для Redis](../platform/metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | Нет | Нет | [Классические облачные службы](../platform/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.classicСompute/virtualMachines | Нет | Нет | [Классические виртуальные машины](../platform/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | Да | Нет | [Учетные записи хранения (классические)](../platform/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Да | Нет | [Учетные записи хранения (классические) — BLOB-объекты](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft.ClassicStorage/storageAccounts/fileServices | Да | Нет | [Учетные записи хранения (классические) — файлы](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Да | Нет | [Учетные записи хранения (классические) — очереди](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Да | Нет | [Учетные записи хранения (классические) — таблицы](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft.CognitiveServices/accounts | Да | Нет | [Cognitive Services](../platform/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Да | Да<sup>1</sup> | [Виртуальные машины](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets; | Да | Нет |[Масштабируемые наборы виртуальных машин](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Да| Нет | [Группы контейнеров](../platform/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | Нет | Нет | [Реестры контейнеров](../platform/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Да | Нет | [Управляемые кластеры](../platform/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Да | Да | [Data Box](../platform/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Да| Нет | [Фабрики данных V1](../platform/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories; |Да | Нет | [Фабрики данных V2](../platform/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Да | Нет | [Общие ресурсы данных](../platform/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft.DBforMariaDB/servers | Нет | Нет | [База данных для MariaDB](../platform/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Нет | Нет |[База данных для MySQL](../platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers | Нет | Нет | [База данных для PostgreSQL](../platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | Нет | Нет | [База данных для PostgreSQL v2](../platform/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/flexibleServers | Да | Нет | [База данных для PostgreSQL (гибкие серверы)](../platform/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.Devices/IotHubs | Да | Нет |[Центр Интернета вещей](../platform/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Да | Нет | [Службы подготовки устройств](../platform/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. Дигиталтвинс/Дигиталтвинсинстанцес | Да | Нет | [Digital Twins](../platform/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Да | Нет | [База данных Cosmos](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | Да | Нет | [Домены Сетки событий](../platform/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/Системтопикс | Да | Нет | [Статьи по системе сетки событий](../platform/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |Да | Нет | [Разделы Сетки событий](../platform/metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Да| Нет | [Кластеры концентраторов событий](../platform/metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Да| Нет | [Центры событий](../platform/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Да | Нет | [Кластеры HDInsight](../platform/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | Да | Нет | [Application Insights](../platform/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Да |Да |[Хранилища](../platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Да |Нет |[Кластеры обозреватель данных](../platform/metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Да | Нет |[Среды службы интеграции](../platform/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Нет | Нет |[Logic Apps](../platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Да | Нет | [Машинное обучение](../platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | Да | Нет | [Сопоставление учетных записей](../platform/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | Нет | Нет | [Службы мультимедиа](../platform/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Да | Нет | [Конечные точки потоковой передачи служб мультимедиа](../platform/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Да | Да | [Пулы ресурсов Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Да | Да | [Тома NetApp для Azure](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Да | Нет | [Шлюзы приложений](../platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Да | Нет | [брандмауэры;](../platform/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Нет | Нет | [Зоны DNS](../platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Да | Нет |[Каналы ExpressRoute](../platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | Да | Нет |[ExpressRoute Direct](../platform/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers (только для SKU "Стандартный")| Да| Нет | [Подсистемы балансировки нагрузки](../platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/Натгатевайс| Нет | Нет | [Шлюзы NAT](../platform/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/Приватиндпоинтс| Нет | Нет | [Частные конечные точки](../platform/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft.Network/privateLinkServices| Нет | Нет | [Службы частной связи](../platform/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft.Network/publicipaddresses; | Нет | Нет | [общедоступные IP-адреса](../platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Да | Нет | [Профили диспетчера трафика](../platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Да | Нет | [Рабочие области Log Analytics](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. пиринг/пиринг | Да | Нет | [Пиринги](../platform/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. пиринг/Пирингсервицес | Да | Нет | [Службы пиринга](../platform/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | Нет | Нет | [Производительность](../platform/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Да | Нет | [Ретрансляторы](../platform/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | Нет | Нет | [Службы поиска](../platform/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Да | Нет | [Служебная шина](../platform/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | Нет | Да | [Управляемые экземпляры SQL](../platform/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Нет | Да | [Базы данных SQL](../platform/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | Нет | Да | [Эластичные пулы SQL](../platform/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Да | Нет | [Учетные записи хранения](../platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/blobServices | Да| Нет | [Учетные записи хранения — BLOB-объекты](../platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft.Storage/storageAccounts/fileServices | Да| Нет | [Учетные записи хранения — файлы](../platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft.Storage/storageAccounts/queueServices | Да| Нет | [Учетные записи хранения — очереди](../platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft.Storage/storageAccounts/tableServices | Да| Нет | [Учетные записи хранения — таблицы](../platform/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft.StorageCache/caches | Да | Нет | [Кэши HPC](../platform/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/Сторажесинксервицес | Да | Нет | [Службы синхронизации хранилища](../platform/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Да | Нет | [Stream Analytics](../platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | Да | Нет | [Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. синапсе/workspaces/Бигдатапулс | Да | Нет | [Пулы Apache Spark аналитики синапсе](../platform/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. синапсе/workspaces/Склпулс | Да | Нет | [Пулы SQL Analytics синапсе](../platform/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. Вмвареклаудсимпле/virtualMachines | Да | Нет | [Виртуальные машины CloudSimple](../platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Да | Нет | [Среда службы приложений пулов с несколькими ролями](../platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Да | Нет | [Пулы рабочих ролей Среда службы приложений](../platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Да | Нет | [Планы службы приложений](../platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Да | Нет | [Службы приложений и Функции](../platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Да | Нет | [Слоты Службы приложений](../platform/metrics-supported.md#microsoftwebsitesslots)|

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

## <a name="next-steps"></a>Дальнейшие шаги

* Получите дополнительные сведения об [интерфейсе оповещений](../platform/alerts-overview.md).
* Ознакомьтесь со сведениями об [оповещениях журналов в Azure](./alerts-unified-log.md).
* Сведения об [оповещениях в Azure](../platform/alerts-overview.md).