---
title: Масштабирование типа первичного узла Azure Service Fabric
description: Узнайте, как масштабировать кластер Service Fabric, добавив тип узла.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: eecf398359470f6e5e151c53eb63b3cb56efbe39
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056760"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>Увеличение масштаба типа первичного узла кластера Service Fabric путем добавления типа узла
В этой статье описывается, как масштабировать тип первичного узла кластера Service Fabric путем добавления дополнительного типа узла в кластер. Кластер Service Fabric — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Компьютер или виртуальная машина, которая входит в состав кластера. Масштабируемые наборы виртуальных машин относятся к вычислительным ресурсам Azure. Их можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла, определенный в кластере Azure, [настроен как отдельный масштабируемый набор](service-fabric-cluster-nodetypes.md). Затем каждым типом узла можно управлять отдельно.

Примеры шаблонов, приведенные в следующем руководстве, можно найти здесь: [Service Fabric примеры масштабирования типа первичного узла](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> Не пытайтесь выполнить процедуру масштабирования типа первичного узла, если кластер находится в неработоспособном состоянии, так как это приведет к дальнейшему дестабилизации работы кластера.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Процесс обновления размера и операционной системы типа первичного узла
Ниже приведен процесс обновления размера виртуальной машины и операционной системы для виртуальных машин типа первичного узла.  После обновления виртуальные машины типа первичного узла имеют стандартный размер D4_V2 и работают под Windows Server 2019 Datacenter с контейнерами.

> [!WARNING]
> Прежде чем выполнять эту процедуру для кластера рабочей среды, рекомендуется изучить образцы шаблонов и применить шаги для тестового кластера. Кластер также может быть недоступен в течение короткого периода времени. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>Развертывание начального кластера Service Fabric 
Если вы хотите следовать этому примеру, разверните исходный кластер с одним типом первичного узла и одним масштабируемым набором [Service Fabric-начальный кластер](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). Этот шаг можно пропустить, если уже развернут существующий Service Fabric кластер. 

1. Войдите в свою учетную запись Azure. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Создание группы ресурсов 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName
    -Location $location
```
3. Заполните значения параметров в файлах шаблонов. 
4. Разверните кластер в группе ресурсов, созданной на шаге 2. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Добавление нового типа первичного узла в кластер
> [!Note]
> Ресурсы, созданные в следующих шагах, станут новым типом первичного узла в кластере после завершения операции масштабирования. Убедитесь, что используются уникальные имена из начальной подсети, общедоступного IP-адреса, Load Balancer, масштабируемого набора виртуальных машин и типа узла. 

Чтобы найти шаблон, выполните все приведенные ниже действия. [Service Fabric — новый кластер типов узлов](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json). Следующие шаги содержат фрагменты частичного ресурса, которые выделяют изменения в новых ресурсах.  

1. Создайте новую подсеть в существующей виртуальной сети.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Создайте новый ресурс общедоступного IP-адреса с уникальным domainNameLabel. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Создайте новый ресурс Load Balancer, который зависит от созданного ранее общедоступного IP-адреса. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Создайте новый масштабируемый набор виртуальных машин, использующий новый номер SKU виртуальной машины и SKU ОС, до которого нужно масштабировать. 

Тип узла ref 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

SKU виртуальной машины
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU ОС 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```
5. Добавьте новый тип узла в кластер, который ссылается на масштабируемый набор виртуальных машин, созданный ранее. Свойству **"свойство" на данном** типе узла должно быть присвоено значение true. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Разверните обновленный шаблон ARM. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

После завершения развертывания кластер Service Fabric теперь будет иметь два типа узлов. 

### <a name="remove-the-existing-node-type"></a>Удалить существующий тип узла 
После завершения развертывания ресурсов можно приступить к отключению узлов в исходном типе первичного узла. После отключения узлов системные службы будут перенесены на новый тип первичного узла, который был развернут на предыдущем шаге.

1. Задайте для свойства "Тип первичного узла" в ресурсе кластера Service Fabric значение false. 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. Разверните шаблон с обновленным свойством во всем исходном типе узла. Шаблон с основным флагом, установленным в значение false на исходном типе узла, можно найти здесь: [Service Fabric-PRIMARY node Type false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. Отключите узлы в типе узла 0. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* Для устойчивости к бронзовому дождитесь перехода всех узлов в состояние отключено.
* Для уровня устойчивости "серебро" и "Gold" некоторые узлы будут отключены, а остальные будут находиться в состоянии отключения. Проверьте вкладку Сведения узлов в отключенном состоянии, если они все заблокированы для обеспечения кворума для секций службы инфраструктуры, то можно продолжать работу.

> [!Note]
> Выполнение этого шага может занять некоторое время. 

4. Останавливает данные на узле типа 0. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. Освобождение узлов в исходном масштабируемом наборе виртуальных машин 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> Шаги 6 и 7 необязательны, если вы уже используете общедоступный IP-адрес SKU уровня "Стандартный" и балансировщик нагрузки "Стандартный". В этом случае в одной подсистеме балансировки нагрузки может быть несколько масштабируемых наборов виртуальных машин или типов узлов. 

6. Теперь можно удалить исходный IP-адрес и Load Balancer ресурсы. На этом шаге будет также обновлен DNS-имя. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. Обновите конечную точку управления в кластере, чтобы она ссылалась на новый IP-адрес. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. Удаление состояния узла из типа узла 0.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. Удалите ссылку на исходный тип узла из ресурса Service Fabric в шаблоне ARM. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
Только для отказоустойчивых и более высоких кластеров, обновите ресурс кластера в шаблоне и настройте политики исправности, чтобы игнорировать структуру:/работоспособность системного приложения, добавив АппликатионделтахеалсполиЦиес в свойствах ресурса кластера, как указано ниже. Приведенная ниже политика должна пропускать существующие ошибки, но не разрешать новые ошибки работоспособности.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. Удалите все другие ресурсы, связанные с исходным типом узла, из шаблона ARM. См. раздел [Service Fabric — новый кластер типов узлов](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) для шаблона со всеми удаленными исходными ресурсами.

11. Разверните измененный шаблон Azure Resource Manager. * * Этот шаг займет некоторое время, обычно не более двух часов. Это обновление изменит параметры на Инфраструктуресервице, поэтому требуется перезагрузка узла. В этом случае Форцерестарт игнорируется. Параметр upgradeReplicaSetCheckTimeout указывает максимальное время, которое Service Fabric ожидает, когда Секция находится в надежном состоянии, если она еще не находится в надежном состоянии. После проверки безопасности для всех секций на узле Service Fabric переходит к обновлению на этом узле. Значение параметра upgradeTimeout может быть сокращено до 6 часов, но для максимальной безопасности следует использовать 12 часов.
Затем убедитесь, что ресурс Service Fabric на портале отображается как готовый. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Теперь тип первичного узла кластера обновлен. Убедитесь, что развернутые приложения работают правильно и кластер работоспособен.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [добавить тип узла в кластер](virtual-machine-scale-set-scale-node-type-scale-out.md).
* Дополнительные сведения об [обновлениях приложений](service-fabric-concepts-scalability.md).
* [Руководство. Масштабирование кластера Service Fabric](service-fabric-tutorial-scale-cluster.md)
* [Масштабируйте кластер Azure программно](service-fabric-cluster-programmatic-scaling.md), используя свободный пакет SDK для вычислений Azure.
* [Увеличение и уменьшение масштаба автономного кластера](service-fabric-cluster-windows-server-add-remove-nodes.md).
