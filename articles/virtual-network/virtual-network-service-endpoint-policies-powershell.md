---
title: Ограничьте эксфильтрацию данных для хранения данных Azure - Azure PowerShell
description: В этой статье вы узнаете, как ограничить и ограничить эксфильтрацию виртуальных сетевых данных ресурсами хранения azure с помощью политик идентимативных сетевых сетевых сетевых сетевых сетевых сетей с помощью Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78253030"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Управление эксфильтрацией данных для учетных записей хранения Azure с помощью политик иного направления виртуальной сетевой системы с помощью Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Политики виртуальной точки зрения сетевого обслуживания позволяют применять элементы управления доступом к учетным записям хранения Azure из виртуальной сети над конечными точками обслуживания. Это ключ к обеспечению безопасности рабочих нагрузок, управлению тем, какие учетные записи хранилища разрешены и где разрешена эксфильтрация данных.
Вы узнаете, как выполнять следующие задачи:

* Создайте виртуальную сеть.
* Добавьте подсеть и включите конечную точку службы для хранения Azure.
* Создайте две учетные записи хранилища Azure и позвольте сети получить к нему доступ из созданной выше подсети.
* Создайте политику конечной точки службы, чтобы разрешить доступ только к одной из учетных записей хранилища.
* Развертывание виртуальной машины (VM) в подсеть.
* Подтвердите доступ к разрешенной учетной записи хранилища из подсети.
* Подтвердить доступ к неразрешенной учетной записи хранилища из подсети.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально для работы с этой статьей, вам понадобится модуль Azure PowerShell 1.0.0 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-virtual-network"></a>Создание виртуальной сети

Перед созданием виртуальной сети необходимо создать для нее группу ресурсов и другие компоненты, указанные в этой статье. Создайте группу ресурсов с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). В следующем примере создается группа ресурсов *myResourceGroup*. 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Создайте виртуальную сеть с помощью командлета [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). В следующем примере создается виртуальная сеть с именем *myVirtualNetwork* и префиксом адреса *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Включение конечной точки службы

Создайте подсеть в виртуальной сети. В этом примере создается подсеть *Private* с конечной точкой службы для службы *Microsoft.Storage*. 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Ограничение доступа к сети для подсети

Создание правил безопасности группы сетевой безопасности с [помощью New-AzNetworkSecurityRuleConfig.](/powershell/module/az.network/new-aznetworksecurityruleconfig) Приведенное ниже правило разрешает исходящий доступ к общедоступным IP-адресам, назначенным службе хранилища Azure. 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Следующее правило запрещает доступ ко всем общедоступным IP-адресам. Предыдущее правило переопределяет это правило ввиду более высокого приоритета. Оно предоставляет доступ к общедоступным IP-адресам службы хранилища Azure.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Следующее правило разрешает для подсети входящий трафик протокола удаленного рабочего стола (RDP), поступающий из любого расположения. В подсети разрешены подключения к удаленному рабочему столу, чтобы позже можно было подтвердить сетевой доступ к ресурсу.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Создайте группу безопасности сети с помощью командлета [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). В следующем примере создается группа безопасности сети *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Связать группу сетевой безопасности с *частной* подсетью с [Set-AzVirtualNetworkSubnetConfig,](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) а затем написать конфигурацию подсети в виртуальную сеть. В следующем примере подсеть *Private* привязывается к группе безопасности сети *myNsgPrivate*.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Ограничьте доступ к учетным записям хранения azure

Действия, необходимые для ограничения сетевого доступа к ресурсам, созданным с помощью служб Azure, использующих конечные точки службы, отличаются в зависимости службы. Ознакомьтесь с документацией по отдельным службам, чтобы получить точные инструкции для них. В оставшейся части этой статьи в качестве примера приведены инструкции по ограничению сетевого доступа для учетной записи хранения Azure.

### <a name="create-two-storage-accounts"></a>Создание двух учетных записей хранилища

Создайте учетную запись хранения данных Azure с [помощью New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount)

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

