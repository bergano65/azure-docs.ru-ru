---
title: Краткое руководство. Настройка журналов потоков NSG с помощью шаблона Azure Resource Manager
description: Сведения о том, как включить журналы потоков NSG программным способом с помощью шаблона Azure Resource Manager и Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986323"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Краткое руководство. Настройка журналов потоков NSG из шаблона ARM

В этом кратком руководстве показано, как включить [журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) программным способом с помощью шаблона [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) (шаблон ARM) и Azure PowerShell. 

Сначала мы рассмотрим свойства объекта журнала потоков NSG, а также несколько примеров шаблонов. Затем мы развернем шаблон с помощью локального экземпляра PowerShell.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="nsg-flow-logs-object"></a>Объект журналов потоков NSG

Ниже показан объект журналов потоков NSG со всеми параметрами.
Полный обзор свойств см. в [справочнике по шаблонам журналов потоков NSG](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

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
Чтобы создать ресурс Microsoft.Network/networkWatchers/flowLogs, добавьте приведенный выше код JSON в раздел ресурсов шаблона.


## <a name="creating-your-template"></a>Создание шаблона

Если вы используете шаблоны Azure Resource Manager в первый раз, вы можете узнать больше о них, перейдя по указанным ниже ссылкам.

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Руководство. Создание и развертывание первого шаблона Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Ниже приведен пример полного шаблона — простейшая версия с минимальными параметрами, передаваемыми для настройки журналов потоков NSG. Дополнительные примеры см. [здесь](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Пример**. Приведенный ниже шаблон включает журналы потоков NSG на целевом NSG и сохраняет их в определенной учетной записи хранения.

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
> * Имя ресурса имеет формат "родительский ресурс_дочерний ресурс". Здесь родительский ресурс является региональным экземпляром Наблюдателя за сетями (формат: NetworkWatcher_RegionName. Пример NetworkWatcher_centraluseuap).
> * targetResourceId — идентификатор ресурса целевой группы NSG.
> * storageId — это идентификатор ресурса целевой учетной записи хранения.


## <a name="deploying-your-azure-resource-manager-template"></a>Развертывание шаблона Azure Resource Manager

В этом руководстве предполагается, что у вас есть группа ресурсов и NSG, для которых можно включить ведение журнала потоков.
Любой из приведенных выше примеров шаблонов можно сохранить локально как `azuredeploy.json`. Обновите значения свойств, чтобы они указывали на действительные ресурсы в вашей подписке.

Чтобы развернуть шаблон, выполните следующую команду в PowerShell.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Приведенные выше команды развертывают ресурс в группе ресурсов NetworkWatcherRG, а не в группе ресурсов, содержащей NSG.


## <a name="validate-the-deployment"></a>Проверка развертывания

Существует несколько способов проверить, успешно ли выполнено развертывание. В консоли PowerShell для параметра ProvisioningState должно отобразиться значения Succeeded. Кроме того, вы можете посетить страницу [журналов потоков NSG на портале](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs), чтобы подтвердить изменения. Если возникнут проблемы с развертыванием, см. статью [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Удаление ресурса
Azure позволяет удалять ресурсы с помощью полного режима развертывания. Чтобы удалить ресурс журналов потоков, укажите развертывание в полном режиме, не включая ресурс, который нужно удалить. См. сведения о [полном режиме развертывания](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode).

Кроме того, вы можете отключить журнал потоков NSG из портала Azure, как описано ниже.
1. Войдите на портал Azure.
2. В верхнем левом углу портала выберите **Все службы**. В текстовом поле **Фильтр** введите *Наблюдатель за сетями*. Когда в результатах поиска появится **Наблюдатель за сетями**, выберите его.
3. В разделе **ЖУРНАЛЫ** выберите **Журналы потоков NSG**
4. В списке NSG выберите NSG, для которого необходимо отключить журналы потоков.
5. В разделе **Параметры журналов потока** задайте для состояния журнала значение **Выкл.**
6. Прокрутите вниз и выберите **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы включили журналы потоков NSG. Теперь нужно узнать, как визуализировать данные потока NSG с помощью следующих средств: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Средства с открытым кодом](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Аналитика трафика Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
