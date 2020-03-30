---
title: Развертывание кластера в зонах доступности
description: Узнайте, как создать кластер Azure Service Fabric в зонах доступности.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609984"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Развертывание кластера Azure Service Fabric в зонах доступности
Зоны доступности в Azure — это предложение с высокой доступностью, которое защищает приложения и данные от сбоев в работе центра обработки данных. Зона доступности — это уникальное физическое место, оснащенное независимой мощностью, охлаждением и сетью в регионе Azure.

Service Fabric поддерживает кластеры, охватывающие зоны доступности, развертывая типы узлов, закрепленные в определенных зонах. Это обеспечит высокую доступность ваших приложений. Зоны доступности Azure доступны только в некоторых регионах. Для получения дополнительной информации смотрите [обзор зон доступности Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Шаблоны примеров доступны: [шаблон зоны перекрестной доступности service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Рекомендуемая топология для первичного типа узлов кластеров Azure Service Fabric, охватывающих зоны доступности
Кластер Service Fabric, распределенный по зонам доступности, обеспечивает высокую доступность состояния кластера. Чтобы охватить кластер Service Fabric по зонам, необходимо создать основной тип узла в каждой зоне доступности, поддерживаемой регионом. Это позволит равномерно распределить семенные узлы по каждому из типов основных узлов.

Рекомендуемая топология для типа первичного узла требует ресурсов, изложенных ниже:

* Уровень надежности кластера установлен на платину.
* Три типа узлов, отмеченные как первичные.
    * Каждый тип узла должен быть отображен по своему собственному набору виртуальной машины, расположенной в разных зонах.
    * Каждый виртуальный набор масштабов машины должен иметь не менее пяти узлов (Серебряная долговечность).
* Единый общественный ресурс ИС с использованием стандартного SKU.
* Ресурс с одной нагрузкой балансоровых средств с использованием стандартного SKU.
* NSG, на которую ссылается подсеть, в которой развертывается наборы виртуальных машин.

>[!NOTE]
> Виртуальная шкала машины набор одного размещения группы собственности должны быть установлены на истину, так как Service Fabric не поддерживает один виртуальный набор масштаба машины, которая охватывает зоны.

 ![Архитектура зоны доступности тканей службы Azure][sf-architecture]

## <a name="networking-requirements"></a>Требования к сети
### <a name="public-ip-and-load-balancer-resource"></a>Общественный ресурс баланса и полезности и грузоподъемности
Для включения свойств зон на ресурсе, установленном виртуальной шкалой машины, балансомер нагрузки и IP-ресурс, на который ссылается этот набор виртуальной шкалы машин, должны использовать *Стандартный* SKU. Создание баланса нагрузки или ресурса IP без свойства SKU создаст базовый SKU, который не поддерживает зоны доступности. Балансировщик нагрузки Standard SKU будет блокировать весь трафик извне по умолчанию; для обеспечения внешнего трафика, NSG должны быть развернуты в подсети.

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
> Невозможно сделать смену SKU на общедоступных ресурсах и балансереи нагрузки. Если вы мигрируете из существующих ресурсов, имеющих базовый SKU, см.

### <a name="virtual-machine-scale-set-nat-rules"></a>Виртуальная шкала машины набор правил NAT
Входящие правила NAT должны соответствовать пулам NAT из набора виртуальной шкалы машин. Каждый виртуальный набор масштабов машины должен иметь уникальный входящий пул NAT.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Стандартные правила исходящего баланса загрузки SKU
Standard Load Balancer и Standard Public IP вводят новые возможности и различное поведение для исходящих подключений по сравнению с использованием базовых SKUs. Если вам требуется исходящее подключение при работе с номерами SKU категории "Стандартный", вы должны явно определить это подключение с использованием общедоступных IP-адресов категории "Стандартный" или общедоступного Load Balancer категории "Стандартный". Для получения дополнительной [информации](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)см.

>[!NOTE]
> Стандартный шаблон ссылается на NSG, который позволяет весь исходящий трафик по умолчанию. Входящий трафик ограничен портами, необходимыми для управления сервисной тканью. Правила NSG могут быть изменены в соответствии с вашими требованиями.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Включение зон в наборе виртуального масштаба машины
Для включения зоны в набор виртуальной шкалы машины необходимо включить следующие три значения в ресурс набора виртуальной шкалы машины.

* Первое значение — это свойство **зон,** в котором указывается, в какую зону доступности будет развернут амеб виртуального набора машин.
* Второе значение - это свойство "singlePlacementGroup", которое должно быть установлено на истину.
* Третье значение — это свойство «faultDomainOverride» в расширении набора виртуальной машины Service Fabric. Значение для этого свойства должно включать область и зону, в которой будет размещен этот виртуальный набор масштабов машины. Пример: "faultDomainOverride": "Eastus/az1" Все ресурсы виртуального набора масштабов машин должны быть размещены в одном регионе, поскольку кластеры Azure Service Fabric не имеют поддержки поперечное количество.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Включение нескольких типов первичных узлов в ресурсе кластера service Fabric
Чтобы установить один или несколько типов узлов в качестве основного в ресурсе кластера, установите свойство "isPrimary" на "истинное". При развертывании кластера Service Fabric в зонах доступности необходимо иметь три типа узлов в отдельных зонах.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Переход к использованию зон доступности из кластера с помощью базового баланса нагрузки SKU и базового IP-адреса SKU
Чтобы перенести кластер, использующий балансирус и IP с базовым SKU, необходимо сначала создать совершенно новый ресурс Load Balancer и IP с использованием стандартного SKU. Обновить эти ресурсы на месте не представляется возможным.

Новый LB и IP должны быть отослужы в новых типах узлов зоны доступности, которые вы хотели бы использовать. В приведенном выше примере три новых виртуальных набора машин были добавлены в зонах 1,2 и 3. Эти виртуальные наборы шкалы машин ссылаются на вновь созданные LB и IP и помечены как основные типы узлов в кластере Service Fabric Resource.

Для начала необходимо добавить новые ресурсы в существующий шаблон управления ресурсами. Эти ресурсы включают в себя:
* Общественный ресурс ИС с использованием стандартного SKU.
* Ресурс балансора нагрузки используя стандартный SKU.
* NSG, на которую ссылается подсеть, в которой развертывается наборы виртуальных машин.
* Три типа узлов, помечены как первичные.
    * Каждый тип узла должен быть отображен на свой собственный виртуальный набор масштаба машины, расположенный в разных зонах.
    * Каждый виртуальный набор масштабов машины должен иметь не менее пяти узлов (Серебряная долговечность).

Пример этих ресурсов можно найти в [шаблоне образца](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

После завершения развертывания ресурсов можно начать отсутчать узлы в типе основного узла из исходного кластера. По мере отключения узлов системные службы будут переходить на новый тип первичного узла, который был развернут на вышеупомянутом этапе.

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

После того, как все узлы отключены, системные службы будут работать по типу основного узла, который распределяется по зонам. Затем можно удалить отключенные узлы из кластера. После удаления узлов можно удалить исходные ресурсы набора IP, Load Balancer и виртуальной машины.

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

Затем следует удалить ссылки на эти ресурсы из развернутого шаблона «Менеджер ресурсов».

Заключительный шаг будет включать обновление имени DNS и общественного IP.

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
