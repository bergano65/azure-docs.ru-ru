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
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: c72392e46805049703300dd6f60fc7bf08b9053b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235785"
---
# <a name="capacity-planning-and-scaling"></a>Планирование ресурсов и масштабирование

Прежде чем создавать кластер Azure Service Fabric или масштабировать вычислительные ресурсы для размещения этого кластера, очень важно правильно оценить производительность. См. дополнительные сведения о [планировании загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Дополнительные рекомендации по обеспечению масштабируемости кластера см. в разделе [вопросы масштабируемости в Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)

Помимо рассмотрения характеристики типа и кластера узла, необходимо запланировать операций занимает больше часа для выполнения в рабочей среде, независимо от количества виртуальных машин, вы добавляете масштабирования.

## <a name="auto-scaling"></a>Автомасштабирование
Операций масштабирования следует выполнять с помощью развертывания шаблона ресурсов Azure, так как это лучший способ обрабатывать [конфигурации ресурсов как код]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)и с помощью масштабируемого набора виртуальных машин, автоматическое масштабирование приведет к вашей с версиями шаблон Resource Manager, ошибочно определение масштабируемого набора, количество экземпляров; повышает риск будущих развертываний, вызывая непредусмотренных операций масштабирования и в целом следует использовать автоматическое масштабирование, если:

* Если развертывание шаблонов Resource Manager с объявленной емкостью не подходит для вашего варианта использования.
  * Кроме ручного масштабирования можно настроить [непрерывной интеграции и конвейера доставки в службах Azure DevOps, с помощью проектов развертывания группы ресурсов Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), который обычно вызывается приложение логики, которое использует метрики производительности виртуальной машины, оттуда [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough); фактически автоматическое масштабирование независимо от метрик необходимо, во время оптимизации значение Добавление Azure Resource Manager.
