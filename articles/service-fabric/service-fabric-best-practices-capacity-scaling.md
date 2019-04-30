---
title: Рекомендации по планированию ресурсов и масштабированию в Azure Service Fabric | Документация Майкрософт
description: Рекомендации по планированию и масштабированию кластеров и приложений Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 425154958e4c60902b56f320f714a011b9095830
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471542"
---
# <a name="capacity-planning-and-scaling"></a>Планирование ресурсов и масштабирование

Прежде чем создавать кластер Azure Service Fabric или масштабировать вычислительные ресурсы для размещения этого кластера, очень важно правильно оценить производительность. См. дополнительные сведения о [планировании загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Определившись с такими характеристиками, как тип узла и кластер, приготовьтесь к тому, что операции масштабирования в рабочей среде займут не менее часа независимо от количества добавляемых виртуальных машин.

## <a name="auto-scaling"></a>Автомасштабирование
Операции масштабирования следует выполнять путем развертывания шаблона ресурса Azure, так как это наилучший способ использовать [конфигурации ресурсов как код]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). Кроме того, автомасштабирование масштабируемого набора виртуальных машин приведет к неточному определению количества экземпляров масштабируемого набора виртуальных машин в шаблоне Resource Manager с контролем версий. А это, в свою очередь, увеличит риск непредвиденных операций масштабирования при последующих развертываниях. В целом, автомасштабирование следует использовать в следующих ситуациях:

* Если развертывание шаблонов Resource Manager с объявленной емкостью не подходит для вашего варианта использования.
  * Кроме масштабирования вручную, вы можете настроить [конвейер непрерывной интеграции и доставки в службах Azure DevOps с помощью проектов развертывания группы ресурсов Azure]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Этот конвейер обычно вызывается приложением логики, которое использует метрики производительности виртуальной машины, полученные через [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). Фактически это позволяет выполнять автомасштабирование на основе любой выбранной метрики с одновременной оптимизацией возможностей использования Azure Resource Manager.
* В один момент времени вам нужно выполнить горизонтальное масштабирование только одного узла масштабируемого набора виртуальных машин.
  * Для горизонтального масштабирования трех и более узлов одновременно следует [масштабировать кластер Service Fabric, добавив масштабируемый набор виртуальных машин](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out). Для свертывания и развертывания масштабируемых наборов гораздо безопаснее изменять количество узлов на единицу.
* Вам нужно использовать уровень надежности Silver или выше для кластера Service Fabric, а также уровень устойчивости Silver или выше для любого масштабируемого набора, для которого вы настраиваете правила автомасштабирования.
  * Минимальная емкость для правил автоматического масштабирования должна быть не менее пяти экземпляров виртуальных машин и не ниже минимального количества экземпляров для используемого уровня надежности для вашего узла основного типа.

> [!NOTE]
> Служба Azure Service Fabric с отслеживанием состояния fabric:/System/InfastructureService/<ИМЯ_ТИПА_УЗЛА> выполняется на всех типах узлов с уровнями устойчивости Silver или выше. Это единственная системная служба, выполнение которой в Azure поддерживается для любого из типов узлов кластеров. 

## <a name="vertical-scaling-considerations"></a>Рекомендации по вертикальному масштабированию

Для [вертикального масштабирования](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) типа узла в Azure Service Fabric требуется выполнить несколько шагов и удовлетворить некоторые требования. Пример.
* Масштабируемый кластер должен быть работоспособным. В противном случае вы еще больше дестабилизируете его работу.
* **Уровень устойчивости Silver или выше** для всех типов узлов кластера Service Fabric, в котором размещены службы с отслеживанием состояния.

> [!NOTE]
> Узел основного типа, на котором размещены системные службы Service Fabric с отслеживанием состояния, должен иметь уровень устойчивости Silver или выше. Когда вы включите уровень устойчивости Silver, все операции кластера (обновление, добавление, удаление узлов и т. д.) замедлятся, так как система обеспечивает безопасность данных за счет скорости операций.

