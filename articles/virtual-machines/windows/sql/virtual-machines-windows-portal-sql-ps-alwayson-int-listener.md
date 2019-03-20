---
title: Настройка прослушивателей групп доступности AlwaysOn в Microsoft Azure | Документы Майкрософт
description: Настройте прослушиватели группы доступности в модели Azure Resource Manager с использованием внутреннего балансировщика нагрузки с одним или несколькими IP-адресами.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.openlocfilehash: 1b15a3966c6e408fb17655b112c1ec900bd86c5f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999792"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Настройка одного или нескольких прослушивателей групп доступности AlwaysOn в модели Resource Manager
В этой статье вы узнаете, как:

* создать внутренний балансировщик нагрузки для групп доступности SQL Server с помощью командлетов PowerShell;
* добавить дополнительные IP-адреса в подсистему балансировки нагрузки для поддержки нескольких групп доступности. 

Прослушиватель группы доступности — это виртуальная сеть, к которой подключаются клиенты, чтобы получить доступ к базе данных. На виртуальных машинах Azure балансировщик нагрузки содержит IP-адрес прослушивателя. Балансировщик нагрузки направляет трафик в экземпляр SQL Server, который прослушивает порт пробы. Обычно группа доступности использует внутреннюю подсистему балансировки нагрузки. В такой системе можно разместить один или несколько IP-адресов. Каждый IP-адрес использует определенный порт пробы. В этой статье описывается, как использовать PowerShell для создания подсистемы балансировки нагрузки или добавления IP-адресов в имеющуюся подсистему балансировки нагрузки для групп доступности SQL Server. 

