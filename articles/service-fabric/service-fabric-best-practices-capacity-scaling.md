---
title: Планирование емкости и масштабирование для системы обслуживания Azure
description: Рекомендации по планированию и масштабированию кластеров и приложений Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258997"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Планирование емкости и масштабирование для системы обслуживания Azure

Перед созданием кластера Azure Service Fabric или масштабирования вычислительных ресурсов, хостатовк и хозяемых кластером, важно планировать емкость. См. дополнительные сведения о [планировании загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Дополнительные рекомендации по наилучшей практике [Service Fabric scalability considerations](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)для масштабируемости кластеров см.

Помимо учета характеристик типа узлов и кластера, следует ожидать, что операции масштабирования для производственной среды займут более часа. Это соображение верно независимо от количества добавленных vMs.

## <a name="autoscaling"></a>Автомасштабирование
Операции масштабирования следует выполнять с помощью шаблонов Azure Resource Manager, поскольку оптимальная практика — рассматривать [конфигурации ресурсов как код.]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) 

Использование автоматического масштабирования через виртуальные наборы масштабов машин сделает ваш шаблон управления ресурсами версии неточно определить ваш экземпляр рассчитывает для виртуальных наборов масштаба машины. Неточное определение увеличивает риск того, что будущие развертывания вызовут непреднамеренные операции масштабирования. В общем случае следует использовать автомасштабирование, если:

* Если развертывание шаблонов Resource Manager с объявленной емкостью не подходит для вашего варианта использования.
     
   В дополнение к ручному масштабированию можно настроить [непрерывную интеграцию и конвейер доставки в службах Azure DevOps с помощью проектов развертывания группы ресурсов Azure.](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts) Этот конвейер обычно вызывается логическим приложением, которое использует показатели производительности виртуальных машин, запрошенные из [API Rest Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) Конвейер эффективно автоматизируется на основе любых показателей, которые вы хотите, в то время как оптимизация для шаблонов менеджера ресурсов.
* Вам нужно горизонтально масштабировать только один виртуальный узла шкалы масштаба за один раз.
   
   Чтобы масштабироваться на три или более узлов одновременно, следует [масштабировать кластер Service Fabric, добавляя виртуальный набор масштабов машины.](virtual-machine-scale-set-scale-node-type-scale-out.md) Безопаснее всего масштабировать и масштабировать виртуальные наборы машин по горизонтали, один узло за раз.
* У вас есть серебряная надежность или выше для кластера Service Fabric, и прочность Silver или выше в любом масштабе, где вы настраиваете правила автоматического масштабирования.
  
   Минимальная емкость для правил автоматического масштабирования должна быть равна или больше, чем пять виртуальных экземпляров машины. Он также должен быть равен или больше, чем ваш минимум уровня надежности для вашего основного типа узла.

> [!NOTE]
> Ткань обслуживания Service Fabric stateful:/System/InfastructureService/<NODE_TYPE_NAME> работает на каждом типе узла, который имеет серебряную или более высокую прочность. Это единственная системная служба, которая поддерживается для работы в Azure на любом из типов узлов кластеров.

## <a name="vertical-scaling-considerations"></a>Рекомендации по вертикальному масштабированию

[Вертикальное масштабирование](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) типа узлов в функции службы Azure требует ряда шагов и соображений. Пример:

* Масштабируемый кластер должен быть работоспособным. В противном случае, вы будете дестабилизировать кластер дальше.
* Серебряный уровень прочности или больше требуется для всех типов кластерных узлов Service Fabric, которые размещали государственные службы.

> [!NOTE]
> Ваш основной тип узла, в котором размещаются службы системы service Fabric, должен быть уровень прочности серебра или больше. После включения Silver долговечность, кластерные операции, такие как обновления, добавление или удаление узлов и так далее, будут медленнее, потому что система оптимизирует безопасность данных по скорости операций.

Вертикальное масштабирование виртуального набора масштабов машины является разрушительной операцией. Вместо этого горизонтально масштабируйте кластер, добавляя новый набор масштабов с желаемым SKU. Затем перемините свои услуги в нужный SKU, чтобы завершить безопасную операцию вертикального масштабирования. Изменение ресурса набора виртуальной шкалы машины SKU является разрушительной операцией, поскольку он переизменяет узлы, которая удаляет все локально упорнеее состояние.

