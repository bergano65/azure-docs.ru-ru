---
title: Создайте частную конечную точку Azure с помощью Azure PowerShell Документы Майкрософт
description: Узнайте больше о частной ссылке Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430345"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Создание частной конечной точки с помощью Azure PowerShell
Частная конечная точка — ключевой компонент для построения частной ссылки в Azure. Это позволяет ресурсам Azure, таким как виртуальные машины (VM), обмениваться данными в частном порядке с ресурсами частной ссылки. 

В этом кратком руководстве вы узнаете, как создать виртуальную машину в виртуальной сети Azure и сервер базы данных SQL с частной конечной точкой Azure, используя Azure PowerShell. Затем вы сможете безопасно получить доступ к серверу базы данных SQL с виртуальной машины.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Прежде чем создавать ресурсы, необходимо создать группу ресурсов, в размещении - виртуальную сеть и частную конечную точку с [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Следующий пример создает группу ресурсов под названием *myResourceGroup* в месте *назападе:*

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Создайте виртуальную сеть
В этом разделе вы создаете виртуальную сеть и подсеть. Далее вы связываете подсеть с вашей виртуальной сетью.

### <a name="create-a-virtual-network"></a>Создайте виртуальную сеть

Создайте виртуальную сеть для вашей частной конечной точки с [Помощью New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork) Следующий пример создает виртуальную сеть под названием *MyVirtualNetwork:*
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Добавить подсеть

Azure развертывает ресурсы в подсеть в виртуальной сети, поэтому необходимо создать подсеть. Создайте конфигурацию подсети под названием *mySubnet* с [помощью Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Следующий пример создает подсеть под названием *mySubnet* с частным флагом политики сети конечных точек, установленным для **инвалидов.**

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> Легко перепутать `PrivateEndpointNetworkPoliciesFlag` параметр с другим доступным `PrivateLinkServiceNetworkPoliciesFlag`флагом, потому что они оба длинные слова и имеют схожий внешний вид.  Убедитесь, что вы используете правильный, `PrivateEndpointNetworkPoliciesFlag`.

### <a name="associate-the-subnet-to-the-virtual-network"></a>Связать подсеть с виртуальной сетью

Вы можете написать конфигурацию подсети в Виртуальную сеть с [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Эта команда создает подсеть:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте VM в виртуальной сети с [New-AzVM](/powershell/module/az.compute/new-azvm). При запуске приведенной ниже команды запрашиваются учетные данные. Введите имя пользователя и пароль виртуальной машины:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

Параметр `-AsJob` создает виртуальную машину в фоновом режиме. Перейдите к следующему шагу.

Когда Azure начнет создание виртуальной машины в фоновом режиме, вы получите результат, аналогичный следующему:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Создание сервера базы данных SQL 

Создайте сервер базы данных S'L, используя команду New-AzSqlServer. Помните, что имя вашего сервера базы данных S'L должно быть уникальным в Azure, поэтому замените значение заполнителя в скобках на ваше собственное уникальное значение:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Создание частной конечной точки

Частная конечная точка для сервера базы данных S'L в вашей виртуальной сети с [New-AzPrivateLinkServiceConnection:](/powershell/module/az.network/New-AzPrivateLinkServiceConnection) 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS 
Создайте частную dNS-зону для домена S'L Database Server и создайте связующее звено с виртуальной сетью: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress). Этот пример возвращает общедоступный IP-адрес *myVM* VM:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Откройте командную строку на локальном компьютере. Выполнить команду mstsc. Замените <publicIpAddress> общедоступным IP-адресом, полученным на последнем шаге: 


> [!NOTE]
> Если вы выполняли эти команды в командной строке PowerShell на локальном компьютере и используете модуль Az PowerShell 1.0 или более поздней версии, можете продолжить работу в этом интерфейсе.
```
mstsc /v:<publicIpAddress>
```

1. При появлении запроса выберите **Подключиться**. 
2. Введите имя пользователя и пароль, указанные при создании виртуальной машины.
  > [!NOTE]
  > Возможно, вам придется выбрать больше вариантов > использовать другую учетную запись, чтобы указать учетные данные, которые вы ввели при создании VM. 
  
3. Нажмите кнопку **ОК**. 
4. Вы можете получить предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Доступ с виртуальной машины к серверу базы данных SQL в частном порядке

1. Откройте PowerShell на удаленном рабочем столе myVm.
2. Введите `nslookup myserver.database.windows.net`. 

    Должно появиться сообщение следующего вида:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Установите SQL Server Management Studio
4. При подключении к серверу введите или выберите эту информацию: Установка типа Value Server Select Database Engine.
      Имя сервера Выберите myserver.database.windows.net имя пользователя Введите имя пользователя, предоставленное во время создания.
      Пароль Введите пароль, предоставленный во время создания.
      Помните, что пароль Выберите Да.
5. Выберите Подключиться.
6. Просматривайте базы данных из левого меню. 
7. (По желанию) Создание или запрос информации из базы данных
8. Закройте удаленное подключение к рабочему столу *на myVM.* 

## <a name="clean-up-resources"></a>Очистка ресурсов 
Когда вы закончите использовать частную конечную точку, сервер базы данных s'L и VM, используйте [Remove-AzResourceGroup,](/powershell/module/az.resources/remove-azresourcegroup) чтобы удалить группу ресурсов и все ресурсы, которые она имеет:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о службе [Приватный канал Azure](private-link-overview.md)