Вертикальное масштабирование Масштабируемого набора виртуальных машин является необратимой операцией. Чтобы выполнить безопасную операцию вертикального масштабирования, масштабируйте кластер горизонтально, добавив новый Масштабируемый набор с нужным номером SKU и перенеся службы в этот набор с нужным номером SKU. Изменение номера SKU для ресурса Масштабируемого набора виртуальных машин является необратимой операцией, так как повторно создается образ всех узлов и удаляются все локально сохраненные состояния.

Кластер использует [свойства узла и ограничения на размещение](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) Service Fabric, чтобы выбрать место для размещения служб приложений. Выполняя вертикальное масштабирование для узла основного типа, объявите такие же значения свойств для `"nodeTypeRef"` (настраивается в расширении Service Fabric для Масштабируемого набора виртуальных машин). Следующий фрагмент кода из шаблона Resource Manager демонстрирует свойства, которые вам нужно объявить, с тем же значением для новых подготовленных масштабируемых наборов, до которых вы выполняете масштабирование, и поддерживается для кластера только с временным отслеживанием состояния:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Не оставляйте кластер работать с несколькими масштабируемыми наборами, для которых указано одинаковое значение `nodeTypeRef`, дольше чем требуется для выполнения успешной операции вертикального масштабирования.
> Всегда проверяйте операции в тестовых средах, прежде чем вносить любые изменения в рабочую среду. По умолчанию системные службы кластера Service Fabric имеют ограничение на размещение, позволяющее развертывать их только на узле основного типа.

