---
title: Поддерживаемые ресурсы для оповещений метрик в Azure Monitor
description: Справочник по поддерживаемым метрикам и журналам для оповещений метрик в Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: c036fa3708d718d6199d27989e60b11015a1227e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585855"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Поддерживаемые ресурсы для оповещений метрик в Azure Monitor

Azure Monitor теперь поддерживает [новый тип оповещений о метриках](../../azure-monitor/platform/alerts-overview.md), который имеет ряд преимуществ перед [классическими оповещениями](../../azure-monitor/platform/alerts-classic.overview.md). Метрики доступны для [большого числа служб Azure](../../azure-monitor/platform/metrics-supported.md). Список типов ресурсов, поддерживаемых новыми оповещениями, постоянно расширяется. В этой статье рассматриваются новые возможности.

Вы также можете использовать новые оповещения метрики для популярных данных журналов, хранящихся в Log Analytics рабочей области, извлеченной в качестве метрик. Дополнительные сведения см. в статье [Создание оповещений о метриках для журналов в Azure Monitor](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Поддержка портала, PowerShell, CLI и REST
Сейчас можно создавать новые оповещения метрик только в [шаблонах](../../azure-monitor/platform/alerts-metric-create-templates.md)портал Azure, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)или диспетчер ресурсов. Возможность настройки новых оповещений с помощью PowerShell и Azure CLI версии 2.0 и выше будет реализована в ближайшее время.

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Новые оповещения на основе метрик поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor](../../azure-monitor/platform/metrics-charts.md).

Вот полный список источников метрик Azure Monitor, которые поддерживаются в новых оповещениях:

|Тип ресурса  |Поддерживаемые измерения |Оповещения о нескольких ресурсах| Доступные метрики|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Да| Нет | [Управление API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft. Аппплатформ/пружина |Нет| Да|
|Microsoft.Automation/automationAccounts | Да| Нет | [Учетные записи автоматизации](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Недоступно| Нет | [Ученые записи пакетной службы](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis;|Да| Нет |[Кэш Azure для Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft. Классикстораже/storageAccounts/ммксклассик|Нет|Да|
|Microsoft. Классикстораже/storageAccounts/ммксклассик/Блобсервицес|Нет|Да|
|Microsoft. Классикстораже/storageAccounts/ммксклассик/Филесервицес|Нет|Да|
|Microsoft. Классикстораже/storageAccounts/ммксклассик/Куеуесервицес|Нет|Да|
|Microsoft. Классикстораже/storageAccounts/ммксклассик/Таблесервицес|Нет|Да| |
|Microsoft.CognitiveServices/accounts| Недоступно | Нет | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Да | Да | [Виртуальные машины](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets; |Недоступно | Да |[Масштабируемые наборы виртуальных машин](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Да| Нет | [Группы контейнеров](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Да | Нет | [Управляемые кластеры](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft. Датабокседже/Датабокседжедевицес | Да | Да | |
|Microsoft.DataFactory/datafactories| Да| Нет | [Фабрики данных V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories; |Да | Нет |[Фабрики данных V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft. файл или учетные записи |Нет| Да|
|Microsoft.DBforMySQL/servers |Недоступно| Нет |[База данных для MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |Недоступно | Нет | [База данных для PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | Недоступно | Нет |[Метрики центра Интернета вещей](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Да | Нет |[Метрики DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft. EventGrid/Domains|Нет|Да| |
|Microsoft.EventGrid/topics |Да | Нет |[Статьи по сетке событий](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Да| Нет |[Кластеры концентраторов событий](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Да| Нет |[Центры событий](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Нет |Нет |[Хранилища](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |Недоступно | Нет |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Да| Нет | [Машинное обучение](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Да| Нет | [Пулы ресурсов Azure NetApp](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/Нетаппаккаунтс/КапаЦитипулс/Volumes |Да| Нет | [Тома NetApp для Azure](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|Недоступно| Нет |  |
|Microsoft.Network/dnsZones | Недоступно| Нет | [Зоны DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Недоступно | Нет |[Цепи Express Route](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (только для SKU "Стандартный")| Да| Нет | [Подсистемы балансировки нагрузки](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/Натгатевайс|Нет|Да|
|Microsoft. Network/Приватиндпоинтс|Нет|Да|
|Microsoft. Network/Привателинксервицес|Нет|Да|
|Microsoft.Network/publicipaddresses; |Недоступно | Нет |[общедоступные IP-адреса](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Да | Нет | [Профили диспетчера трафика](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Да | Нет | [Рабочие области Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Да | Нет | [Ретрансляторы](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft. пиринг/Пирингсервицес|Нет|Да|
|Microsoft.PowerBIDedicated/capacities | Недоступно | Нет | [Емкости](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |Недоступно|Нет | [Службы поиска](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Да| Нет |[Служебная шина](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |    Нет | Да |
|Microsoft.Sql/servers/databases    | Нет | Да |
|Microsoft.Storage/storageAccounts |Да | Нет | [Учетные записи хранения](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Да| Нет | [Службы BLOB-объектов](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [службы файлов](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [службы очередей](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) и [службы таблиц](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |Недоступно| Нет | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft. Microsoft. Вмвареклаудсимпле/virtualMachines |Да|Нет |[Виртуальные машины CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Да | Нет | [Среда службы приложений пулов с несколькими ролями](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Да | Нет | [Пулы рабочих ролей Среда службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Да | Нет | [Планы службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Да | Нет | [Службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) и [Функции](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Да | Нет | [Слоты Службы приложений](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

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

## <a name="next-steps"></a>Дальнейшие шаги

* Получите дополнительные сведения об [интерфейсе оповещений](../../azure-monitor/platform/alerts-overview.md).
* Ознакомьтесь со сведениями об [оповещениях журналов в Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Сведения об [оповещениях в Azure](../../azure-monitor/platform/alerts-overview.md).