* В один момент времени вам нужно выполнить горизонтальное масштабирование только одного узла масштабируемого набора виртуальных машин.
  * Для горизонтального масштабирования с 3 или более узлов одновременно, следует [масштабирования кластера Service Fabric путем добавления набора масштабирования виртуальных машин](https://docs.microsoft.com/azure service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)и лучше всего для свертывания и out виртуальной машины, масштабируемых наборов по горизонтали 1 узел за раз.
* Вам нужно использовать уровень надежности Silver или выше для кластера Service Fabric, а также уровень устойчивости Silver или выше для любого масштабируемого набора, для которого вы настраиваете правила автомасштабирования.
  * Емкость правила автоматического масштабирования (минимум) должно быть равно или больше, чем 5 экземпляров виртуальных машин и должно быть равно или больше, чем ваш уровень надежности минимальное значение для типа первичного узла.

> [!NOTE]
> Azure Service Fabric с отслеживанием состояния service fabric: / System/InfastructureService/< NODE_TYPE_NAME >, запускается на каждый тип узла с Silver или более высокую устойчивость, по которой — только системная служба, которая поддерживается для запуска в Azure на любой из типов узлов в кластерах .

## <a name="vertical-scaling-considerations"></a>Рекомендации по вертикальному масштабированию

[Вертикальное масштабирование](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) тип узла в Azure Service Fabric требуется ряд инструкции и рекомендации. Например:

* Масштабируемый кластер должен быть работоспособным. В противном случае будет только дестабилизировать дальнейшей кластера.
* **Silver устойчивости уровня или более поздней версии** является обязательным для всех типов узлов кластера Service Fabric, на которых размещены службы с отслеживанием состояния.

> [!NOTE]
> Типа первичного узла, на котором размещены системные службы Service Fabric с отслеживанием состояния должен быть уровень устойчивости Silver уровня или более поздней версии. Когда вы включите уровень устойчивости Silver, все операции кластера (обновление, добавление, удаление узлов и т. д.) замедлятся, так как система обеспечивает безопасность данных за счет скорости операций.

Вертикальное масштабирование набора масштабирования виртуальных машин является необратимой операцией. Чтобы выполнить безопасную операцию вертикального масштабирования, масштабируйте кластер горизонтально, добавив новый Масштабируемый набор с нужным номером SKU и перенеся службы в этот набор с нужным номером SKU. Изменение ресурса набора масштабирования виртуальной машины SKU является необратимой операцией, так как он повторно образы узлы удаляются все локально сохраненное состояние.

Кластер использует [свойства узла и ограничения на размещение](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) Service Fabric, чтобы выбрать место для размещения служб приложений. Вертикальное масштабирование типа первичного узла, при объявлении одинаковые значения свойств для `"nodeTypeRef"`, который можно найти в масштабируемый набор виртуальных машин задайте расширение Service Fabric. Следующий фрагмент кода из шаблона Resource Manager демонстрирует свойства, которые вам нужно объявить, с тем же значением для новых подготовленных масштабируемых наборов, до которых вы выполняете масштабирование, и поддерживается для кластера только с временным отслеживанием состояния:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Не оставляйте кластер работать с несколькими масштабируемыми наборами, для которых указано одинаковое значение `nodeTypeRef`, дольше чем требуется для выполнения успешной операции вертикального масштабирования.
> Всегда проверяйте операции в тестовых средах, прежде чем вносить любые изменения в рабочую среду. По умолчанию системные службы Service Fabric кластера имеют ограничение размещения для целевого только для типа первичного узла.

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

Масштабирование кластера Service Fabric, увеличив число экземпляров для конкретной виртуальной машины масштабируемого набора. Расширение можно выполнить программным способом, указав AzureClient и ID для того масштабируемого набора, размер которого вы намерены увеличить.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Чтобы масштабировать вручную, измените емкость в свойство SKU требуемого [масштабируемый набор виртуальных машин](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) ресурсов.
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Уменьшение масштаба

Уменьшение масштаба нужно выполнять осторожнее, чем увеличение. Например:

* Системные службы Service Fabric запускаются в кластере на узле основного типа. Никогда не уменьшайте число экземпляров узлов для этого типа ниже значения, которое гарантирует соответствующий уровень надежности. 
* Для службы с отслеживанием состояния определенное количество узлов должно постоянно работать, чтобы поддерживать доступность этой службы и сохранять ее состояние. Требуется как минимум количество узлов, равное количеству целевых наборов реплик секции или службы.

Чтобы уменьшить масштаб вручную, выполните следующие действия.

1. В PowerShell выполните `Disable-ServiceFabricNode` с намерением RemoveNode, чтобы отключить удаляемый узел. Удалите тип узла с наибольшим номером. Например, если ваш кластер содержит шесть узлов, удалите экземпляр виртуальной машины MyNodeType_5.
2. Выполните `Get-ServiceFabricNode` и убедитесь, что этот узел перешел в отключенное состояние. Если это не так, подождите, пока узел не будет отключен. Этот процесс для каждого узла может занять несколько часов. Не продолжайте процесс, пока узел не перейдет в отключенное состояние.
3. Уменьшите на единицу число виртуальных машин для соответствующего типа узла. Будет удален самый верхний экземпляр виртуальной машины.
4. При необходимости повторите шаги 1–3, но никогда не следует уменьшать число экземпляров в типах первичных узлов до значения меньше гарантируемого уровнем надежности. См. [рекомендации по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Чтобы масштабировать вручную, измените емкость в свойство SKU требуемого [масштабируемый набор виртуальных машин](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) ресурсов.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Повторяйте шаги с 1 по 3, пока вы не достигнете нужной емкости. Никогда не уменьшайте число экземпляров в узлах основного типа ниже значения, которое гарантирует соответствующий уровень надежности. Дополнительные сведения об уровнях надежности и требуемом количестве экземпляров см в [рекомендациям по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Чтобы уменьшить масштаб программным способом, необходимо подготовить узел к завершению работы. Этот процесс предусматривает поиск узла для удаления (узел с наивысшим номером) и его отключение. Например:

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
> При масштабировании кластера работает, вы увидите удален узла или виртуальной Машины, отображаемый в неработоспособном состоянии в Service Fabric Explorer. Описание этого поведения, см. в разделе [поведения, вы можете заметить в обозревателе Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Вы можете:
> * Вызовите [Remove ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) с именем соответствующего узла.
> * Развертывание [приложение service fabric автомасштабирования вспомогательный](https://github.com/Azure/service-fabric-autoscale-helper/) в кластере, что гарантирует масштабированное неработающих узлов будут удалены из Service Fabric Explorer.

## <a name="reliability-levels"></a>Уровни надежности

[Уровень надежности](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) является свойством ресурса кластера Service Fabric, а также нельзя настроить по-разному для отдельные типы узлов. Он определяет коэффициент репликации для системных служб кластера и применяется на уровне ресурса кластера. Уровень надежности определяет минимальное количество узлов для узла основного типа. Уровень надежности может принимать следующие значения:

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

Уровень устойчивости настраивается для двух ресурсов. Профиль расширения [ресурс масштабируемого набора виртуальных машин](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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
