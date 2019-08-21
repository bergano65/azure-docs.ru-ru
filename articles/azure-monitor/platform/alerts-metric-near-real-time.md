---
title: Поддерживаемые ресурсы для оповещений метрик в Azure Monitor
description: Справочник по поддерживаемым метрикам и журналам для оповещений метрик в Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 37ef1cda37e1799a4dc488947e6c9ed4c9ad4055
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636183"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Поддерживаемые ресурсы для оповещений метрик в Azure Monitor

Azure Monitor теперь поддерживает [новый тип оповещений о метриках](../../azure-monitor/platform/alerts-overview.md), который имеет ряд преимуществ перед [классическими оповещениями](../../azure-monitor/platform/alerts-classic.overview.md). Метрики доступны для [большого числа служб Azure](../../azure-monitor/platform/metrics-supported.md). Список типов ресурсов, поддерживаемых новыми оповещениями, постоянно расширяется. В этой статье рассматриваются новые возможности.

Вы также можете использовать новые оповещения метрики для популярных данных журналов, хранящихся в Log Analytics рабочей области, извлеченной в качестве метрик. Дополнительные сведения см. в статье [Создание оповещений о метриках для журналов в Azure Monitor](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Поддержка портала, PowerShell, CLI и REST
В настоящее время новые оповещения на основе метрик можно создать только на портале Azure, с помощью [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/) или [шаблонов Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md). Возможность настройки новых оповещений с помощью PowerShell и Azure CLI версии 2.0 и выше будет реализована в ближайшее время.

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Новые оповещения на основе метрик поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor](../../azure-monitor/platform/metrics-charts.md).

Вот полный список источников метрик Azure Monitor, которые поддерживаются в новых оповещениях:

|Тип ресурса  |Поддерживаемые измерения  | Доступные метрики|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Да        | [Управление API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Да   | [Учетные записи службы автоматизации](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Н/Д| [Учетные записи пакетной службы](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis;     |    Н/Д     |[Кэш Azure для Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    Н/Д     | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    Н/Д     | [Виртуальные машины](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets;     |   Н/Д      |[Масштабируемые наборы виртуальных машин](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Да| [Группы контейнеров](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Да | [Управляемые кластеры](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Да| [Фабрики данных V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories;     |   Да     |[Фабрики данных V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Н/Д      |[База данных для MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Н/Д     | [База данных для PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs    | Н/Д     |[Метрики центра Интернета вещей](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)
|Microsoft.Devices/provisioningServices    | Да     |[Метрики DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)
|Microsoft.EventHub/namespaces     |  Да      |[Центры событий](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Нет | [Хранилища](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     Н/Д    |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Н/Д     | [Шлюзы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | Н/Д| [Зоны DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Н/Д |  [Цепи Express Route](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (только для SKU "Стандартный")| Да| [Подсистемы балансировки нагрузки.](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses;     |  Н/Д       |[Общедоступные IP-адреса](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Да | [Профили диспетчера трафика](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Да|[Рабочие области Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.PowerBIDedicated/capacities | Н/Д | [Емкости](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   Н/Д      |[Службы поиска](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Да       |[Служебная шина](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Да     | [Учетные записи хранения](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Да    | [Службы BLOB-объектов](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [службы файлов](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [службы очередей](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) и [службы таблиц](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Н/Д       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Да | [Планы службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Да | [Службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) и [Функции](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Да | [Слоты Службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|


## <a name="payload-schema"></a>Схема полезных данных

> [!NOTE]
> Вы также можете использовать [общую схему предупреждений](https://aka.ms/commonAlertSchemaDocs), которая предоставляет преимущества единого расширяемого и унифицированного набора полезных данных оповещений во всех службах предупреждений в Azure Monitor для интеграции веб-перехватчика. [Сведения об общих определениях схемы предупреждений.](https://aka.ms/commonAlertSchemaDefinitions)


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

## <a name="next-steps"></a>Следующие шаги

* Получите дополнительные сведения об [интерфейсе оповещений](../../azure-monitor/platform/alerts-overview.md).
* Ознакомьтесь со сведениями об [оповещениях журналов в Azure](../../azure-monitor/platform/alerts-unified-log.md).
* [Подробнее об оповещениях в Azure](../../azure-monitor/platform/alerts-overview.md)