Кластер использует свойства узлов Service Fabric [и ограничения на размещение,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) чтобы решить, где размещать службы приложения. При вертикальном масштабировании типа основного узла объявляйте `"nodeTypeRef"`одинаковые значения свойств для. Вы можете найти эти значения в расширении Service Fabric для виртуальных наборов масштабов машин. 

Следующий фрагмент шаблона менеджера ресурсов показывает свойства, которые вы объявите. Он имеет такое же значение для новых наборов масштабов, которые вы масштабируете, и поддерживается только как временная служба состояния для вашего кластера.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Не оставляйте кластер в работе с несколькими `nodeTypeRef` наборами масштабов, которые используют одно и то же значение свойства дольше, чем требуется для завершения успешной операции вертикального масштабирования.
>
> Всегда проверяйте операции в тестовых средах перед попыткой внесения изменений в производственную среду. По умолчанию службы кластерных систем Service Fabric имеют ограничение размещения только для целевого типа основного узла.

Объявив свойства узла и ограничения на размещение, выполните следующие шаги поочередно для каждого экземпляра виртуальной машины. Это позволяет системным службам (и вашим службам состояния) быть изящно отключены на экземпляре VM, который вы удаляете по мере создания новых реплик в другом месте.

1. От PowerShell `Disable-ServiceFabricNode` запустите `RemoveNode` с намерением отключить узла, который вы собираетесь удалить. Удалите тип узла с наибольшим номером. Например, если у вас есть кластер из шести узлов, удалите экземпляр виртуальной машины "MyNodeType_5".
2. Выполните `Get-ServiceFabricNode` и убедитесь, что этот узел перешел в отключенное состояние. Если это не так, подождите, пока узел не будет отключен. Это может занять пару часов для каждого узла. Не продолжайте процесс, пока узел не перейдет в отключенное состояние.
3. Уменьшите количество VMs на один в этом типе узла. Будет удален самый верхний экземпляр виртуальной машины.
4. При необходимости повторите шаги 1–3, но никогда не следует уменьшать число экземпляров в типах первичных узлов до значения меньше гарантируемого уровнем надежности. См. [рекомендации по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).
5. Как только все ВМ исчезли (представлены как "Down") ткань: /System/InfrastructureService/'имя узла» покажет состояние ошибки. Затем можно обновить ресурс кластера, чтобы удалить тип узла. Можно либо использовать развертывание шаблона ARM, либо отсваивать ресурс кластера через [диспетчер ресурсов Azure.](https://resources.azure.com) Это позволит запустить обновление кластера, которое удалит ткань:/System/InfrastructureService/'node type», которая находится в состоянии ошибки.
 6. После этого вы можете дополнительно удалить VMScaleSet, вы все равно увидите узлы, как "Вниз" из представления Service Fabric Explorer, хотя. Последним шагом было бы очистить `Remove-ServiceFabricNodeState` их с командой.

## <a name="horizontal-scaling"></a>горизонтальное масштабирование;

Вы можете сделать горизонтальное масштабирование [вручную](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) или [программно.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)

> [!NOTE]
> Если вы масштабируете тип узла, который имеет прочность серебряного или золотого золота, масштабирование будет медленным.

### <a name="scaling-out"></a>Развертывание

