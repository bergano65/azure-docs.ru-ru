---
title: Масштабирование типа узла Azure Service Fabric
description: Узнайте, как развертывать кластер Service Fabric путем добавления масштабируемого набора виртуальных машин.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: a8a8a432d4a11427f2c4f545a0907540af3112bc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056488"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-virtual-machine-scale-set"></a>Увеличение масштаба типа первичного узла кластера Service Fabric путем добавления масштабируемого набора виртуальных машин
В этой статье описывается, как масштабировать тип первичного узла кластера Service Fabric за счет увеличения количества ресурсов виртуальных машин. Кластер Service Fabric — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Компьютер или виртуальная машина, которая входит в состав кластера. Масштабируемые наборы виртуальных машин относятся к вычислительным ресурсам Azure. Их можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла, определенный в кластере Azure, [настроен как отдельный масштабируемый набор](service-fabric-cluster-nodetypes.md). Затем каждым типом узла можно управлять отдельно. После создания кластера Service Fabric можно масштабировать тип узла кластера вертикально (изменение ресурсов узлов) или обновить ОС виртуальных машин типа узла.  Кластер можно масштабировать в любое время, даже когда в нем выполняются рабочие нагрузки.  Вместе с кластером автоматически масштабируются ваши приложения.

> [!WARNING]
> Не пытайтесь выполнить процедуру масштабирования типа первичного узла, если кластер находится в неработоспособном состоянии, так как это приведет к дальнейшему дестабилизации работы кластера.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Процесс обновления размера и операционной системы виртуальных машин типа первичного узла
В этом разделе описана процедура обновления размера и операционной системы виртуальных машин типа первичного узла.  После обновления виртуальные машины типа первичного узла будут иметь размер Standard D4_V2 и работать под управлением Windows Server 2016 Datacenter с контейнерами.

> [!WARNING]
> Прежде чем выполнять эту процедуру для кластера рабочей среды, рекомендуется изучить образцы шаблонов и применить шаги для тестового кластера. Кластер будет недоступен в течение некоторого времени. НЕЛЬЗЯ вносить изменения в несколько масштабируемых наборов виртуальных машин, объявленных параллельно в одном и том же типе узла (NodeType). В этом случае необходимо выполнить раздельные операции развертывания для применения изменений к каждому набору виртуальных машин в этом типе узла.

1. Разверните исходный кластер с двумя типами узлов и двумя масштабируемыми наборами (один набор на один тип узла), используя эти примеры файлов [шаблона](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) и [параметров](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json).  Оба масштабируемых набора имеют размер Standard D2_V2 и работают под управлением Windows Server 2012 R2 Datacenter.  Подождите, пока на кластере будет выполнено базовое обновление.   
2. Разверните пример приложения с отслеживанием состояния в кластере (необязательно).
3. Когда вы решите обновить виртуальные машины типа первичного узла, добавьте новый масштабируемый набор в тип первичного узла, используя эти примеры файлов [шаблона](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) и [параметров](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json). В итоге в типе первичного узла будет два масштабируемых набора.  Системные службы и пользовательские приложения могут перемещаться между виртуальными машинами в двух разных масштабируемых наборах.  Виртуальные машины нового масштабируемого набора будут иметь размер Standard D4_V2 и работать под управлением Windows Server 2016 Datacenter с контейнерами.  Вместе с новым масштабируемым набором также будут добавлены новая подсистема балансировки нагрузки и общедоступный IP-адрес.  
    Чтобы найти новый масштабируемый набор в шаблоне, выполните поиск ресурса Microsoft.Compute/virtualMachineScaleSets, названного с использованием параметра *vmNodeType2Name*.  Новый масштабируемый набор добавляется в тип первичного узла с использованием параметра "properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef".
4. Проверьте состояние работоспособности кластера и убедитесь, что все узлы работоспособны.
5. Отключите узлы в старом масштабируемом наборе типа первичного узла, чтобы удалить узел. Вы можете отключить все узлы сразу, и операции будут поставлены в очередь. Дождитесь отключения всех узлов, что может занять некоторое время.  Когда старые узлы в типе узла будут отключены, системные службы и начальные узлы будут перемещены на виртуальные машины нового масштабируемого набора в типе первичного узла.
6. Удалите старый масштабируемый набор из типа первичного узла. (После отключения узлов, как показано на шаге 5, в колонке масштабируемого набора виртуальных машин на портале Azure отмените выделение узлов старого типа по одному.)
7. Удалите подсистему балансировки нагрузки, связанную со старым масштабируемым набором. Кластер недоступен, пока новый общедоступный IP-адрес и подсистема балансировки нагрузки настраиваются для нового масштабируемого набора.  
8. Сохраните параметры DNS общедоступного IP-адреса, связанного с масштабируемым набором старого типа первичного узла, в переменной и удалите этот общедоступный IP-адрес.
9. Замените параметры DNS общедоступного IP-адреса, связанного с масштабируемым набором нового типа первичного узла, параметрами DNS удаленного общедоступного IP-адреса.  Кластер теперь снова станет доступным.
10. Удалите состояние узлов из кластера.  Если уровень устойчивости старого масштабируемого набора был "Серебряный" или "Золотой", этот шаг выполняется системой автоматически.
11. Если на предыдущем шаге вы развернули приложение с отслеживанием состояния, убедитесь в правильности его работы.

## <a name="set-up-the-test-cluster"></a>Настройка тестового кластера

Сначала скачайте два набора файлов, которые понадобятся для работы с этим учебником, исходный [шаблон]() и [параметры]() и конечный [шаблон]() и [параметры]()

Затем войдите в учетную запись Azure.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

