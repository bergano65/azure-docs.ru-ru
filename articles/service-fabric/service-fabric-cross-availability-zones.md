---
title: Развертывание кластера Azure Service Fabric в зонах доступности | Документация Майкрософт
description: Узнайте, как создать кластер Azure Service Fabric в зонах доступности.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077460"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Развертывание кластера Azure Service Fabric в зонах доступности
Зоны доступности в Azure является высокого уровня доступности предлагают, защищающий приложения и данные от сбоев центров обработки данных. Зона доступности — уникальные физическое расположение, оснащенных независимыми системами электроснабжения, охлаждения и сетевого взаимодействия в регионе Azure.

Service Fabric поддерживает кластеров, охватывающих в зонах доступности путем развертывания типы узлов, закрепленные с определенными зонами. Это обеспечит высокий уровень доступности приложений. Зоны доступности Azure доступны только в выбранных регионах. Дополнительные сведения см. в разделе [Общие сведения о зонах доступности Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Доступны примеры шаблонов: [Service Fabric cross шаблон зоны доступности](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Рекомендуемые топологии для типа первичного узла Azure Service Fabric кластеры, состоящие в зонах доступности
Кластер Service Fabric, распределенных между зонами доступности обеспечивает высокий уровень доступности состояние кластера. Кластер Service Fabric, охватывающая зоны, необходимо создать в каждой зоне доступности, поддерживаемый регион типа первичного узла. Это будет начальные узлы равномерно распределить каждого типа первичного узла.

Рекомендуемая топология для типа первичного узла требуются ресурсы, указанные ниже:

* Уровень надежности кластера, задайте для Platinum.
* Три типа узла помечены как основной.
    * Каждый тип узла должен быть сопоставлен свой собственный набор масштабирования виртуальных машин, расположенных в разных зонах.
    * Каждый масштабируемый набор виртуальных машин, должны иметь по крайней мере пять узлов (устойчивости Silver).
* Один общедоступный IP-адрес ресурса с помощью SKU "стандартный".
* Один ресурс балансировки нагрузки с помощью SKU "стандартный".
* Группы безопасности сети, ссылается на подсеть, в которых выполняется развертывание масштабируемых наборов виртуальных машин.

>[!NOTE]
> Масштабируемый набор виртуальных машин, одна группа свойства необходимо задать значение true, так как Service Fabric не поддерживает масштабируемый набор отдельной виртуальной машины, который охватывает зоны.

 ![Архитектура зоне доступности Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Требования к сети
### <a name="public-ip-and-load-balancer-resource"></a>Общедоступный IP-адрес и ресурса подсистемы балансировки нагрузки
Чтобы включить зоны, свойство в масштабируемый набор виртуальных машин значение ресурса, подсистемы балансировки нагрузки и ресурс IP-адреса, которые ссылается этот масштабируемый набор виртуальных машин должны использовать *стандартный* SKU. Создание подсистемы балансировки нагрузки или ресурс IP-адреса без свойство SKU будет создан SKU "базовый", который не поддерживает зоны доступности. Подсистемы балансировки нагрузки уровня "стандартный" позволяет блокировать весь трафик из внешней по умолчанию; Чтобы разрешить за пределами трафика, группы безопасности сети должны быть развернуты в подсети.

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
> Это не можно выполнить на месте изменение номера SKU на ресурсы общедоступных IP-адрес и load balancer. Если вы выполняете миграцию из существующих ресурсов, которые имеют SKU "базовый", см. в разделе миграции части этой статьи.

### <a name="virtual-machine-scale-set-nat-rules"></a>Масштабируемый набор виртуальных машин правила преобразования сетевых адресов
Подсистема балансировки нагрузки правила NAT для входящего трафика должно соответствовать пулы NAT из масштабируемого набора виртуальных машин. Каждый масштабируемый набор виртуальных машин должен иметь уникальный пул NAT входящего трафика.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Стандартные правила для исходящих подключений SKU подсистемы балансировки нагрузки
Стандартная Подсистема балансировки нагрузки и стандартный общедоступный IP-адрес Представляем новые возможности и различное поведение исходящих подключений по сравнению с использованием основных номеров SKU. Если вам требуется исходящее подключение при работе с номерами SKU категории "Стандартный", вы должны явно определить это подключение с использованием общедоступных IP-адресов категории "Стандартный" или общедоступного Load Balancer категории "Стандартный". Дополнительные сведения см. в разделе [исходящие подключения](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) и [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Стандартный шаблон ссылается на группу безопасности сети, который разрешает весь исходящий трафик по умолчанию. Входящий трафик ограничен порты, необходимые для операций управления Service Fabric. Правила NSG можно изменить в соответствии с требованиями.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Включение зон в масштабируемый набор виртуальных машин задайте
Чтобы включить зоны, задайте, вы на масштабируемый набор виртуальных машин должен включать следующие три значения в ресурсе масштабируемого набора виртуальных машин.

* Первое значение **зоны** свойство, которое указывает, какой зоне доступности, масштабируемый набор виртуальных машин будет развернута.
* Второе значение — свойство «singlePlacementGroup», которое необходимо задать значение true.
* Третье значение является свойство «faultDomainOverride» в расширение набора масштабирования виртуальных машин Service Fabric. Значение этого свойства должно включать региона и зоны, в котором будет размещаться этот масштабируемый набор виртуальных машин. Пример: «faultDomainOverride»: «eastus/az1», все масштабируемого набора виртуальных машин ресурсы должны находиться в том же регионе, поскольку кластеров Azure Service Fabric не имеют кросс-поддержка регионов.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Включение нескольких основных типа узла в ресурсе кластера Service Fabric
Чтобы задать один или несколько типов узлов в качестве основного сервера в ресурс кластера, задайте для свойства «isPrimary», «true». При развертывании кластера Service Fabric в зонах доступности, должен иметь три типы узлов в различных зонах.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Перейти на использование зон доступности из кластера, используя базовый SKU балансировщика нагрузки и базовый SKU IP-адреса
Для переноса в кластере, которые было с помощью подсистемы балансировки нагрузки и IP-адрес с SKU "базовый", необходимо сначала создать новую подсистему балансировки нагрузки и IP-адрес ресурса с помощью SKU "стандартный". Он не может изменять эти ресурсы на месте.

Новые балансировки Нагрузки и IP-адрес следует ссылаться в новых типов узлов перекрестного зоны доступности, которые вы хотите использовать. В примере выше три новых масштабирования виртуальных машин набора ресурсы были добавлены в зонах 1, 2 и 3. Эти масштабируемые виртуальных машин задает ссылку на только что созданный балансировки Нагрузки и IP-адрес и помечаются как типы первичного узла в ресурсе кластера Service Fabric.

Чтобы начать, необходимо добавить новые ресурсы в существующий шаблон Resource Manager. Эти ресурсы включают:
* Общедоступный IP-адрес ресурса с помощью SKU "стандартный".
* Ресурс балансировки нагрузки, с помощью SKU "стандартный".
* Группы безопасности сети, ссылается на подсеть, в которых выполняется развертывание масштабируемых наборов виртуальных машин.
* Три типа узла, помеченный как первичный.
    * Каждый тип узла должен быть сопоставлен свой собственный набор масштабирования виртуальных машин, расположенных в разных зонах.
    * Каждый масштабируемый набор виртуальных машин, должны иметь по крайней мере пять узлов (устойчивости Silver).

Пример эти ресурсы можно найти в [пример шаблона](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Когда ресурсы развернуты, вы сможете отключить узлов основного типа из исходного кластера. Как узлы, отключенные, системные службы будут перенесены в новый тип первичного узла, которое развернуто на предыдущем шаге.

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

Как только узлы все отключены, системные службы будут выполняться на типа первичного узла, распределены между зонами. После этого можно удалить отключенные узлы из кластера. После удаления узлов, можно удалить исходный IP-адрес подсистемы балансировки нагрузки, и ресурсы масштабируемого набора виртуальных машин.

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

Затем следует удалить ссылки на эти ресурсы из шаблона Resource Manager, которое развернуто.

Последний шаг включает обновление DNS-имя и общедоступный IP-адрес.

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