Возможность назначения нескольких IP-адресов для внутреннего балансировщика нагрузки впервые представлена в Azure. Она доступна только в модели Resource Manager. Для этого необходимо развернуть группу доступности SQL Server на виртуальных машинах Azure с использованием модели Resource Manager. Обе виртуальные машины SQL Server должны принадлежать к одной и той же группе доступности. Вы можете использовать [шаблон Майкрософт](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) , чтобы автоматически создать группу доступности в Azure Resource Manager. Этот шаблон также автоматически создает внутренний балансировщик нагрузки. При необходимости можно [вручную настроить группу доступности AlwaysOn](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Здесь предполагается, что группы доступности уже настроены.  

Связанные разделы включают:

* [Настройка групп доступности AlwaysOn в виртуальной машине Azure (графический пользовательский интерфейс)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Настройка подключения между виртуальными сетями с помощью Azure Resource Manager и PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Проверка версии PowerShell

Примеры из этой статьи были проверены с помощью модуля Azure PowerShell 5.4.1.

Версия проверяемого модуля PowerShell соответствует 5.4.1 или более поздней версии.

См. статью [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Настройка брандмауэра Windows

Настройте брандмауэр Windows для разрешения доступа к SQL Server. Правила брандмауэра разрешают TCP-подключения к портам, используемым экземпляром SQL Server и пробой прослушивателя. Дополнительные сведения см. в разделе [Настройка брандмауэра Windows для доступа к ядру СУБД](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Создайте правило входящего трафика для порта SQL Server и порта пробы.

Ограничивая доступ с помощью группы безопасности сети Azure, убедитесь, что правило разрешения включает IP-адреса виртуальных машин SQL Server и динамический IP-адрес балансировщика нагрузки для прослушивателя AG и IP-адрес основного кластера, если они применяются.

## <a name="determine-the-load-balancer-sku-required"></a>Определение требований для номера SKU в подсистеме балансировки нагрузки

Доступны два номера SKU [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md): "Базовый" и "Стандартный". Рекомендуется использовать Load Balancer (цен. категория "Стандартный"). Если виртуальные машины находятся в группе доступности, разрешено использовать Load Balancer (цен. категория "Базовый"). Для Load Balancer (цен. категория "Стандартный") требуется, чтобы во всех виртуальных машинах использовались стандартные IP-адреса.

В текущем [шаблоне Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) для группы доступности используется Load Balancer (цен. категория "Базовый") с базовыми IP-адресами.

В данных примерах статьи приведена Load Balancer (цен. категория "Стандартный"). Сценарий `-sku Standard` включен в примеры.

```PowerShell
$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Чтобы создать Load Balancer (цен. категория "Базовый"), из строки ее создания необходимо удалить сценарий `-sku Standard`. Например: 

```PowerShell
$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Пример сценария. Создание внутреннего балансировщика нагрузки с помощью PowerShell

> [!NOTE]
> Если вы создали группу доступности с помощью [шаблона Майкрософт](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), внутренняя подсистема балансировки нагрузки уже создана.

Следующий сценарий PowerShell создает внутренний балансировщик нагрузки, задает IP-адрес для нее, а также настраивает правила балансировки нагрузки. Для запуска сценария откройте интегрированную среду сценариев Windows PowerShell и вставьте его в область сценариев. Войдите в PowerShell, используя командлет `Connect-AzAccount`. Если подписок Azure несколько, для установки подписки используется `Select-AzSubscription ` . 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> Пример сценария. Добавление IP-адреса в имеющуюся подсистему балансировки нагрузки с помощью PowerShell
Чтобы применить несколько групп доступности, добавьте другой IP-адрес в подсистему балансировки нагрузки. Каждому IP-адресу требуется собственное правило балансировки нагрузки, порт пробы и внешний порт.

Внешний порт — это порт, используемый приложениями для подключения к экземпляру SQL Server. Один и тот же внешний порт может использоваться для IP-адресов различных групп доступности.

> [!NOTE]
> Для групп доступности SQL Server для каждого IP-адреса требуется определенный порт пробы. Например, если один IP-адрес в балансировщике нагрузки использует порт пробы 59999, другие IP-адреса не могут использовать этот порт пробы.

* Дополнительные сведения об ограничениях см. в пункте **Частный внешний IP-адрес на балансировщик нагрузки** раздела [Ограничения сети — Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Дополнительные сведения об ограничениях групп доступности см. в разделе [Ограничения (группы доступности)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Следующий сценарий добавляет новый IP-адрес в имеющийся балансировщик нагрузки. Внутренняя подсистема балансировки нагрузки использует порт прослушивателя для внешнего порта балансировки нагрузки. Это может быть порт, который прослушивает SQL Server. Для экземпляров SQL Server по умолчанию — это порт 1433. Для правила балансировки нагрузки группы доступности требуется плавающий IP-адрес (прямой ответ от сервера), поэтому внутренний порт не отличается от внешнего порта. Обновите переменные для среды. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Настройка прослушивателя

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Настройка порта прослушивателя в SQL Server Management Studio

1. Запустите SQL Server Management Studio и подключитесь к основной реплике.

1. Перейдите в раздел **Высокий уровень доступности AlwaysOn** | **Группы доступности** | **Прослушиватели группы доступности**. 

1. Теперь вы увидите имя прослушивателя, созданного в диспетчере отказоустойчивости кластеров. Щелкните правой кнопкой мыши имя прослушивателя и выберите **Свойства**.

1. В поле **Порт** укажите номер порта для прослушивателя группы доступности с помощью использованного ранее параметра $EndpointPort (по умолчанию использовалось значение 1433) и нажмите кнопку **ОК**.

## <a name="test-the-connection-to-the-listener"></a>Проверка подключения к прослушивателю

Чтобы проверить подключение:

1. Подключитесь по протоколу RDP к серверу SQL Server, который находится в той же виртуальной сети, но не содержит реплику. Это может быть другой сервер SQL Server в кластере.

1. Для проверки подключения используйте служебную программу **sqlcmd** . Например, в следующем сценарии **sqlcmd** подключается к основной реплике через прослушиватель с использованием аутентификации Windows.
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Если прослушиватель использует порт, отличный от порта по умолчанию (1433), укажите порт в строке подключения. Например, следующая команда sqlcmd подключается к прослушивателю через порт 1435. 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

sqlcmd автоматически подключается к любому экземпляру SQL Server, на котором размещена основная реплика. 

> [!NOTE]
> Указанный порт должен быть открыт в брандмауэре обоих серверов SQL Server. Для обоих серверов требуется правило для входящего трафика используемого TCP-порта. Дополнительные сведения см. в статье [Добавление и изменение правила брандмауэра](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

## <a name="guidelines-and-limitations"></a>Рекомендации и ограничения
Обратите внимание на следующие рекомендации относительно прослушивателя группы доступности в Azure, использующего внутренний балансировщик нагрузки Azure.

* Если вы используете внутреннюю подсистему балансировки нагрузки, доступ к прослушивателю можно получить только из этой же виртуальной сети.

* Ограничивая доступ с помощью группы безопасности сети Azure, убедитесь, что правило разрешения включает IP-адреса виртуальных машин SQL Server и динамический IP-адрес балансировщика нагрузки для прослушивателя AG и IP-адрес основного кластера, если они применяются.

## <a name="for-more-information"></a>Дополнительные сведения
Дополнительные сведения см. в статье [Введение в группы доступности AlwaysOn SQL Server на виртуальных машинах Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Командлеты PowerShell
Для создания внутреннего балансировщика нагрузки на виртуальных машинах Azure можно использовать следующие командлеты PowerShell.

* Командлет [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) создает подсистему балансировки нагрузки. 
* Командлет [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) создает конфигурацию внешних IP-адресов для подсистемы балансировки нагрузки. 
* Командлет [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) создает конфигурацию правила для подсистемы балансировки нагрузки. 
* Командлет [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) создает конфигурацию внутреннего пула IP-адресов для подсистемы балансировки нагрузки. 
* Командлет [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) создает конфигурацию пробы для подсистемы балансировки нагрузки.
* Командлет [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) удаляет подсистему балансировки нагрузки из группы ресурсов Azure.