Объявив свойства узла и ограничения на размещение, выполните следующие шаги поочередно для каждого экземпляра виртуальной машины. Это позволит корректно завершить работу системных служб (и служб с отслеживанием состояния) на удаляемом экземпляре виртуальной машины по мере создания новых реплик в других расположениях.
1. В PowerShell выполните `Disable-ServiceFabricNode` с намерением RemoveNode, чтобы отключить удаляемый узел. Удалите тип узла с наибольшим номером. Например, если ваш кластер содержит шесть узлов, удалите экземпляр виртуальной машины MyNodeType_5.
2. Выполните `Get-ServiceFabricNode` и убедитесь, что этот узел перешел в отключенное состояние. Если это не так, подождите, пока узел не будет отключен. Этот процесс для каждого узла может занять несколько часов. Не продолжайте процесс, пока узел не перейдет в отключенное состояние.
3. Уменьшите на единицу число виртуальных машин для соответствующего типа узла. Будет удален самый верхний экземпляр виртуальной машины.
4. При необходимости повторите шаги 1–3, но никогда не следует уменьшать число экземпляров в типах первичных узлов до значения меньше гарантируемого уровнем надежности. См. [рекомендации по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

## <a name="horizontal-scaling"></a>горизонтальное масштабирование;

Горизонтальное масштабирование в Service Fabric выполняется [вручную](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) или [программными средствами](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Для типов узлов с уровнем устойчивости Silver или Gold масштабирование будет происходить медленно.

### <a name="scaling-out"></a>Развертывание

Расширьте кластер Service Fabric, увеличив число экземпляров для конкретного масштабируемого набора виртуальных машин. Расширение можно выполнить программным способом, указав AzureClient и ID для того масштабируемого набора, размер которого вы намерены увеличить.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Чтобы расширить кластер вручную, измените значение емкости в свойстве SKU для ресурса нужного [масштабируемого набора виртуальных машин](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile).
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Уменьшение масштаба

Уменьшение масштаба нужно выполнять осторожнее, чем увеличение. Пример.
* Системные службы Service Fabric запускаются в кластере на узле основного типа. Никогда не уменьшайте число экземпляров узлов для этого типа ниже значения, которое гарантирует соответствующий уровень надежности. 
* Для службы с отслеживанием состояния определенное количество узлов должно постоянно работать, чтобы поддерживать доступность этой службы и сохранять ее состояние. Требуется как минимум количество узлов, равное количеству целевых наборов реплик секции или службы.

Чтобы уменьшить масштаб вручную, выполните следующие действия.

1. В PowerShell выполните `Disable-ServiceFabricNode` с намерением RemoveNode, чтобы отключить удаляемый узел. Удалите тип узла с наибольшим номером. Например, если ваш кластер содержит шесть узлов, удалите экземпляр виртуальной машины MyNodeType_5.
2. Выполните `Get-ServiceFabricNode` и убедитесь, что этот узел перешел в отключенное состояние. Если это не так, подождите, пока узел не будет отключен. Этот процесс для каждого узла может занять несколько часов. Не продолжайте процесс, пока узел не перейдет в отключенное состояние.
3. Уменьшите на единицу число виртуальных машин для соответствующего типа узла. Будет удален самый верхний экземпляр виртуальной машины.
4. При необходимости повторите шаги 1–3, но никогда не следует уменьшать число экземпляров в типах первичных узлов до значения меньше гарантируемого уровнем надежности. См. [рекомендации по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Чтобы уменьшить кластер вручную, измените значение емкости в свойстве SKU для нужного ресурса [масштабируемого набора виртуальных машин](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile).

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Повторяйте шаги с 1 по 3, пока вы не достигнете нужной емкости. Никогда не уменьшайте число экземпляров в узлах основного типа ниже значения, которое гарантирует соответствующий уровень надежности. Дополнительные сведения об уровнях надежности и требуемом количестве экземпляров см в [рекомендациям по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Чтобы уменьшить масштаб программным способом, необходимо подготовить узел к завершению работы. Этот процесс предусматривает поиск узла для удаления (узел с наивысшим номером) и его отключение. Пример.

```c#
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

Определив, какой узел будет удаляться, отключите и удалите его, указав тот же экземпляр `FabricClient` (в нашем примере это `client`) и имя экземпляра узла (в нашем примере это `instanceIdString`), которые вы использовали в коде выше:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> При масштабировании кластера работает, вы увидите удален узла или виртуальной Машины, отображаемый в неработоспособном состоянии в Service Fabric Explorer. Описание этого поведения, см. в разделе [поведения, вы можете заметить в обозревателе Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer).
> 
> Вы можете:
> * Вызовите [Remove ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) с именем соответствующего узла.
> * Развертывание [приложение service fabric автомасштабирования вспомогательный](https://github.com/Azure/service-fabric-autoscale-helper/) в кластере, что гарантирует масштабированное неработающих узлов будут удалены из Service Fabric Explorer.

## <a name="reliability-levels"></a>Уровни надежности

[Уровень надежности](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) является свойством ресурса кластера Service Fabric. Его нельзя настраивать по-разному для разных типов узлов. Он определяет коэффициент репликации для системных служб кластера и применяется на уровне ресурса кластера. Уровень надежности определяет минимальное количество узлов для узла основного типа. Уровень надежности может принимать следующие значения:
* Platinum — запуск системных служб с целевым количеством семь для набора реплик и девятью начальными узлами.
* Gold — запуск системных служб с целевым количеством семь для набора реплик и семью начальными узлами.
* Silver — запуск системных служб с целевым количеством пять для набора реплик и пятью начальными узлами.
* Bronze — запуск системных служб с целевым количеством три для набора реплик и тремя начальными узлами.

Мы рекомендуем использовать уровень надежности не ниже Silver.

Уровень надежности задается в разделе свойств для [ресурса Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), как показано ниже:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Уровни устойчивости

> [!WARNING]
> Типы узлов, выполняющиеся с устойчивостью Bronze, _не получают привилегий_. Это означает, что задания инфраструктуры, которые влияют на рабочие нагрузки без отслеживания состояния, не будут остановлены или отложены, что может повлиять на рабочие нагрузки. Используйте уровень устойчивости Bronze только для тех типов узлов, на которых выполняются рабочие нагрузки без учета состояния. Для рабочих нагрузок настройте уровень Silver или выше, чтобы обеспечить согласованность состояния. Выберите уровень надежности, следуя рекомендациям в [документации по планированию ресурсов](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Уровень устойчивости настраивается для двух ресурсов. Во-первых, в профиле расширения для [масштабируемого набора виртуальных машин](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

И, во-вторых, в `nodeTypes` для [ресурса Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