После создания учетной записи хранилища извлеките ключ для учетной записи хранилища в переменную с [Get-AzStorageAccountAccountKey:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

Этот ключ потребуется для создания файлового ресурса на более позднем этапе. Введите `$storageAcctKey` и запишите значение, так как позже его потребуется ввести вручную при подключении файлового ресурса как диска на виртуальной машине.

Теперь повторите вышеперечисленные шаги, чтобы создать вторую учетную запись хранения.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Кроме того, извлеките ключ учетной записи хранилища из этой учетной записи для последующего использования для создания файла.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Создание доли файлов в каждой учетной записи хранилища

Создайте контекст для учетной записи хранения данных и ключов с [помощью New-AzStorageContext.](/powershell/module/az.storage/new-AzStoragecontext) Этот контекст инкапсулирует имя учетной записи хранения и ее ключ.

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Создайте файл с [помощью New-AzStorageShare:](/powershell/module/az.storage/new-azstorageshare)

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Запретить доступ сети к учетным записям хранения

По умолчанию учетные записи хранения принимают сетевые подключения клиентов в любой сети. Чтобы ограничить доступ к выбранным сетям, измените действие по умолчанию на *Отрицание* с [помощью Update-AzStorageAccountNetworkRuleSetSet.](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) После запрещения сетевого доступа учетная запись хранения не будет доступна из любой сети.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Включить доступ к сети только из подсети VNet

Извлеките созданную виртуальную сеть с [помощью Get-AzVirtualNetwork,](/powershell/module/az.network/get-azvirtualnetwork) а затем извлеките частный объект субсети в переменную с [Get-AzVirtualNetworkSubnetConfig:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Разрешить доступ к учетной записи хранилища из *частной* подсети с [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Применить политику, чтобы разрешить доступ к действительной учетной записи хранилища

Чтобы убедиться, что пользователи виртуальной сети могут получить доступ только к безопасным и разрешенным учетным записям Хранилища Azure, можно создать политику конечной точки службы со списком разрешенных учетных записей хранилища в определении. Затем эта политика применяется к подсети виртуальной сети, которая подключена к хранению через конечные точки службы.

### <a name="create-a-service-endpoint-policy"></a>Создание политики конечной точки службы.

В этом разделе создается определение политики со списком разрешенных ресурсов для доступа к конечной точке службы

Извлечение идентификатора ресурса для первой (разрешенной) учетной записи хранения 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Создайте определение политики, чтобы вышеуказанный ресурс

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Создание политики конечных точек службы с использованием определения политики, созданного выше

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Связать политику конечных точек службы с виртуальной сетевой подсетью

После создания политики конечных точек службы вы свяжете ее с целевой подсетью с конфигурацией конечных точек службы для Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Проверка ограничения доступа к учетным записям хранения Azure

### <a name="deploy-the-virtual-machine"></a>Развертывание виртуальной машины

Чтобы протестировать доступ сети к учетной записи хранилища, разместите VM в подсети.

Создайте виртуальную машину в *частной* подсети с [New-AzVM.](/powershell/module/az.compute/new-azvm) При выполнении следующей команды будут запрошены учетные данные. В качестве вводимых значений указываются имя пользователя и пароль для виртуальной машины. Параметр `-AsJob` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Будут возвращены выходные данные, как показано ниже.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Подтвердить доступ к *разрешенной* учетной записи хранилища

Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). Приведенный ниже пример возвращает общедоступный IP-адрес виртуальной машины *myVmPrivate*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Замените `<publicIpAddress>` в следующей команде общедоступным IP-адресом, возвращенным предыдущей командой, а затем введите следующую команду:

```powershell
mstsc /v:<publicIpAddress>
```

Будет создан и скачан на ваш компьютер файл протокола удаленного рабочего стола (RDP-файл). Откройте этот RDP-файл. При появлении запроса выберите **Подключиться**. Введите имя пользователя и пароль, указанные при создании виртуальной машины. Возможно, потребуется выбрать **More choices** (Дополнительные варианты), а затем **Use a different account** (Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины. Нажмите кнопку **ОК**. При входе в систему может появиться предупреждение о сертификате. Если вы получили предупреждение, выберите **Да** или **Продолжить**.

На *myVmPrivate* VM отображают долю файла Azure с разрешенной учетной записи хранилища, чтобы управлять с помощью PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell вернет выходные данные, как в следующем примере.

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Файловый ресурс Azure успешно подключен как диск Z.

Закройте сеанс удаленного рабочего стола с виртуальной машиной *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Подтверждение отказа в доступе к *неразрешенной* учетной записи хранилища

На том же *myVmPrivate* VM попытайтесь сопоставить совместное представление файлов Azure для привода X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Доступ к файловому ресурсу будет запрещен, и отобразится сообщение об ошибке `New-PSDrive : Access is denied`. Доступ указано из-за того, что учетная запись *хранилища, не* указанная в списке разрешенных ресурсов, в политике конечных точек службы. 

Закройте сеанс удаленного рабочего стола с виртуальной машиной *myVmPublic*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы применили политику конечных точек службы в отношении точки фактической сетевой службы Azure к хранилищем Azure. Вы создали учетные записи хранилища Azure и ограниченный доступ к сети только к определенным учетным записям хранения (и, таким образом, отрицали другие) из виртуальной сетевой подсети. Чтобы узнать больше о политиках [Service endpoints policies overview](virtual-network-service-endpoint-policies-overview.md)конечных точек службы, см.
