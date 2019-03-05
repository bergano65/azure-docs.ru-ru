---
title: Масштабирование кластера Service Fabric в Azure | Документы Майкрософт
description: В этом руководстве вы узнаете, как быстро масштабировать кластер Service Fabric в Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/01/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 975b4558c0501423211553c1c2e330bced2c74a6
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674152"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Руководство. Масштабирование кластера Service Fabric в Azure

Это руководство представляет собой вторую часть серии. В нем показано, как масштабировать существующий кластер. Завершив работу с этим руководством, вы будете знать, как масштабировать кластер и очистить все остающиеся ресурсы.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * получение количества узлов кластера;
> * добавление узлов кластера (развертывание);
> * удаление узлов кластера (свертывание).

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание безопасного [кластера Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) в Azure с помощью шаблона;
> * увеличение или уменьшение масштаба кластера;
> * [Обновление среды выполнения кластера](service-fabric-tutorial-upgrade-cluster.md)
> * [Удаление кластера](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Установите [модуль Azure PowerShell версии 4.1 или более поздней версии](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) либо [Azure CLI](/cli/azure/install-azure-cli).
* Создание защищенного [кластера Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) в Azure
* Настройте среду разработки Windows. [Установите Visual Studio 2017](https://www.visualstudio.com), а также рабочие нагрузки **разработка Azure**, **ASP.NET и веб-разработка** и **кроссплатформенная разработка .NET Core**.  Теперь настройте [среду разработки .NET](service-fabric-get-started.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в учетную запись Azure и выберите подписку, прежде чем выполнять команды Azure.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Для успешного изучения этой части руководства необходимо подключиться к кластеру Service Fabric и масштабируемому набору виртуальных машин (в котором размещается кластер). Масштабируемый набор виртуальных машин — ресурс Azure, в котором размещена Service Fabric в Azure.

Подключившись к кластеру, можно отправить к нему запрос для получения информации. Можно узнать, о каких узлах известно кластеру. Подключение к кластеру в следующем коде выполняется с помощью сертификата, который был создан в первой части этой серии. Задайте для переменных `$endpoint` и `$thumbprint` собственные значения.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Теперь, когда вы подключены, можно использовать команду для получения информации о состоянии каждого узла в кластере. Для **PowerShell** используйте команду `Get-ServiceFabricClusterHealth`, а для **sfctl** — `sfctl cluster select`.

## <a name="scale-out"></a>Масштабирование

При развертывании вы добавляете дополнительные экземпляры виртуальных машин в масштабируемый набор. Эти экземпляры становятся узлами, которые использует Service Fabric. Службе Service Fabric известно, когда в масштабируемый набор добавляются экземпляры (путем развертывания), и она реагирует автоматически. Следующий код получает масштабируемый набор по имени и увеличивает значение **емкости** масштабируемого набора на 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Этот код задает значение 6 для емкости.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Свертывание

Свертывание похоже на развертывание, только при этом вы используете меньшее значение **емкости**. При свертывании масштабируемого набора нужно удалить из него экземпляры виртуальных машин. Как правило, службе Service Fabric неизвестно, что произошло, и она считает, что узел утерян. Затем Service Fabric сообщает о неработоспособном состоянии кластера. Чтобы предотвратить это состояние, необходимо сообщить Service Fabric, что узел должен исчезнуть.

### <a name="remove-the-service-fabric-node"></a>Удаление узла Service Fabric

> [!NOTE]
> Эта часть относится только к уровню устойчивости *Bronze*. Дополнительные сведения об устойчивости см. в разделе [Характеристики устойчивости кластера][durability].

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

После применения этих шагов узел можно удалить из масштабируемого набора. Если вы используете любой уровень устойчивости, кроме [Bronze][durability], эти шаги будут выполнены, когда экземпляр масштабируемого набора будет удален.

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
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Этот код задает значение 5 для емкости.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * получение количества узлов кластера;
> * добавление узлов кластера (развертывание);
> * удаление узлов кластера (свертывание).

Теперь перейдите к следующему руководству, чтобы узнать, как обновить среду выполнения кластера.
> [!div class="nextstepaction"]
> [обновление среды выполнения кластера;](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
