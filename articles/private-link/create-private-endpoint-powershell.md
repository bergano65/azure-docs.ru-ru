---
title: Создание частной конечной точки Azure с помощью Azure PowerShell | Документация Майкрософт
description: Сведения о частной ссылке Azure
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: f9a2bd4c4ec176e018948a7a5a01603d075a7ea2
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018014"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Создание частной конечной точки с помощью Azure PowerShell
Частная конечная точка — это фундаментальный Стандартный блок для частной ссылки в Azure. Она позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными с частными ресурсами. 

В этом кратком руководстве вы узнаете, как создать виртуальную машину в виртуальной сети Azure, сервере базы данных SQL с частной конечной точкой Azure, используя Azure PowerShell. Затем можно безопасно получить доступ к серверу базы данных SQL с виртуальной машины.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Создать группу ресурсов

Перед созданием ресурсов необходимо создать группу ресурсов, в которой размещается виртуальная сеть и частная конечная точка, с помощью [New-азресаурцеграуп](/powershell/module/az.resources/new-azresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *WestUS* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть
В этом разделе вы создадите виртуальную сеть и подсеть. Затем подсеть будет связана с виртуальной сетью.

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Создайте виртуальную сеть для частной конечной точки с помощью [New-азвиртуалнетворк](/powershell/module/az.network/new-azvirtualnetwork). В следующем примере создается виртуальная сеть с именем *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Добавление подсети

Azure развертывает ресурсы в подсеть в виртуальной сети, поэтому необходимо создать подсеть. Создайте конфигурацию подсети с именем *mySubnet* с помощью [Add-азвиртуалнетворксубнетконфиг](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). В следующем примере создается подсеть с именем *mySubnet* с флагом политики сети частной конечной точки, установленным в значение **Disabled (отключено**).

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet ` 
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Связывание подсети с виртуальной сетью

Вы можете записать конфигурацию подсети в виртуальную сеть с помощью [Set-азвиртуалнетворк](/powershell/module/az.network/Set-azVirtualNetwork). Эта команда создает подсеть:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину в виртуальной сети с помощью [New-AzVM](/powershell/module/az.compute/new-azvm). При запуске приведенной ниже команды запрашиваются учетные данные. Введите имя пользователя и пароль виртуальной машины:

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

Создайте сервер базы данных SQL с помощью команды New-Азсклсервер. Помните, что имя сервера базы данных SQL должно быть уникальным в пределах Azure, поэтому замените значение заполнителя в квадратных скобках своим уникальным значением:

$adminSqlLogin = "SqlAdmin" $password = "ChangeYourAdminPassword1"

$Server = New-азсклсервер-ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" ` -Location "WestCentralUS" '-складминистраторкредентиалс $ (New-Object-TypeName System. Management. Automation. PSCredential-ArgumentList $adminSqlLogin, $ ( ConvertTo-SecureString-String $password — AsPlainText-Force))

New-азсклдатабасе-ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"` -DatabaseName "МИДА"`
    -RequestedServiceObjectiveName "S0" ` -сампленаме "AdventureWorksLT"


## <a name="create-a-private-endpoint"></a>Создание частной конечной точки

Частная конечная точка для сервера базы данных SQL в виртуальной сети с помощью [New-азпривателинксервицеконнектион](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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

## <a name="configure-the-private-dns-zone"></a>Настройка зоны Частная зона DNS 
Создайте частную зону DNS для домена сервера базы данных SQL и создайте связь связи с виртуальной сетью: 

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

Чтобы получить общедоступный IP-адрес виртуальной машины, используйте [Get-азпублиЦипаддресс](/powershell/module/az.network/Get-AzPublicIpAddress) . В этом примере возвращается общедоступный IP-адрес виртуальной машины *myVM* :

```azurepowershell
Get-AzPublicIpAddress ` 
  -Name myPublicIpAddress ` 
  -ResourceGroupName myResourceGroup ` 
  | Select IpAddress 
```  
Откройте командную строку на локальном компьютере. Выполните команду mstsc.  <publicIpAddress>Замените общедоступным IP-адресом, возвращенным на последнем шаге: 


> [!NOTE]
> Если вы выполняли эти команды в командной строке PowerShell на локальном компьютере и используете модуль Az PowerShell 1.0 или более поздней версии, можете продолжить работу в этом интерфейсе.
```
mstsc /v:<publicIpAddress>
```

1. При появлении запроса выберите **подключить**. 
2. Введите имя пользователя и пароль, указанные при создании виртуальной машины.
  > [!NOTE]
  > Вам может потребоваться выбрать дополнительные варианты > использовать другую учетную запись, чтобы указать учетные данные, введенные при создании виртуальной машины. 
3. Нажмите кнопку **ОК**. 
4. Вы можете получить предупреждение о сертификате. В этом случае выберите **Да** или **продолжить**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Доступ к серверу базы данных SQL в частном порядке с виртуальной машины

1. В удаленный рабочий стол myVM откройте PowerShell.
2. Ентернслукуп myserver.database.windows.net вы получите примерно следующее сообщение:  Сервер Повершеллкопи Azure:  Неизвестный адрес:  168.63.129.16 неполномочный ответ:  Имя: myserver.privatelink.database.windows.net адрес:  псевдонимы 10.0.0.5: myserver.database.windows.net
3. Установить SQL Server Management Studio
4. В окне Подключение к серверу введите или выберите следующие сведения: Параметр тип сервера значение выберите ядро СУБД.
      Имя сервера выберите myserver.database.windows.net Username введите имя пользователя, указанное во время создания.
      Пароль введите пароль, указанный во время создания.
      Запомнить пароль выберите Да.
5. Выберите Подключить.
6. Просмотр баз данных из левого меню. 
7. При необходимости Создание или запрос информации из MyDatabase
8. Закройте подключение к удаленному рабочему столу *myVM*. 

## <a name="clean-up-resources"></a>Очистка ресурсов 
Завершив использование частной конечной точки, сервера базы данных SQL и виртуальной машины, удалите группу ресурсов и все ресурсы, используя команду [Remove-азресаурцеграуп](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения о [частной ссылке Azure](private-link-overview.md)
