---
title: Развертывание и настройка брандмауэра Azure с помощью Azure PowerShell
description: В этой статье вы узнаете, как развертывать и настраивать брандмауэр Azure с помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7f48012ca1f97c2e28380d95da37863c4bc17f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73831839"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Развертывание и настройка брандмауэра Azure с помощью Azure PowerShell

Управление доступом исходящих сетевых подключений является важной частью общего плана безопасности сети. Например, вы можете ограничить доступ к веб-сайтам или исходящим IP-адресам и портам, которые могут быть доступными.

Вы можете управлять доступом к исходящей сети из подсети Azure только с помощью Брандмауэра Azure. Брандмауэр Azure позволяет настроить:

* Правила приложений, определяющие полные доменные имена (FQDN), к которым можно получить доступ из подсети.
* Правила сети, определяющие адрес источника, протокол, порт назначения и адрес назначения.

При маршрутизации трафика на брандмауэр, используемый в качестве шлюза по умолчанию, для подсети к трафику применяются настроенные правила брандмауэра.

Для этой статьи создается упрощенный единый VNet с тремя подсетями для простого развертывания. Для развертываний в рабочей среде рекомендуется использовать [звездообразную модель](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), в которой брандмауэр находится в собственной виртуальной сети. Серверы рабочей нагрузки размещены в одноранговых виртуальных сетях в одном регионе с одной или несколькими подсетями.

* **AzureFirewallSubnet** — в этой подсети находится брандмауэр.
* **Workload-SN** — в этой подсети находится сервер рабочей нагрузки. Трафик этой подсети проходит через брандмауэр.
* **Jump-SN** — в этой подсети находится сервер перехода. Сервер перехода имеет общедоступный IP-адрес, к которому можно подключиться с помощью удаленного рабочего стола. Затем вы можете подключиться к серверу рабочей нагрузки (используя другой удаленный рабочий стол).

![Инфраструктура сети, используемая в руководстве](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка тестовой сетевой среды;
> * развертывание брандмауэра;
> * создание маршрута по умолчанию;
> * настройка правила приложения для предоставления доступа к [www.google.com]\(www.google.com);
> * настройка сетевых правил для предоставления доступа к внешним DNS-серверам;
> * тестирование брандмауэра.

Если вы предпочитаете, вы можете завершить эту процедуру с помощью [портала Azure](tutorial-firewall-deploy-portal.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Эта процедура требует, чтобы вы запускали PowerShell локально. Необходимо установить модуль Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). После проверки версии PowerShell выполните командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="set-up-the-network"></a>Настройка сети

Сначала создайте группу ресурсов, необходимых для развертывания брандмауэра. Затем создайте виртуальную сеть, подсети и тестовые серверы.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов содержит все ресурсы для развертывания.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Создание виртуальной сети

Эта виртуальная сеть имеет три подсети:

> [!NOTE]
> Размер подсети AzureFirewallSubnet равен /26. Дополнительные сведения о размере подсети см. в статье с [часто задаваемыми вопросами о Брандмауэре Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
Создайте виртуальную сеть:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Теперь создайте виртуальные машины для перехода и рабочей нагрузки и поместите их в соответствующие подсети.
При появлении запроса введите имя пользователя и пароль для виртуальной машины.

Создайте виртуальную машину Srv-Jump.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Создайте виртуальную рабочую нагрузку без публичного IP-адреса.
При появлении запроса введите имя пользователя и пароль для виртуальной машины.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Развертывание брандмауэра

Теперь разместите брандмауэр в виртуальной сети.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Запишите частный IP-адрес. Вы будете использовать его позже при создании маршрута по умолчанию.

## <a name="create-a-default-route"></a>создание маршрута по умолчанию;

Создание таблицы с отключением распространения маршрута BGP

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Настройка правила приложения

Правило приложения позволяет доступ к www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Брандмауэр Azure содержит встроенную коллекцию правил для целевых полных доменных имен инфраструктуры, которые разрешены по умолчанию. Эти доменные имена предназначены для платформы и не могут использоваться для других целей. См. дополнительные сведения об [FQDN инфраструктуры](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Настройка правила сети

Правило сети позволяет доступ к двум IP-адресам в порту 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Изменение первичного и вторичного адресов DNS для сетевого интерфейса **Srv-Work**

Для целей тестирования в этой процедуре назначаем первичные и вторичные DNS-адреса сервера. Это не является общим требованием службы "Брандмауэр Azure".

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>тестирование брандмауэра.

Теперь проверьте брандмауэр, чтобы убедиться, что он работает должным образом.

1. Обратите внимание на частный IP-адрес виртуальной машины **Srv-Work:**

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Подключите удаленный рабочий стол к виртуальной машине **Srv-Jump** и выполните вход. Оттуда откройте удаленное подключение к рабочему столу на частный IP-адрес **Srv-Work** и вопийте.

3. На **SRV-Work**откройте окно PowerShell и запустите следующие команды:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Обе команды должны отвечать на ответы, показывая, что ваши dNS-запросы проходят через брандмауэр.

1. Выполните следующие команды:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Запросы `www.google.com` должны быть `www.microsoft.com` успешными, а запросы должны сбой. Это показывает, что правила брандмауэра работают в обычном режиме.

Итак, теперь вы убедились в том, что правила брандмауэра работают:

* Вы можете разрешать имена DNS с помощью настроенного внешнего DNS-сервера.
* Вы можете перейти только к одному разрешенному имени FQDN.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете сохранить ресурсы брандмауэра для следующего руководства, или, если это больше не нужно, удалить группу ресурсов **Test-FW-RG,** чтобы удалить все ресурсы, связанные с брандмауэром:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Дальнейшие действия

* [Руководство. Журналы мониторинга брандмауэра Azure](./tutorial-diagnostics.md)
