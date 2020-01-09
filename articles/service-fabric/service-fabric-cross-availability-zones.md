---
title: Развертывание кластера в Зоны доступности
description: Узнайте, как создать кластер Azure Service Fabric в Зоны доступности.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609984"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Развертывание кластера Azure Service Fabric в Зоны доступности
Зоны доступности в Azure — это высокодоступное предложение, защищающее приложения и данные от сбоев центров обработки данных. Зона доступности — это уникальное физическое расположение, оснащенное независимым питанием, охлаждением и сетью в регионе Azure.

Service Fabric поддерживает кластеры, охватывающие Зоны доступности, путем развертывания типов узлов, которые закреплены в конкретных зонах. Это обеспечит высокую доступность ваших приложений. Зоны доступности Azure доступны только в выбранных регионах. Дополнительные сведения см. в разделе [зоны доступности Azure обзор](https://docs.microsoft.com/azure/availability-zones/az-overview).

Примеры шаблонов доступны: [шаблон зоны перекрестной доступности Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Рекомендуемая топология для типа первичного узла Service Fabric кластеров Azure, охватывающих несколько Зоны доступности
Кластер Service Fabric, распределенный между Зоны доступности обеспечивает высокий уровень доступности состояния кластера. Чтобы охватить кластер Service Fabric в разных зонах, необходимо создать тип первичного узла в каждой зоне доступности, поддерживаемой регионом. Начальные узлы распределяются равномерно по каждому из типов первичного узла.

Для топологии, рекомендуемой для типа первичного узла, требуются следующие ресурсы:

* Уровень надежности кластера, установленный в категории Platinum.
* Три типа узлов, помеченные как первичные.
    * Каждый тип узла должен быть сопоставлен с собственным масштабируемым набором виртуальных машин, расположенным в разных зонах.
    * Каждый масштабируемый набор виртуальных машин должен иметь по крайней мере пять узлов (серебристая устойчивость).
* Один ресурс общедоступного IP-адреса, использующий SKU "Стандартный".
* Один Load Balancer ресурс, использующий номер SKU "Стандартный".
* NSG, на который ссылается подсеть, в которой развертываются масштабируемые наборы виртуальных машин.

>[!NOTE]
> Свойство одной группы размещения масштабируемого набора виртуальных машин должно иметь значение true, так как Service Fabric не поддерживает один масштабируемый набор виртуальных машин, охватывающий зоны.

 ![Архитектура зоны доступности Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Требования к сети
### <a name="public-ip-and-load-balancer-resource"></a>Общедоступный IP-адрес и Load Balancer ресурс
Чтобы включить свойство "зоны" в ресурсе масштабируемого набора виртуальных машин, подсистема балансировки нагрузки и ресурс IP, на который ссылается этот масштабируемый набор виртуальных машин, должны использовать номер SKU " *стандартный* ". При создании подсистемы балансировки нагрузки или ресурса IP-адреса без свойства SKU будет создан базовый номер SKU, который не поддерживает Зоны доступности. Балансировщик нагрузки уровня "Стандартный" будет блокировать весь трафик извне по умолчанию; чтобы разрешить внешний трафик, необходимо развернуть NSG в подсети.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Невозможно выполнить изменение SKU на месте в ресурсах общедоступного IP-адреса и балансировщика нагрузки. Если выполняется миграция с существующих ресурсов, имеющих базовый номер SKU, см. раздел миграция этой статьи.

### <a name="virtual-machine-scale-set-nat-rules"></a>Правила NAT для масштабируемого набора виртуальных машин
Правила NAT для входящего трафика подсистемы балансировки нагрузки должны соответствовать пулам NAT из масштабируемого набора виртуальных машин. Каждый масштабируемый набор виртуальных машин должен иметь уникальный пул NAT для входящего трафика.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Стандартные правила SKU Load Balancer исходящих подключений
Load Balancer (цен. категория "Стандартный") и стандартный общедоступный IP-адрес предоставляют новые возможности и различные варианты поведения для исходящего подключения по сравнению с использованием основных номеров SKU. Если вам требуется исходящее подключение при работе с номерами SKU категории "Стандартный", вы должны явно определить это подключение с использованием общедоступных IP-адресов категории "Стандартный" или общедоступного Load Balancer категории "Стандартный". Дополнительные сведения см. в статье [Исходящие подключения](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) и [Azure Load Balancer (цен. Категория "Стандартный")](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Стандартный шаблон ссылается на NSG, который разрешает по умолчанию весь исходящий трафик. Входящий трафик ограничивается портами, необходимыми для операций управления Service Fabric. Правила NSG можно изменить в соответствии с вашими требованиями.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Включение зон в масштабируемом наборе виртуальных машин
Чтобы включить зону, в масштабируемом наборе виртуальных машин необходимо включить следующие три значения в ресурс масштабируемого набора виртуальных машин.

* Первое значение — это свойство **Zones** , которое указывает, в какой зоне доступности будет развернут масштабируемый набор виртуальных машин.
* Вторым значением является свойство "singlePlacementGroup", которое должно иметь значение true.
* Третье значение — это свойство "Фаултдомаиноверриде" в Service Fabric расширении масштабируемого набора виртуальных машин. Значение этого свойства должно включать регион и зону, в которых будет размещен этот масштабируемый набор виртуальных машин. Пример: "Фаултдомаиноверриде": "eastus/AZ1". все ресурсы масштабируемого набора виртуальных машин должны быть помещены в один регион, так как кластеры Azure Service Fabric не поддерживают кросс-регион.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Включение нескольких типов первичных узлов в ресурс кластера Service Fabric
Чтобы задать один или несколько типов узлов в качестве первичных в ресурсе кластера, задайте для свойства "IsTrue" значение "true". При развертывании кластера Service Fabric в разных Зоны доступности необходимо иметь три типа узлов в разных зонах.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Переход на использование Зоны доступности из кластера с использованием базового SKU Load Balancer и IP-адреса базового SKU
Чтобы выполнить миграцию кластера, который использовал Load Balancer и IP-адрес с номером SKU "базовый", сначала необходимо создать совершенно новый Load Balancer и IP-ресурс, используя номер SKU "Стандартный". Невозможно обновить эти ресурсы на месте.

На новые типы узлов зоны перекрестной доступности, которые вы хотите использовать, должны быть ссылки на новую ФУНТОВу и IP-адрес. В приведенном выше примере в зонах 1, 2 и 3 были добавлены три новых ресурса масштабируемого набора виртуальных машин. Эти масштабируемые наборы виртуальных машин ссылаются на только что созданную балансировку нагрузки и IP-адрес и помечаются как типы первичных узлов в ресурсе кластера Service Fabric.

Для начала необходимо добавить новые ресурсы в существующий шаблон диспетчер ресурсов. К этим ресурсам относятся:
* Ресурс общедоступного IP-адреса, использующий SKU "Стандартный".
* Load Balancer ресурс с использованием номера SKU "Стандартный".
* NSG, на который ссылается подсеть, в которой развертываются масштабируемые наборы виртуальных машин.
* Три типа узлов, помеченные как первичные.
    * Каждый тип узла должен быть сопоставлен с собственным масштабируемым набором виртуальных машин, расположенным в разных зонах.
    * Каждый масштабируемый набор виртуальных машин должен иметь по крайней мере пять узлов (серебристая устойчивость).

Пример этих ресурсов можно найти в [образце шаблона](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

После завершения развертывания ресурсов можно приступить к отключению узлов в типе первичного узла из исходного кластера. После отключения узлов системные службы будут перенесены на новый тип первичного узла, который был развернут на предыдущем шаге.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

После отключения всех узлов системные службы будут работать на первичном узле типа, который распределяется между зонами. После этого Отключенные узлы можно удалить из кластера. После удаления узлов можно удалить исходные IP-адреса, Load Balancer и масштабируемые наборы виртуальных машин.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Затем следует удалить ссылки на эти ресурсы из развернутого шаблона диспетчер ресурсов.

На последнем шаге будет выполняться обновление DNS-имени и общедоступного IP-адреса.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
