---
title: Удаление типа узла в Azure Service Fabric | Документация Майкрософт
description: Узнайте, как удалить тип узла из кластера Service Fabric, работающего в Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 193a24aebff8f7de60752e53bbc1b18dd5c54f33
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051773"
---
# <a name="remove-a-service-fabric-node-type"></a>Удаление типа узла Service Fabric
В этой статье описывается, как масштабировать кластер Azure Service Fabric путем удаления существующего типа узла из кластера. Кластер Service Fabric — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Компьютер или виртуальная машина, которая входит в состав кластера. Масштабируемые наборы виртуальных машин относятся к вычислительным ресурсам Azure. Их можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла, определенный в кластере Azure, [настроен как отдельный масштабируемый набор](service-fabric-cluster-nodetypes.md). Затем каждым типом узла можно управлять отдельно. После создания кластера Service Fabric вы можете масштабировать кластер по горизонтали, удалив тип узла (масштабируемый набор виртуальных машин) и все его узлы.  Кластер можно масштабировать в любое время, даже когда в нем выполняются рабочие нагрузки.  Вместе с кластером автоматически масштабируются ваши приложения.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Используйте [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) удаляемого типа узла Service Fabric.

Три операции, которые возникают при вызове Remove-AzServiceFabricNodeType являются:
1.  Удаляется масштабируемый набор виртуальных машин, связанный с этим типом узла.
2.  Удаляется тип узла с кластера.
3.  Общее состояние всех узлов этого типа удаляется из системы. Если на таком узле есть службы, тогда службы сначала перемещаются на другой узел. Если диспетчеру кластеров не удается найти узел для реплики или службы, тогда операция откладывается или блокируется.

> [!WARNING]
> С помощью Remove-AzServiceFabricNodeType удалить тип узла из рабочего кластера не рекомендуется для использования на регулярной основе. Это опасная команда, так как она удаляет ресурс масштабируемого набора виртуальных машин, связанный с типом узла. 

## <a name="durability-characteristics"></a>Характеристики устойчивости
Безопасность имеет приоритет перед скорость, при использовании Remove AzServiceFabricNodeType. Тип узла должен иметь [уровень устойчивости](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Silver или Gold по следующим причинам:
- Уровень Bronze не дает никаких гарантий в отношении сохранении сведений о состоянии.
- В рамках устойчивости уровня Silver и Gold перехватываются любые изменения в масштабируемом наборе.
- Уровень Gold также позволяет контролировать обновления Azure для нижестоящего масштабируемого набора.

Service Fabric "координирует" базовые изменения и обновления, поэтому данные не теряются. Тем не менее при удалении узла с устойчивостью уровня Bronze вы можете потерять сведения о состоянии. Если вы удаляете тип первичного узла и используете приложение без отслеживания состояния, уровень Bronze приемлем. При выполнении рабочих нагрузок с отслеживанием состояния в рабочей среде минимальной конфигурацией должен быть уровень Silver. Аналогичным образом для рабочих сценариев тип первичного узла всегда должен быть уровня Silver или Gold.

### <a name="more-about-bronze-durability"></a>Сведения об устойчивости Bronze

При удалении типа узла Bronze все узлы этого типа сразу же выходят из строя. Service Fabric не перехватывает обновления масштабируемого набора для узлов Bronze, поэтому все виртуальные машины сразу же выходят из строя. Если у вас есть какой-либо объект с отслеживанием состояния на этих узлах, данные будут потеряны. Теперь, даже если вы использовали объекты без отслеживания состояния, все узлы в Service Fabric входят в кольцевую топологию, поэтому все окружение может быть потеряно, что может нарушить работу самого кластера.

## <a name="recommended-node-type-removal-process"></a>Рекомендуемый процесс удаления типа узла

Чтобы удалить тип узла, выполните [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) командлета.  Запуск командлета занимает некоторое время.  Затем запустите команду [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) на всех узлах, которые нужно удалить.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о [характеристиках устойчивости](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) кластера.
- Дополнительные сведения о [типах узлов и масштабируемых наборах виртуальных машин](service-fabric-cluster-nodetypes.md).
- Дополнительные сведения о [масштабировании кластеров Service Fabric](service-fabric-cluster-scaling.md).
