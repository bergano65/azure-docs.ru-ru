---
title: Поддерживаемые ресурсы для оповещений метрик в Azure Monitor
description: Справочник по поддерживаемым метрикам и журналам для оповещений метрик в Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: 62c1e29b668c3ef58b0e26e2749c55b913279425
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545679"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Поддерживаемые ресурсы для оповещений метрик в Azure Monitor

Azure Monitor теперь поддерживает [новый тип оповещений о метриках](../../azure-monitor/platform/alerts-overview.md), который имеет ряд преимуществ перед [классическими оповещениями](../../azure-monitor/platform/alerts-classic.overview.md). Метрики доступны для [большого числа служб Azure](../../azure-monitor/platform/metrics-supported.md). Список типов ресурсов, поддерживаемых новыми оповещениями, постоянно расширяется. В этой статье рассматриваются новые возможности.

Вы также можете использовать новые метрические оповещения на популярных данных журнала, хранящихся в рабочей области журнала Analytics, извлеченных в качестве метрик. Дополнительные сведения см. в статье [Создание оповещений о метриках для журналов в Azure Monitor](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Поддержка портала, PowerShell, CLI и REST
В настоящее время создавать новые метрические оповещения можно только на портале Azure, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)или [шаблонах менеджера ресурсов.](../../azure-monitor/platform/alerts-metric-create-templates.md) Возможность настройки новых оповещений с помощью PowerShell и Azure CLI версии 2.0 и выше будет реализована в ближайшее время.

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Новые оповещения на основе метрик поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor](../../azure-monitor/platform/metrics-charts.md).

Вот полный список источников метрик Azure Monitor, которые поддерживаются в новых оповещениях:

|Тип ресурса  |Поддерживаемые измерения |Многоресурсные оповещения| Доступные метрики|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Да| нет | [Управление API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Весна |нет| Да|
|Microsoft.Automation/automationAccounts | Да| нет | [Учетные записи автоматизации](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Н/Д| нет | [Ученые записи пакетной службы](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis;|Да| нет |[Кэш Redis для Azure](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic|нет|Да|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices|нет|Да|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices|нет|Да|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices|нет|Да|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices|нет|Да| |
|Microsoft.CognitiveServices/accounts| Н/Д | нет | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Да | Да | [Виртуальные машины](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets; |Н/Д | Да |[Масштабируемые наборы виртуальных машин](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Да| нет | [Группы контейнеров](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Да | нет | [Управляемые кластеры](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/DataBoxEdgeDevices | Да | Да | |
|Microsoft.DataFactory/datafactories| Да| нет | [Фабрики данных V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories; |Да | нет |[Фабрики данных V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/учетные записи |нет| Да|
|Microsoft.DBforMySQL/servers |Н/Д| нет |[База данных для MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |Н/Д | нет | [База данных для PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | Н/Д | нет |[Метрики концентратора IoT](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Да | нет |[Метрики ДПС](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/домены|нет|Да| |
|Microsoft.EventGrid/topics |Да | нет |[Темы сетки событий](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Да| нет |[Кластеры концентраторов событий](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Да| нет |[Центры событий](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| нет |нет |[Хранилища](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |Н/Д | нет |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Да| нет | [Машинное обучение](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Да| нет | [Пулы емкости Сети Azure](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes |Да| нет | [Объемы неттов Azure](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|Н/Д| нет |  |
|Microsoft.Network/dnsZones | Н/Д| нет | [Зоны DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Н/Д | нет |[Цепи Express Route](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (только для SKU "Стандартный")| Да| нет | [Балансировоки нагрузки](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|нет|Да|
|Microsoft.Network/privateEndpoints|нет|Да|
|Microsoft.Network/privateLinkServices|нет|Да|
|Microsoft.Network/publicipaddresses; |Н/Д | нет |[Общедоступные IP-адреса](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Да | нет | [Профили диспетчера трафика](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Да | нет | [Рабочие области Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Да | нет | [Ретрансляторы](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.peering/peeringServices|нет|Да|
|Microsoft.PowerBIDedicated/capacities | Н/Д | нет | [Мощности](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |Н/Д|нет | [Службы поиска](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Да| нет |[Служебная шина](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |    нет | Да |
|Microsoft.Sql/servers/databases    | нет | Да |
|Microsoft.Storage/storageAccounts |Да | нет | [Учетные записи хранения](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Да| нет | [Службы BLOB-объектов](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [службы файлов](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [службы очередей](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) и [службы таблиц](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |Н/Д| нет | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Да|нет |[Виртуальные машины CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Да | нет | [Среда службы приложений Многофункциональные бассейны](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Да | нет | [Бассейны для работников службы охраны окружающей среды App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Да | нет | [Планы обслуживания приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Да | нет | [Службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Да | нет | [Слоты Службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Схема полезных данных

> [!NOTE]
> Вы также можете использовать [общую схему оповещения,](https://aka.ms/commonAlertSchemaDocs)которая обеспечивает преимущество наличия единой расширяемой и единой полезной нагрузки оповещения во всех службах оповещения в Azure Monitor для интеграции веб-крючка. [Узнайте об общих определениях схемы оповещения.](https://aka.ms/commonAlertSchemaDefinitions)


Если используется соответствующим образом настроенная [группа действий](../../azure-monitor/platform/action-groups.md), то операция POST содержит следующие полезные данные и схему JSON для всех новых оповещений на основе метрик:

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

* Получите дополнительные сведения об [интерфейсе оповещений](../../azure-monitor/platform/alerts-overview.md).
* Ознакомьтесь со сведениями об [оповещениях журналов в Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Узнайте о [предупреждениях в Azure](../../azure-monitor/platform/alerts-overview.md).
