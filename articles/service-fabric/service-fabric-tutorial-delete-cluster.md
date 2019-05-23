---
title: Удаление кластера Service Fabric в Azure | Документация Майкрософт
description: Из этого руководства вы узнаете, как удалить размещенный в Azure кластер Service Fabric и все его ресурсы. Можно удалить группу ресурсов, содержащую кластер, или выборочно удалить его ресурсы.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 0e5137a8183f378ee5960846e281222c6ecaaa47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158075"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Руководство по Удаление кластера Service Fabric, работающего в Azure

В этом руководстве из серии из пяти руководств показано, как удалить кластер Service Fabric, выполняющийся в Azure. Чтобы полностью удалить кластер Service Fabric, нужно также удалить и все ресурсы, которые он использует. Вы можете сделать это одним из двух способов: удалить группу ресурсов, в которой находится кластер (при этом будет удален кластерный ресурс и другие ресурсы в группе ресурсов), или удалить ресурс кластера и связанные с ним ресурсы по отдельности (но не другие ресурсы в группе ресурсов).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Удаление группы ресурсов со всеми ее ресурсами.
> * Выборочное удаление ресурсов из группы ресурсов.

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание безопасного [кластера Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) в Azure с помощью шаблона;
> * [мониторинг кластера](service-fabric-tutorial-monitor-cluster.md);
> * [свертывание и развертывание кластера](service-fabric-tutorial-scale-cluster.md);
> * [Обновление среды выполнения кластера](service-fabric-tutorial-upgrade-cluster.md)
> * Удаление кластера


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Установите [Azure PowerShell](https://docs.microsoft.com/powershell/azure//install-Az-ps) или [Azure CLI](/cli/azure/install-azure-cli).
* Создание защищенного [кластера Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) в Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Удаление группы ресурсов, содержащей кластер Service Fabric
Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов.

Войдите в Azure и выберите идентификатор подписки, в которой вы хотите удалить кластер.  Идентификатор подписки можно узнать, войдя на [портал Azure](https://portal.azure.com). Удалите группу ресурсов и все кластерные ресурсы с помощью командлета [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) или команды [az group delete](/cli/azure/group?view=azure-cli-latest).

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Выборочное удаление кластерного ресурса и связанных ресурсов
Если в группе ресурсов содержатся только ресурсы, связанные с кластером Service Fabric, который требуется удалить, проще всего будет удалить группу ресурсов целиком. Если требуется выборочно удалить ресурсы в группе ресурсов и сохранить ресурсы, не связанные с кластером, выполните следующие действия.

Отобразите список ресурсов в группе ресурсов.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Для каждого ресурса, который требуется удалить, выполните следующий скрипт:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Чтобы удалить ресурс кластера, выполните следующий скрипт:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Удаление группы ресурсов со всеми ее ресурсами.
> * Выборочное удаление ресурсов из группы ресурсов.

Теперь, завершив изучение данного руководства, попробуйте следующее.
* Узнайте, как проверять кластер Service Fabric и управлять им с помощью [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
* Узнайте, как [установить исправления операционной системы Windows](service-fabric-patch-orchestration-application.md) на узлах кластера.
* Узнайте, как агрегировать и собирать события [кластеров Windows](service-fabric-diagnostics-event-aggregation-wad.md) и выполнять [настройку Log Analytics](service-fabric-diagnostics-oms-setup.md) для мониторинга событий кластера.
