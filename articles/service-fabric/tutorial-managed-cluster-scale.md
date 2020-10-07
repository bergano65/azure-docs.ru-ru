---
title: Масштабирование управляемого кластера Service Fabric (предварительная версия)
description: В этом учебнике описано, как масштабировать тип узла управляемого кластера Service Fabric.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: a6777743932f3cc73db973c4d9935d4b5827700b
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410240"
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
* [Azure PowerShell версии 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) или более поздней (см. статью [*Установка Azure PowerShell*](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.7.0&preserve-view=true)).

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
