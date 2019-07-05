---
title: Планирования емкости и масштабирования для Azure Service Fabric | Документация Майкрософт
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
ms.openlocfilehash: 8ba4763e8d4835911d33d21c0f5bb431851a649b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444720"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Планирование производительности и масштабирования для Azure Service Fabric

Перед созданием кластера Azure Service Fabric или масштабирование вычислительных ресурсов, на которых размещается кластер, очень важно правильно оценить производительность. См. дополнительные сведения о [планировании загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Дополнительные советы и рекомендации для обеспечения масштабируемости кластера, см. в разделе [вопросы масштабируемости в Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Помимо рассмотрения узел характеристики типа и кластера, следует ожидать операций масштабирования занимает больше часа для завершения для рабочей среды. Это верно независимо от количества виртуальных машин, вы добавляете.

## <a name="autoscaling"></a>Автомасштабирование
Следует выполнять операции масштабирования с помощью шаблонов Azure Resource Manager, так как это лучший способ обрабатывать [конфигурации ресурсов как код]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

С помощью автоматического масштабирования с помощью масштабируемых наборов виртуальных машин сделает ошибочно определить количество экземпляров для масштабируемых наборов виртуальных машин с версиями шаблон Resource Manager. Неправильное определение повышает риск, что будущие развертывания приведет к непредвиденным операции масштабирования. В общем случае следует использовать автоматическое масштабирование, если:

* Если развертывание шаблонов Resource Manager с объявленной емкостью не подходит для вашего варианта использования.
     
   Кроме ручного масштабирования можно настроить [конвейера непрерывной интеграции и доставки в службах Azure DevOps с помощью проектов развертывания группы ресурсов Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Этот конвейер обычно вызывается приложение логики, которое использует метрики производительности виртуальной машины, оттуда [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). Конвейер эффективно зависимости автоматически масштабируется по любой метрики нужно, одновременно оптимизируя для шаблонов Resource Manager.
* Необходимо горизонтально масштабировать только один узел масштабируемого набора виртуальных машин одновременно.
   
   Для масштабирования с тремя или более узлами одновременно, следует [горизонтальное масштабирование кластера Service Fabric путем добавления набора масштабирования виртуальных машин](virtual-machine-scale-set-scale-node-type-scale-out.md). Лучше всего для свертывания и масштабирования масштабируемый набор виртуальных машин по горизонтали, задает один узел за раз.
* У вас есть надежности Silver или выше для кластера Service Fabric, а уровень устойчивости Silver или выше в любом масштабе, в которой можно настроить правила автоматического масштабирования.
  
   Минимальная емкость для правила автоматического масштабирования должен быть равным или больше, чем пять экземпляров виртуальных машин. Также она должна равным или больше, чем ваш уровень надежности минимальное значение для типа первичного узла.

> [!NOTE]
> Service Fabric с отслеживанием состояния service fabric: / System/InfastructureService/< NODE_TYPE_NAME > работает на каждый тип узла, имеет устойчивости Silver или более поздней версии. Это единственная Системная служба, которая поддерживается для запуска в Azure на любой из типов узлов в кластеры.

## <a name="vertical-scaling-considerations"></a>Рекомендации по вертикальному масштабированию

[Вертикальное масштабирование](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) тип узла в Azure Service Fabric требуется ряд инструкции и рекомендации. Пример:

* Масштабируемый кластер должен быть работоспособным. В противном случае будет дестабилизировать дальнейшей кластера.
* Уровень устойчивости Silver уровня или более поздней версии необходим для всех типов узлов кластера Service Fabric, на которых размещены службы с отслеживанием состояния.

> [!NOTE]
> Типа первичного узла, на котором размещена системных служб с отслеживанием состояния Service Fabric должен быть уровень устойчивости Silver уровня или более поздней версии. После включения устойчивости Silver, операции кластера, такие как обновление, добавление или удаление узлов и т. д. будет более система выполняет оптимизацию для защиты данных по скорости операций.

Вертикальное масштабирование набора масштабирования виртуальных машин является необратимой операцией. Вместо этого горизонтально масштабируйте кластер, добавив новый масштабируемый набор с нужный номер SKU. Затем перенесите нужный номер SKU для завершения операции безопасный вертикального масштабирования ваших служб. Изменение ресурса набора масштабирования виртуальной машины SKU является необратимой операцией, так как он пересоздает образ узлов, который удаляет все локально сохраняемое состояние.

Ваш кластер использует Service Fabric [свойства узла и ограничения на размещение](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) выбрать место для размещения служб вашего приложения. Когда вертикальное масштабирование типа первичного узла, объявлять одинаковые значения свойств для `"nodeTypeRef"`. Эти значения можно найти в расширении Service Fabric для масштабируемых наборов виртуальных машин. 

В следующем фрагменте шаблона Resource Manager показано свойства, которые вы объявлю. Он имеет то же значение для недавно подготовленного масштабируемые наборы, которые вы масштабирование и поддерживается только в качестве временной службы с отслеживанием состояния для кластера.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Не оставляйте кластером с несколькими наборами масштабирования, использующие одну `nodeTypeRef` для выполнения успешной операции вертикального масштабирования требуется больше времени, чем значение свойства.
>
> Всегда проверяйте операций в тестовой среде, прежде чем изменения в рабочую среду. По умолчанию системные службы для кластера Service Fabric имеют ограничение размещения только в целевой тип первичного узла.

Объявив свойства узла и ограничения на размещение, выполните следующие шаги поочередно для каждого экземпляра виртуальной машины. Благодаря этому системных служб (и ваших служб с отслеживанием состояния), чтобы корректно завершить работу экземпляра виртуальной Машины, удаляется при создании новых реплик в другом месте.

1. В PowerShell выполните `Disable-ServiceFabricNode` блокировка с намерением `RemoveNode` для отключения узла, который вы собираетесь удалить. Удалите тип узла с наибольшим номером. Например если у вас есть кластер из шести узлов, удалите экземпляр виртуальной машины «MyNodeType_5».
2. Выполните `Get-ServiceFabricNode` и убедитесь, что этот узел перешел в отключенное состояние. Если это не так, подождите, пока узел не будет отключен. Это может занять несколько часов, для каждого узла. Не продолжайте процесс, пока узел не перейдет в отключенное состояние.
3. Уменьшите число виртуальных машин на одну в этом типе узла. Будет удален самый верхний экземпляр виртуальной машины.
4. При необходимости повторите шаги 1–3, но никогда не следует уменьшать число экземпляров в типах первичных узлов до значения меньше гарантируемого уровнем надежности. См. [рекомендации по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).
5. Как только все виртуальные машины будут удалены (в виде «Вниз») fabric: / System/InfrastructureService / [имя_узла] будет отображаться с состоянием ошибки. Затем можно обновить ресурс кластера, чтобы удалить тип узла. Можно использовать развертывание шаблона ARM, или изменить ресурс кластера через [диспетчера ресурсов Azure](https://resources.azure.com). После этого будет запущена обновление кластера, это приведет к удалению fabric: / System/InfrastructureService / [тип узла] службы, который находится в состоянии ошибки.
 6. После вы все равно увидите узлы, как «Вниз» с помощью Service Fabric Explorer просмотреть менее, при необходимости можно удалить VMScaleSet. Последним шагом будет очистить их с `Remove-ServiceFabricNodeState` команды.

### <a name="example-scenario"></a>Пример сценария
— Сценарий поддерживается для времени выполнения операции вертикального масштабирования: необходимо выполнить миграцию из неуправляемого диска кластера Service Fabric и приложения для использования управляемых дисков без простоя приложения. 

Можно подготовить новый масштабируемый набор виртуальных машин с управляемыми дисками и выполнить обновление приложения с ограничениями на размещение, предназначенных для подготовленной емкости. Кластер Service Fabric можно запланировать выполнение рабочей нагрузки подготовленного кластера узел емкости, которая разворачивается по доменам обновления без простоя приложения. 

Конечные точки внутреннего пула для [Azure Load Balancer уровня SKU "базовый"](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) могут быть виртуальными машинами в одну группу доступности или масштабируемый набор виртуальных машин. Это означает, что нельзя использовать подсистему балансировки нагрузки SKU "базовый", если при перемещении приложения Service Fabric систем между масштабируемые наборы, не вызывая временной недоступности конечной точки управления кластера Service Fabric. Это верно, несмотря на то, что кластер и его приложение по-прежнему работают.

Пользователи часто подсистему балансировки нагрузки уровня "стандартный", при выполнении виртуальный адрес (VIP) обмен IP между балансировщиком нагрузки SKU "базовый" и ресурсы load balancer уровня "стандартный". Эта методика ограничивает любые будущие прочтите около 30 секунд, необходимых для переключения виртуального IP-адреса.

## <a name="horizontal-scaling"></a>горизонтальное масштабирование;

Можно сделать, горизонтальное масштабирование, либо [вручную](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) или [программно](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Если масштабирование типа узла, имеющего устойчивости Silver или Gold, масштабирование будет происходить медленно.

### <a name="scaling-out"></a>Развертывание

Масштабирование кластера Service Fabric, увеличив число экземпляров для конкретной виртуальной машины масштабируемого набора. Вы можете выполнить развертывание программным способом с помощью `AzureClient` и идентификатор требуемой масштабируемого набора для повышения его емкости.

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

Уменьшение масштаба нужно выполнять осторожнее, чем увеличение. Пример:

* Для типа первичного узла в кластере запустите системные службы Service Fabric. Никогда не уменьшайте число экземпляров узлов для этого типа ниже значения, которое гарантирует соответствующий уровень надежности. 
* Для службы с отслеживанием состояния необходимо определенное количество узлов, которые всегда являются вверх, чтобы поддерживать доступность и сохранять состояние службы. Как минимум вам потребуется несколько узлов равным целевой счетчик наборов реплик секции или службы.

Чтобы уменьшить масштаб вручную, выполните следующие действия.

1. В PowerShell выполните `Disable-ServiceFabricNode` блокировка с намерением `RemoveNode` для отключения узла, который вы собираетесь удалить. Удалите тип узла с наибольшим номером. Например если у вас есть кластер из шести узлов, удалите экземпляр виртуальной машины «MyNodeType_5».
2. Выполните `Get-ServiceFabricNode` и убедитесь, что этот узел перешел в отключенное состояние. Если это не так, подождите, пока узел не будет отключен. Это может занять несколько часов, для каждого узла. Не продолжайте процесс, пока узел не перейдет в отключенное состояние.
3. Уменьшите число виртуальных машин на одну в этом типе узла. Будет удален самый верхний экземпляр виртуальной машины.
4. Повторите шаги 1 – 3, при необходимости, пока вы не развернете емкость, которую вы хотите. Никогда не уменьшайте число экземпляров в узлах основного типа ниже значения, которое гарантирует соответствующий уровень надежности. См. [рекомендации по планированию загрузки кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Чтобы масштабировать вручную, измените емкость в свойство SKU требуемого [масштабируемый набор виртуальных машин](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) ресурсов.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Необходимо подготовить узел для завершения работы программного масштабирования. Поиск узла, который нужно удалить (самый верхний экземпляр узел). Пример:

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

Отключение и удаление узла с использованием тех же `FabricClient` экземпляра (`client` в данном случае) и экземпляр узла (`instanceIdString` в данном случае), которые использовались в предыдущем коде:

```c#
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
> При уменьшении масштаба кластера, вы увидите, что экземпляр удален узла или виртуальной Машины, отображаемый в неработоспособном состоянии в Service Fabric Explorer. Описание этого поведения, см. в разделе [поведения, вы можете заметить в обозревателе Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Вы можете:
> * Вызовите [команду Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) с именем соответствующего узла.
> * Развертывание [вспомогательное приложение Service Fabric автомасштабирования](https://github.com/Azure/service-fabric-autoscale-helper/) в кластере. Благодаря этому приложению, что масштаба узлы будут удалены из Service Fabric Explorer.

## <a name="reliability-levels"></a>Уровни надежности

[Уровень надежности](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) является свойством ресурсе кластера Service Fabric. Его нельзя настроить по-разному для отдельные типы узлов. Он определяет коэффициент репликации для системных служб кластера и применяется на уровне ресурса кластера. 

Уровень надежности определяет минимальное количество узлов для узла основного типа. Уровень надежности может принимать следующие значения:

* Platinum: Выполняется системных служб с целевой счетчик наборов реплик из семи и девять начальных узлов.
* Gold: Выполняется системных служб с целевой счетчик наборов реплик из семи до семи начальных узлов.
* Silver: Выполняется системных служб с целевой счетчик наборов реплик из пяти до пяти начальных узлов.
* Bronze: Выполняется системных служб с целевой счетчик наборов реплик из трех и тремя узлами начальных значений.

Мы рекомендуем использовать уровень надежности не ниже Silver.

Уровень надежности задается в разделе свойств для [ресурса Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), как показано ниже:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Уровни устойчивости

> [!WARNING]
> Типы узлов, выполняющиеся с устойчивостью Bronze, _не получают привилегий_. Задания инфраструктуры, которые влияют на рабочие нагрузки без отслеживания состояния не будет остановлена или отложена, которая может повлиять на рабочие нагрузки. 
>
> Используйте уровень устойчивости Bronze только для тех типов узлов, на которых выполняются рабочие нагрузки без учета состояния. Для рабочих нагрузок запустите Silver или более поздней версии, чтобы обеспечить согласованность состояния. Выберите уровень надежности, следуя рекомендациям в [документации по планированию ресурсов](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Уровень устойчивости настраивается для двух ресурсов. Одним является расширение профиль [ресурс масштабируемого набора виртуальных машин](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

Другой ресурс находится под `nodeTypes` в [ресурса Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md).
* Узнайте о [вариантах поддержки Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