Масштабируйте кластер Service Fabric, увеличивая количество экземпляров для определенного набора виртуальной шкалы машин. Вы можете масштабировать программно, `AzureClient` используя иидентификатор для желаемого набора масштаба для увеличения емкости.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Чтобы масштабировать вручную, обновите емкость в свойстве SKU желаемого ресурса [набора виртуальной шкалы машины.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Уменьшение масштаба

Масштабирование требует большего внимания, чем масштабирование. Например:

* Службы системы обслуживания Fabric работают в основном типе узлов в кластере. Никогда не уменьшайте число экземпляров узлов для этого типа ниже значения, которое гарантирует соответствующий уровень надежности. 
* Для службы с состоянием требуется определенное количество узлов, которые всегда готовы поддерживать доступность и сохранять состояние вашего сервиса. Как минимум, необходимо количество узлов, равных целевому набору реплик и количества разделов или службы.

Чтобы уменьшить масштаб вручную, выполните следующие действия.

1. От PowerShell `Disable-ServiceFabricNode` запустите `RemoveNode` с намерением отключить узла, который вы собираетесь удалить. Удалите тип узла с наибольшим номером. Например, если у вас есть кластер из шести узлов, удалите экземпляр виртуальной машины "MyNodeType_5".
2. Выполните `Get-ServiceFabricNode` и убедитесь, что этот узел перешел в отключенное состояние. Если это не так, подождите, пока узел не будет отключен. Это может занять пару часов для каждого узла. Не продолжайте процесс, пока узел не перейдет в отключенное состояние.
3. Уменьшите количество VMs на один в этом типе узла. Будет удален самый верхний экземпляр виртуальной машины.
4. Повторите шаги 1 к 3 по мере необходимости, пока вы не предоставите емкость, которая вам нужна. Никогда не уменьшайте число экземпляров в узлах основного типа ниже значения, которое гарантирует соответствующий уровень надежности. См. [рекомендации по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Для масштабирования в ручном режиме, обновить емкость в свойстве SKU желаемого [виртуального набора шкалы машины](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) ресурса.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Необходимо подготовить узла к завершению масштабирования в программном порядке. Найдите узла для удаления (узла высшей инстанции). Пример:

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Отключите и удалите узла, используя тот же `FabricClient` экземпляр (в`client` данном случае) и экземпляр узла (в`instanceIdString` данном случае), который вы использовали в предыдущем коде:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> При уменьшении кластера отображается удаленный узла/VM экземпляр, отображаемый в неработоспособном состоянии в Service Fabric Explorer. Для объяснения такого поведения [см.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer) Вы можете:
> * Позвоните [в команду Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) с соответствующим именем узла.
> * Развертывание [приложения автоматического помощника Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) в вашем кластере. Это приложение гарантирует, что уменьшенные узлы будут удалены от Service Fabric Explorer.

## <a name="reliability-levels"></a>Уровни надежности

[Уровень надежности](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) является свойством ресурса кластера Service Fabric. Он не может быть настроен по-разному для отдельных типов узлов. Он определяет коэффициент репликации для системных служб кластера и применяется на уровне ресурса кластера. 

Уровень надежности определяет минимальное количество узлов для узла основного типа. Уровень надежности может принимать следующие значения:

* Платина: Запускает системные службы с целевым набором реплики, насчитываемым в семь и девять узла семян.
* Золото: Запускает системные службы с целевым набором реплики, насчитываемым семью и семью узлами семян.
* Серебро: Запускает системные службы с целевым набором реплики, насчитываемым пятью и пятью узлами семян.
* Бронза: Запускает системные службы с набором целевых реплик из трех и трех семенных узлов.

Мы рекомендуем использовать уровень надежности не ниже Silver.

Уровень надежности задается в разделе свойств для [ресурса Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), как показано ниже:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Уровни устойчивости

> [!WARNING]
> Типы узлов, выполняющиеся с устойчивостью Bronze, _не получают привилегий_. Задания инфраструктуры, влияющие на рабочие нагрузки без состояния, не будут остановлены или задержаны, что может повлиять на рабочие нагрузки. 
>
> Используйте уровень устойчивости Bronze только для тех типов узлов, на которых выполняются рабочие нагрузки без учета состояния. Для производственных нагрузок, запустить серебро или выше, чтобы обеспечить согласованность состояния. Выберите уровень надежности, следуя рекомендациям в [документации по планированию ресурсов](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Уровень устойчивости настраивается для двух ресурсов. Одним из них является расширение профиля [виртуального набора шкалы машины ресурса:](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Другой ресурс находится `nodeTypes` в [Microsoft.ServiceFabric /кластеров ресурса:](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на вс-минах или компьютерах под управлением Windows Server: [создание кластера Service Fabric для Windows Server.](service-fabric-cluster-creation-for-windows-server.md)
* Создайте кластер на вс-минах или компьютерах под управлением Linux: [Создайте кластер Linux.](service-fabric-cluster-creation-via-portal.md)
* Узнайте о [вариантах поддержки Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