В этом учебнике рассматривается сценарий создания самозаверяющего сертификата. Чтобы использовать имеющийся сертификат из Azure Key Vault, пропустите приведенный ниже шаг, а затем выполните инструкции по [использованию существующего сертификата для развертывания кластера](./upgrade-managed-disks.md#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Создание самозаверяющего сертификата и развертывание кластера

Сначала назначьте переменные, которые потребуются для развертывания кластера Service Fabric. Измените значения `resourceGroupName`, `certSubjectName`, `parameterFilePath` и `templateFilePath` в соответствии со своей учетной записью и средой.

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> Перед выполнением команды для развертывания нового кластера Service Fabric убедитесь, что на локальном компьютере существует расположение `certOutputFolder`.

Затем откройте файл *Deploy-2NodeTypes-2ScaleSets.parameters.json* и измените значения параметров `clusterName` и `dnsName`, чтобы они соответствовали динамическим значениям, заданным в PowerShell. Сохраните эти изменения.

Затем разверните тестовый кластер Service Fabric.

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

После завершения развертывания найдите *PFX*-файл (`$certPfx`) на локальном компьютере и импортируйте его в хранилище сертификатов.

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Эта операция вернет отпечаток сертификата, который будет использоваться для подключения к новому кластеру и проверки его состояния работоспособности.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Подключение к новому кластеру и проверка состояния работоспособности

Подключитесь к кластеру и убедитесь, что все его узлы работоспособны (замените переменные `clusterName` и `thumb`, указав значения для своего кластера).

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Все готово к процедуре обновления.

## <a name="upgrade-the-primary-node-type-vms"></a>Обновление виртуальных машин типа первичного узла

Решив обновить виртуальные машины типа первичного узла, добавьте новый масштабируемый набор в тип первичного узла. В итоге в типе первичного узла будет два масштабируемых набора. Для демонстрации необходимых изменений предоставлены примеры файлов [шаблона](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) и [параметров](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json). Виртуальные машины нового масштабируемого набора будут иметь размер Standard D4_V2 и работать под управлением Windows Server 2016 Datacenter с контейнерами. Вместе с новым масштабируемым набором также будут добавлены новая подсистема балансировки нагрузки и общедоступный IP-адрес. 

Чтобы найти новый масштабируемый набор в шаблоне, выполните поиск ресурса Microsoft.Compute/virtualMachineScaleSets, названного с использованием параметра vmNodeType2Name. Новый масштабируемый набор добавляется в тип первичного узла с использованием параметра "properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef".

### <a name="deploy-the-updated-template"></a>Развертывание обновленного шаблона

Измените параметры `parameterFilePath` и `templateFilePath` соответствующим образом и выполните приведенную ниже команду.

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

После завершения развертывания снова проверьте работоспособность кластера и убедитесь, что все узлы (в исходном и новом масштабируемых наборах) работоспособны.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Перенос узлов в новый масштабируемый набор

Теперь можно приступить к отключению узлов исходного масштабируемого набора. Когда эти узлы будут отключены, системные службы и начальные узлы будут перенесены на виртуальные машины нового масштабируемого набора, так как он также отнесен к типу первичного узла.

Для масштабирования неосновных типов узлов на этом этапе можно изменить ограничение на размещение службы, чтобы включить новый масштабируемый набор виртуальных машин, а затем уменьшить число экземпляров масштабируемого набора виртуальных машин до нуля, по одному узлу за раз (чтобы удаление узла не повлияло на надежность кластера).

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Используйте Service Fabric Explorer, чтобы отслеживать перенос начальных узлов в новый масштабируемый набор и переход узлов в исходном масштабируемом наборе из состояния *Отключение* в состояние *Отключен*.

> [!NOTE]
> Выполнение операции отключения для всех узлов исходного масштабируемого набора может занять некоторое время. Для обеспечения согласованности данных в каждый момент времени допускается изменение только одного начального узла. Для изменения каждого начального узла требуется обновление кластера. Соответственно, для замены начального узла требуются два обновления кластера (одно для добавления и второе для удаления узла). Для обновления пяти начальных узлов в этом примере сценария потребуются 10 обновлений кластера.

## <a name="remove-the-original-scale-set"></a>Удаление исходного масштабируемого набора

После завершения операции отключения удалите масштабируемый набор.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

В Service Fabric Explorer удаленные узлы (и, соответственно, параметр *Состояние работоспособности кластера*) теперь будут отображаться в состоянии *Ошибка*.

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Удаление старой подсистемы балансировки нагрузки и обновление параметров DNS

Теперь мы можем удалить ресурсы, относящиеся к старому типу первичного узла, начиная с подсистемы балансировки нагрузки и старого общедоступного IP-адреса. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Далее мы обновим параметры DNS нового общедоступного IP-адреса, чтобы скопировать параметры общедоступного IP-адреса старого типа первичного узла.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Еще раз проверьте работоспособность кластера.

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Наконец, удалите состояние узла для каждого связанного узла. Если для старого масштабируемого набора использовался уровень устойчивости "Silver" или "Gold", система выполнит этот шаг автоматически.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

Теперь тип первичного узла кластера обновлен. Убедитесь, что развернутые приложения работают правильно и кластер работоспособен.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [добавить тип узла в кластер](virtual-machine-scale-set-scale-node-type-scale-out.md).
* Дополнительные сведения об [обновлениях приложений](service-fabric-concepts-scalability.md).
* [Руководство. Масштабирование кластера Service Fabric](service-fabric-tutorial-scale-cluster.md)
* [Масштабируйте кластер Azure программно](service-fabric-cluster-programmatic-scaling.md), используя свободный пакет SDK для вычислений Azure.
* [Увеличение и уменьшение масштаба автономного кластера](service-fabric-cluster-windows-server-add-remove-nodes.md).
