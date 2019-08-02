---
title: Масштабирование кластера Service Fabric | Документация Майкрософт
description: Масштабировать Service Fabric кластер в или в соответствии с потребностями, задавая правила автомасштабирования для каждого типа узла или масштабируемого набора виртуальных машин. Добавление узлов в кластер Service Fabric
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2019
ms.author: atsenthi
ms.openlocfilehash: b1b3c0e6440212474bf356d4204c0dd91c1491fa
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599892"
---
# <a name="scale-a-cluster-in-or-out"></a>увеличение или уменьшение масштаба кластера;

> [!WARNING]
> Прочтите этот раздел перед тем, как масштабировать

Масштабирование вычислительных ресурсов источника рабочей нагрузки вашего приложения требует преднамеренного планирования. Для разворачивания рабочей среды почти всегда требуется больше часа, поэтому необходимо знать уровень рабочей нагрузки и ее бизнес-контекст. Если вы никогда раньше этого не делали, рекомендуется прочитать и разобраться с [рекомендациями по планированию загрузки кластера Service Fabric](service-fabric-cluster-capacity.md), прежде чем продолжить чтение оставшейся части этого документа. Эта рекомендация заключается в том, чтобы избежать непреднамеренных проблем с LiveSite, а также рекомендуется успешно протестировать операции, которые вы решите выполнить в нерабочей среде. В любое время вы можете [сообщить о производственных проблемах или запросить платную поддержку Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). В этой статье описываются операции масштабирования для инженеров, выделенных для выполнения этих операций, которые обладают соответствующим контекстом, но вы должны понять, какие операции подходят для вашего случая использования, и принять решение. Здесь речь идет о ресурсах для масштабирования (ЦП, хранилище, память), выборе направления масштабирования (вертикальное или горизонтальное) и операций для выполнения (шаблоны развертывания ресурсов, портал, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Масштабирование кластера Service Fabric с помощью правил автомасштабирования или вручную
Наборы масштабирования виртуальных машин относятся к вычислительным ресурсам Azure. Их можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла, определенный в кластере Service Fabric, настроен как отдельный набор масштабирования виртуальных машин. Каждый тип узла поддерживает возможность независимого масштабирования, имеет разные наборы открытых портов и собственные метрики емкости. Дополнительные сведения см. в документе [типы узлов Service Fabric](service-fabric-cluster-nodetypes.md) . Так как типы узлов Service Fabric в кластере состоят из масштабируемых наборов виртуальных машин в серверной части, необходимо настроить правила автомасштабирования для каждого типа узла или масштабируемого набора виртуальных машин.

> [!NOTE]
> Ваша подписка должна включать в себя достаточное количество ядер для добавления виртуальных машин IaaS, которые войдут в состав этого кластера. В настоящее время проверка модели не предусмотрена, поэтому в случае, если лимиты квоты будут нарушены, во время развертывания произойдет сбой. Также один тип узла не может превышать 100 узлов на VMSS. Возможно, придется добавить масштабируемый набор виртуальных машин для достижения целевого масштаба, но автоматическое масштабирование не может автоматически добавлять масштабируемый набор виртуальных машин. Добавление масштабируемых наборов виртуальных машин в динамический кластер является непростой задачей. Обычно это приводит к тому, что пользователи подготавливают новые кластеры, указывая нужные типы узлов при их создании, соответственно [планируя загрузку кластера](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Выбор типа узла или масштабируемого набора виртуальных машин для масштабирования
Сейчас настроить правила автомасштабирования для масштабируемых наборов виртуальных машин с помощью портала для создания кластера Service Fabric нельзя, поэтому мы используем Azure PowerShell (версии 1.0 или более поздней), чтобы получить список типов узлов и добавить к ним правила автомасштабирования.

Чтобы получить список масштабируемых наборов виртуальных машин в составе кластера, выполните следующие командлеты:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Задание правил автоматического масштабирования для типа узла или масштабируемого набора виртуальных машин
Если в кластере есть несколько типов узлов, повторите эти действия для каждого типа узла или масштабируемых наборов виртуальных машин, которые необходимо масштабировать (в или из них). Перед настройкой автомасштабирования обратите внимание на необходимое количество узлов. Минимальное количество узлов, необходимое для основного типа узлов, зависит от выбранного уровня надежности. См. дополнительные сведения об [уровнях надежности](service-fabric-cluster-capacity.md).

> [!NOTE]
> Уменьшение масштаба типа первичного узла до уровня ниже минимального приведет к нестабильности или сбою кластера. Это может вызвать потерю данных в приложениях или системных службах.
> 
> 

В настоящее время функция автомасштабирования не зависит от уровня нагрузки, который приложения сообщают Service Fabric. В связи с этим автомасштабирование регулируется исключительно счетчиками производительности, которые отправляются каждым экземпляром масштабируемого набора виртуальных машин.  

Выполните эти инструкции [, чтобы настроить автоматическое масштабирование для каждого масштабируемого набора виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> В сценарии уменьшения масштаба, если тип узла не имеет [уровня устойчивости][durability] золотого или серебристого, необходимо вызвать [командлет Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) с соответствующим именем узла. Для устойчивости к бронзовой не рекомендуется изменять масштаб более чем одного узла за раз.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Добавление виртуальных машин в тип узла или масштабируемый набор виртуальных машин вручную

При развертывании вы добавляете дополнительные экземпляры виртуальных машин в масштабируемый набор. Эти экземпляры становятся узлами, которые использует Service Fabric. Службе Service Fabric известно, когда в масштабируемый набор добавляются экземпляры (путем развертывания), и она реагирует автоматически. 

> [!NOTE]
> Добавление виртуальных машин занимает некоторое время, поэтому не следует ждать мгновенного добавления. Таким образом, необходимо заранее добавить емкость, чтобы в течение 10 минут можно было получить доступ к виртуальной машине для размещения реплик или экземпляров службы.
> 

### <a name="add-vms-using-a-template"></a>Добавление виртуальных машин с помощью шаблона
Следуйте указаниям в [коллекции шаблонов](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) быстрого запуска, чтобы изменить число виртуальных машин в каждом типе узла. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Добавление виртуальных машин с помощью PowerShell или команд интерфейса командной строки
Следующий код получает масштабируемый набор по имени и увеличивает значение **емкости** масштабируемого набора на 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Этот код задает значение 6 для емкости.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Удаление виртуальных машин из типа узла или масштабируемого набора виртуальных машин вручную
При масштабировании в типе узла удаляются экземпляры виртуальных машин из масштабируемого набора. Если тип узла является бронзовым уровнем устойчивости, Service Fabric не знает, что произошло, и сообщает о том, что узел отсутствовал. Затем Service Fabric сообщает о неработоспособном состоянии кластера. Чтобы предотвратить это неправильное состояние, необходимо явным образом удалить узел из кластера и удалить состояние узла.

Системные службы Service Fabric выполняются в типе первичного узла в кластере. При масштабировании типа первичного узла никогда не следует уменьшать количество экземпляров до меньшего [уровня надежности](service-fabric-cluster-capacity.md) . 
 
Для службы с отслеживанием состояния необходимо определенное количество узлов, которые постоянно доступны и работают, чтобы поддерживать доступность этой службы и сохранять ее состояние. Требуется как минимум количество узлов, равное количеству целевых наборов реплик секции или службы.

### <a name="remove-the-service-fabric-node"></a>Удаление узла Service Fabric

Действия по удалению состояния узла вручную применяются только к типам узлов с уровнем устойчивости бронзовой.  Для уровня устойчивости " *серебро* " и " *Gold* " эти действия выполняются автоматически платформой. Дополнительные сведения о устойчивости см. в статье [Service Fabric планирование емкости кластера][durability].

Чтобы обеспечить равномерное распределение узлов кластера между доменами сбоя и обновления, включив тем самым их сбалансированное использование, необходимо сначала удалить последний созданный узел. Другими словами, необходимо удалить узлы в порядке, обратном порядку их создания. Последний созданный узел — это узел с максимальным значением свойства `virtual machine scale set InstanceId`. В примерах кода ниже возвращается последний созданный узел.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Кластеру Service Fabric необходимо сообщить, что этот узел будет удален. Для этого нужно выполнить три шага:

1. Отключить узел, чтобы он больше не использовался для репликации данных.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Остановить узел, после чего работа среды выполнения Service Fabric будет полностью завершена и ваше приложение получит запрос на завершение.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Удалить узел из кластера.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

После применения этих шагов узел можно удалить из масштабируемого набора. Если вы используете любой уровень устойчивости, помимо [бронзовой][durability], эти действия выполняются при удалении экземпляра масштабируемого набора.

Следующий блок кода получает последний созданный узел, отключает, останавливает и удаляет его из кластера.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

В коде **sfctl** ниже следующая команда используется для получения значений **имени узла** последнего созданного узла: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Используйте следующие запросы **sfctl**, чтобы проверить состояние каждого шага.
>
> **Проверка состояния деактивации**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Проверка состояния остановки**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Свертывание масштабируемого набора

Теперь, когда узел Service Fabric удален из кластера, можно свернуть масштабируемый набор виртуальных машин. В приведенном ниже примере емкость масштабируемого набора уменьшается на 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Этот код задает значение 5 для емкости.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Возможные варианты поведения в обозревателе Service Fabric
При вертикальном масштабировании кластера в Service Fabric Explorer отражается число узлов (экземпляров масштабируемых наборов виртуальных машин), которые входят в кластер.  Тем не менее при уменьшении масштаба кластера удаленный узел или экземпляр виртуальной машины будет отображаться как неработоспособный, если не вызвать [командлет Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) с именем соответствующего узла.   

Вот как объясняется это поведение.

Узлы, перечисленные в Service Fabric Explorer, отражают то, что системным службам Service Fabric (в частности FM) известно о числе узлов в кластере. При уменьшении масштабируемого набора виртуальных машин виртуальная машина удаляется, но системная служба диспетчера отработки отказов по-прежнему считает, что узел (который был сопоставлен с удаленной виртуальной машиной) вернется. В связи с этим Service Fabric Explorer продолжает отображать этот узел (хотя состояние работоспособности может быть ошибочным или неизвестным).

Проверить удаление узла при удалении виртуальной машины можно двумя способами:

1. Выбрать уровень надежности Gold или Silver для типов узлов в кластере — это обеспечит интеграцию инфраструктуры. При уменьшении масштаба узлы будут удаляться из состояния системных служб (FM) автоматически.
Дополнительные сведения об уровнях надежности см. [здесь](service-fabric-cluster-capacity.md).

2. После уменьшения масштаба экземпляра виртуальной машины необходимо вызвать [командлет Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Для постоянной работы кластеров Service Fabric требуется определенное количество узлов, чтобы все время поддерживать доступность и сохранять состояние, которое называется "поддержание кворума". Поэтому обычно не рекомендуется завершать работу всех компьютеров в кластере, пока не будет выполнена [полная архивация состояния](service-fabric-reliable-services-backup-restore.md), так как это может быть небезопасно.
> 
> 

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о планировании емкости кластера, обновлении кластера и секционировании служб см. в следующих статьях:

* [Планирование емкости кластера](service-fabric-cluster-capacity.md)
* [Обновления кластера](service-fabric-cluster-upgrade.md)
* [Секционирование служб с отслеживанием состояния для максимального масштабирования](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
