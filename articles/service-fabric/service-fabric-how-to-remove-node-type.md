---
title: Как удалить тип узла в Azure Service Fabric | Документация Майкрософт
description: Узнайте, как удалить тип узла в Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53981890"
---
# <a name="remove-a-service-fabric-node-type"></a>Удаление типа узла Service Fabric

Тип узла Service Fabric можно удалить с помощью команды [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype).

При вызове Remove-AzureRmServiceFabricNodeType происходят такие две операции:
1.  Удаляется масштабируемый набор виртуальных машин (VMSS), связанный с этим типом узла.
2.  Общее состояние всех узлов этого типа удаляется из системы. Если на таком узле есть службы, тогда службы сначала перемещаются на другой узел. Если диспетчеру кластеров не удается найти узел для реплики или службы, тогда операция откладывается или блокируется.

> [!NOTE]
> Использовать команду Remove-AzureRmServiceFabricNodeType для удаления типа узла из рабочего кластера на регулярной основе не рекомендуется. В этом случае это крайне опасная команда, так как она фактически удаляет ресурс масштабируемого набора виртуальных машин, связанный с типом узла. Когда вы вызываете команду Remove-AzureRmServiceFabricNodeType, система не может определить, нужно ли вам безопасное удаление. 

## <a name="durability-characteristics"></a>Характеристики устойчивости
Безопасность считается более важной, чем скорость при использовании команды Remove-AzureRmServiceFabricNodeType. Рекомендуются [характеристики устойчивости](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) уровня Silver или Gold. Это связано с тем, что:
- Уровень Bronze не дает никаких гарантий в отношении сохранении сведений о состоянии.
- В рамках устойчивости уровня Silver и Gold перехватываются любые изменения в VMSS.
- Уровень Gold также позволяет контролировать обновления Azure для нижестоящих элементов VMSS.

Service Fabric "координирует" базовые изменения и обновления, поэтому данные не теряются. Тем не менее при удалении узла с устойчивостью уровня Bronze вы можете потерять сведения о состоянии. Если вы удаляете тип первичного узла и используете приложение без отслеживания состояния, уровень Bronze приемлем. При выполнении рабочих нагрузок с отслеживанием состояния в рабочей среде минимальной конфигурацией должен быть уровень Silver. Аналогичным образом для рабочих сценариев тип первичного узла всегда должен быть уровня Silver или Gold.

### <a name="more-about-bronze-durability"></a>Сведения об устойчивости Bronze

При удалении типа узла Bronze все узлы этого типа сразу же выходят из строя. Service Fabric не перехватывает обновления VMSS для узлов Bronze, поэтому все виртуальные машины сразу же выходят из строя. Если у вас есть какой-либо объект с отслеживанием состояния на этих узлах, данные будут потеряны. Теперь, даже если вы использовали объекты без отслеживания состояния, все узлы в Service Fabric входят в кольцевую топологию, поэтому все окружение может быть потеряно, что может повлиять на сам кластер.

В отличие от удаления одного узла (в теории вы можете удалить один узел за раз), перед удалением следующего узла нужно подождать, пока реплики и службы будут перемещены, а система стабилизируется.  Тем не менее если одновременно удалить несколько узлов, кластер может выйти из строя (так как Service Fabric не перехватывает все обновления VMSS с устойчивостью Bronze).

## <a name="recommended-node-type-removal-process"></a>Рекомендуемый процесс удаления типа узла

Чтобы удалить тип узла наиболее надежным и быстрым способом:
1.  Если вы используете устойчивость Bronze или не хотите, чтобы система перемещала приложения, содержащие сведения о состоянии, которые будут утрачены как часть процесса удаления типа узла, сначала очистите данные об отслеживании состояния с узлов, которые будут затронуты при удалении этого типа узла.
2.  Запустите команду [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) на всех узлах, которые нужно удалить.
3.  Запустите команду [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) для виртуальных машин, которые будут затронуты при удалении этого типа узла.
4. Запустите команду [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) для удаления типа узла.

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о [характеристиках устойчивости](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) кластера.
- Дополнительные сведения о [типах узлов и масштабируемых наборах виртуальных машин](service-fabric-cluster-nodetypes.md).
- Дополнительные сведения о [масштабировании кластеров Service Fabric](service-fabric-cluster-scaling.md).