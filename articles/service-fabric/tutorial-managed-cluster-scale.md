---
title: Масштабирование управляемого кластера Service Fabric (предварительная версия)
description: В этом учебнике описано, как масштабировать тип узла управляемого кластера Service Fabric.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 01b299744d462496296884211eff08b7a9c64687
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316142"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Руководство по масштабированию управляемого кластера Service Fabric (предварительная версия)

В этой серии учебников мы обсудим следующие темы:

> [!div class="checklist"]
> * [Развертывание управляемого кластера Service Fabric.](tutorial-managed-cluster-deploy.md)
> * Масштабирование управляемого кластера Service Fabric.
> * [Добавление и удаление типов узлов в управляемом кластере Service Fabric](tutorial-managed-cluster-add-remove-node-type.md).
> * [Развертывание приложения в управляемом кластере Service Fabric](tutorial-managed-cluster-deploy-app.md).

В этой части серии учебников описывается:

> [!div class="checklist"]
> * Масштабирование узла управляемого кластера Service Fabric.

## <a name="prerequisites"></a>Предварительные требования

* [Управляемый кластер Service Fabric (см. статью *Развертывание управляемого кластера*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell версии 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) или более поздней (см. статью [*Установка Azure PowerShell*](/powershell/azure/install-az-ps?preserve-view=true&view=azps-4.7.0)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Масштабирование управляемого кластера Service Fabric
Измените число экземпляров, чтобы увеличить или уменьшить количество узлов в типе узла, который необходимо масштабировать. Имена типов узлов можно найти в шаблоне Azure Resource Manager (шаблон ARM) из развертывания кластера или в Service Fabric Explorer.  

> [!NOTE]
> Если тип узла является основным, вы не сможете использовать следующие 3 узла для кластера SKU "Базовый" и 5 узлов для кластера SKU "Стандартный".

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

Кластер начнет обновляться автоматически, и через несколько минут вы увидите дополнительные узлы.

## <a name="next-steps"></a>Дальнейшие действия

На этом шаге мы узнали, как масштабировать тип узла в управляемом кластере Service Fabric. Дополнительные сведения о добавлении и удалении типов узлов см. в статье

> [!div class="nextstepaction"]
> [Добавление и удаление типов узлов в управляемом кластере Service Fabric](tutorial-managed-cluster-add-remove-node-type.md).