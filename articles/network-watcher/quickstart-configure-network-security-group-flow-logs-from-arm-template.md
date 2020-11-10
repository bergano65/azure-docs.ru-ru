---
title: Краткое руководство. Настройка журналов потоков NSG с помощью шаблона Azure Resource Manager
description: Сведения о том, как включить журналы потоков NSG программным способом с помощью шаблона Azure Resource Manager (шаблон ARM) и Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042755"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Краткое руководство. Настройка журналов потоков NSG с помощью шаблона Resource Manager

Проходя это краткое руководство, вы включите [журналы потоков NSG](network-watcher-nsg-flow-logging-overview.md), используя шаблон [Azure Resource Manager](../azure-resource-manager/management/overview.md) (шаблон ARM) и Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Сначала мы рассмотрим свойства объекта журнала потоков NSG, а также несколько примеров шаблонов. Затем мы развернем шаблон с помощью локального экземпляра PowerShell.

Если среда соответствует предварительным требованиям и вы знакомы с использованием шаблонов ARM, нажмите кнопку **Развертывание в Azure**. Шаблон откроется на портале Azure.

[![Развертывание в Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Множественные ресурсы определены в шаблоне:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Объект журналов потоков NSG

Ниже показан объект журналов потоков NSG со всеми параметрами. Полный обзор свойств см. в статье [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Чтобы создать ресурс `Microsoft.Network/networkWatchers/flowLogs`, добавьте приведенный выше JSON в раздел ресурсов шаблона.

## <a name="creating-your-template"></a>Создание шаблона

Если вы используете шаблоны Resource Manager в первый раз, вы можете узнать о них больше, перейдя по указанным ниже ссылкам.

- [Развертывание ресурсов с помощью шаблонов ARM и Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- Создание и развертывание первого шаблона ARM[

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Ниже приведен пример полного шаблона — простейшая версия с минимальными параметрами, передаваемыми для настройки журналов потоков NSG. Дополнительные примеры см. в этом [пошаговом руководстве](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Пример**. Приведенный ниже шаблон включает журналы потоков NSG на целевой NSG и сохраняет их в определенной учетной записи хранения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - Имя ресурса имеет формат _родительский ресурс_дочерний ресурс_. Здесь родительский ресурс является региональным экземпляром Наблюдателя за сетями (формат: NetworkWatcher_RegionName. Пример NetworkWatcher_centraluseuap).
> - `targetResourceId` является идентификатором ресурса для целевой NSG.
> - `storageId` является идентификатором ресурса для целевой учетной записи хранения.

## <a name="deploy-the-template"></a>Развертывание шаблона

В этом руководстве предполагается, что у вас есть группа ресурсов и NSG, для которых можно включить ведение журнала потоков.
Любой из приведенных выше примеров шаблонов можно сохранить локально как `azuredeploy.json`. Обновите значения свойств, чтобы они указывали на действительные ресурсы в вашей подписке.

Чтобы развернуть шаблон, выполните следующую команду в PowerShell.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Приведенные выше команды развертывают ресурс в группе ресурсов NetworkWatcherRG, а не в группе ресурсов, содержащей NSG.

## <a name="validate-the-deployment"></a>Проверка развертывания

Существует несколько способов проверить, успешно ли выполнено развертывание. На консоли PowerShell для `ProvisioningState` должно отображаться значение `Succeeded`. Кроме того, вы можете посетить страницу [журналов потоков NSG на портале](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs), чтобы подтвердить изменения. Если возникнут проблемы с развертыванием, см. статью [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Azure позволяет удалять ресурсы с помощью режима развертывания `Complete`. Чтобы удалить ресурс журналов потоков, укажите развертывание в режиме `Complete`, не включая ресурс, который нужно удалить. См. сведения о [полном режиме развертывания](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Кроме того, вы можете отключить журнал потоков NSG из портала Azure, как описано ниже.

1. Войдите на портал Azure.
1. В верхнем левом углу портала выберите **Все службы**. В текстовом поле **Фильтр** введите _Наблюдатель за сетями_. Когда в результатах поиска появится **Наблюдатель за сетями** , выберите его.
1. В разделе **ЖУРНАЛЫ** выберите **Журналы потоков NSG**.
1. В списке групп безопасности сети выберите NSG, для которой необходимо отключить журналы потоков.
1. В разделе **Параметры журналов потоков** задайте для состояния журнала значение **Выкл.**
1. Прокрутите вниз и выберите **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы включили журналы потоков NSG. Теперь нужно узнать, как визуализировать данные потока NSG с помощью следующих средств:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Средства с открытым кодом](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Аналитика трафика Azure](traffic-analytics.md)
