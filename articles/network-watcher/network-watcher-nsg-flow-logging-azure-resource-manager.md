---
title: Наблюдатель за сетями — создание журналов потоков NSG с помощью шаблона Azure Resource Manager
description: Используйте шаблон Azure Resource Manager и PowerShell, чтобы легко настроить журналы потоков NSG.
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
ms.openlocfilehash: 2ae6314accf5ad9143e1e103239c2e7c884ec947
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776642"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Настройка журналов потоков NSG из шаблона Azure Resource Manager

> [!div class="op_single_selector"]
> - [Портал Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) является собственным и мощным способом управления [инфраструктурой как кодом](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code).

В этой статье показано, как программно включить [журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) с помощью шаблона Azure Resource Manager и Azure PowerShell. Начнем с обзора свойств объекта журнала потоков NSG, за которым следуют несколько примеров шаблонов. Затем мы используем шаблон развертывания с помощью локального экземпляра PowerShell.


## <a name="nsg-flow-logs-object"></a>Объект журналов потоков NSG

Ниже показан объект журналов потоков NSG со всеми параметрами with.
Полный обзор свойств см. в статье [Справочник по шаблону журналов потоков NSG](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

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
Чтобы создать ресурс Microsoft. Network/Нетворкватчерс/панель, добавьте приведенный выше код JSON в раздел ресурсов шаблона.


## <a name="creating-your-template"></a>Создание шаблона

Если вы используете шаблоны Azure Resource Manager в первый раз, вы можете узнать больше о них, используя приведенные ниже ссылки.

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Руководство. Создание и развертывание первого шаблона Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Ниже приведены два примера готовых шаблонов для настройки журналов потоков NSG.

**Пример 1**. самая простая версия выше с минимальным числом переданных параметров. Приведенный ниже шаблон включает журналы потоков NSG на целевом NSG и сохраняет их в определенной учетной записи хранения.

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
> * Имя ресурса имеет формат "родительский ресурс > ресурс/Чилд". В этом случае родительский ресурс является региональным экземпляром наблюдателя за сетями (формат: NetworkWatcher_<RegionName>. Пример: NetworkWatcher_centraluseuap)
> * targetResourceId — идентификатор ресурса целевого NSG
> * Сторажеид — идентификатор ресурса целевой учетной записи хранения.

**Пример 2**. следующие шаблоны, которые позволяют использовать журналы потоков NSG (версия 2) с периодом хранения в 5 дней. Включение Аналитика трафика с интервалом обработки, равным 10 минутам.

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
            "enabled": true,
        "workspaceResourceId": "91a3d1e9-698e-4a49-96dc-f6fc585ae888",
        "trafficAnalyticsInterval": 10
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 1
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Развертывание шаблона Azure Resource Manager

В этом учебнике предполагается, что у вас есть группа ресурсов и NSG, для которых можно включить ведение журнала потоков.
Любой из приведенных выше примеров шаблонов можно сохранить локально как `azuredeploy.json`. Обновите значения свойств, чтобы они указывали на действительные ресурсы в вашей подписке.

Чтобы развернуть шаблон, выполните следующую команду в PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Проверка развертывания

Существует несколько способов проверить, успешно ли прошло развертывание. Консоль PowerShell должна показывать "ProvisioningState" как "успех". Кроме того, для подтверждения изменений можно посетить [страницу портала журналов потоков NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) . Если возникли проблемы с развертыванием, ознакомьтесь со статьей [Устранение распространенных ошибок развертывания Azure с Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).


## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как визуализировать данные NSG Flow с помощью:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Средства с открытым исходным кодом](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Аналитика трафика Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
