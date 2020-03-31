---
title: Network Watcher - Создание журналов потоков NSG с помощью шаблона управления ресурсами Azure
description: Используйте шаблон менеджера ресурсов Azure и PowerShell для легкой настройки журналов потоков NSG.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 35d185a625a81a259c366a45999769ecf76c6a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538164"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Настройка журналов потоков NSG из шаблона управления ресурсами Azure

> [!div class="op_single_selector"]
> - [Портал Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Лазурный CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Менеджер ресурсов Azure](https://azure.microsoft.com/features/resource-manager/) — это назаедвый и мощный способ управления инфраструктурой в [качестве кода.](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

В этой статье показано, как можно включить [журналы NSG Flow](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programmatically с помощью шаблона менеджера ресурсов Azure и Azure PowerShell. Начнем с обзора свойств объекта NSG Flow Log Log, за которым следуют несколько шаблонов. Затем мы развертываем шаблон с помощью локального экземпляра PowerShell.


## <a name="nsg-flow-logs-object"></a>Объект NSG Flow Logs

Объект NSG Flow Logs со всеми параметрами показан ниже.
Для полного обзора свойств можно прочитать [ссылку на шаблон NSG Flow Logs.](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Чтобы создать ресурс Microsoft.Network/networkWatchers/flowLogs, добавьте вышеупомянутую JSON в раздел ресурсов шаблона.


## <a name="creating-your-template"></a>Создание шаблона

Если вы используете шаблоны Azure Resource Manager впервые, вы можете узнать больше о них, используя ссылки ниже.

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Учебник: Создайте и разместите первый шаблон управления ресурсами Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Ниже приведены два примера полных шаблонов для настройки журналов ПОТОКА NSG.

**Пример 1**: Простейшая версия вышесу с минимальными параметрами пройдена. Ниже шаблон позволяет NSG Flow журналы на целевой NSG и хранит их в данной учетной записи хранения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Название ресурса имеет формат "Родительский ресурс>/детский ресурс". Здесь родительским ресурсом является региональный экземпляр Network<RegionName>Watcher (Формат: NetworkWatcher_ . Пример: NetworkWatcher_centraluseuap)
> * targetResourceId — это идентификатор ресурсов целевой NSG
> * storageId — это идентификатор ресурса учетной записи хранения назначения

**Пример 2**: Следующие шаблоны, позволяющие NSG Flow Logs (версия 2) с удержанием в течение 5 дней. Включение анализа трафика с интервалом обработки 10 минут.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Развертывание шаблона управления ресурсами Azure

Этот учебник предполагает, что у вас есть существующая группа ресурсов и NSG, который можно включить flow в журнал.
Вы можете сохранить любой из приведенных `azuredeploy.json`выше шаблонов примера локально, как . Обновите значения свойств, чтобы они указывать на действительные ресурсы в подписке.

Чтобы развернуть шаблон, запустите следующую команду в PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Проверка развертывания

Существует несколько способов проверить, удалось ли развертывание. Консоль PowerShell должна отображать "ProvisioningState" как "Успешно". Кроме того, вы можете посетить [страницу портала NSG Flow Logs,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) чтобы подтвердить свои изменения. Если возникли проблемы с развертыванием, осмотрите [общие ошибки развертывания Отдела Задач с менеджером ресурсов Azure.](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)


## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как визуализировать данные NSG Flow с помощью:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Инструменты с открытым исходным кодом](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Аналитика трафика Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
