---
title: Краткое руководство. Создание виртуальной сети с помощью Azure PowerShell
titlesuffix: Azure Virtual Network
description: Из этого краткого руководства вы узнаете, как создать виртуальную сеть с помощью портала Azure. Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными в частном порядке и взаимодействовать через Интернет.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: jdial
ms.openlocfilehash: ade8329e6e42fae9f3232617488a6d4a69f8ef1f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437391"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Краткое руководство. Создание виртуальной сети с помощью PowerShell

Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными в частном порядке и взаимодействовать через Интернет. Из этого краткого руководства вы узнаете, как создать виртуальную сеть. Создав виртуальную сеть, разверните в ней две виртуальные машины. Затем вы подключитесь к виртуальным машинам из Интернета и установите частную связь через виртуальную сеть.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится модуль AzureRM PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Сведения об установке или обновлении см. в [этой статье](/powershell/azure/azurerm/install-azurerm-ps).

Наконец, если модуль PowerShell запущен локально, необходимо также выполнить `Connect-AzureRmAccount`. Эта команда создает подключение к Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Создание группы ресурсов и виртуальной сети

Для настройки группы ресурсов и виртуальной сети необходимо выполнить несколько шагов.

### <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием виртуальной сети необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). В этом примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть по умолчанию *myVirtualNetwork* в расположении *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Добавление подсети

Azure развертывает ресурсы в подсети виртуальной сети, поэтому необходимо создать подсеть. С помощью командлета [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) создайте конфигурацию подсети с именем *default*:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Связка подсети с виртуальной сетью

Вы можете записать конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork). Эта команда создает подсеть:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте две виртуальные машины в виртуальной сети.

### <a name="create-the-first-vm"></a>Создание первой виртуальной машины

Создайте первую виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). При запуске приведенной ниже команды запрашиваются учетные данные. Введите имя пользователя и пароль виртуальной машины:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Параметр `-AsJob` создает виртуальную машину в фоновом режиме. Перейдите к следующему шагу.

Когда Azure начнет создание виртуальной машины в фоновом режиме, вы получите результат, аналогичный следующему:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM
```

### <a name="create-the-second-vm"></a>Создание второй виртуальной машины

Создайте вторую виртуальную машину с помощью следующей команды:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Вам нужно будет создать другого пользователя и пароль. Создание виртуальной машины в Azure занимает несколько минут.

> [!IMPORTANT]
> Не переходите к следующему шагу, пока Azure не закончит создание виртуальной машины.  Процесс создания закончится, когда выходные данные будут возвращены в PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Приведенный ниже пример возвращает общедоступный IP-адрес виртуальной машины *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Откройте командную строку на локальном компьютере. Выполните команду `mstsc`. Замените `<publicIpAddress>` общедоступным IP-адресом, полученным на последнем шаге:

> [!NOTE]
> Если вы выполняли эти команды в командной строке PowerShell на локальном компьютере и используете модуль AzureRM PowerShell 5.4.1 или более поздней версии, можете продолжить работу в этом интерфейсе.

```cmd
mstsc /v:<publicIpAddress>
```

На ваш компьютер будет скачан файл протокола удаленного рабочего стола (*RDP*), после чего откроется удаленный рабочий стол.

1. При появлении запроса выберите **Подключиться**.

1. Введите имя пользователя и пароль, указанные при создании виртуальной машины.

    > [!NOTE]
    > Возможно, потребуется выбрать **More choices** > **Use a different account** (Дополнительные варианты > Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины.

1. Нажмите кнопку **ОК**.

1. Вы можете получить предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

## <a name="communicate-between-vms"></a>Взаимодействие между виртуальными машинами

1. На удаленном рабочем столе *myVm1* откройте PowerShell.

1. Укажите `ping myVm2`.

    Вы получите сообщение, аналогичное следующему:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudap
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Проверка связи завершилась ошибкой, так как в ней используется протокол ICMP. По умолчанию ICMP запрещен брандмауэром Windows.

1. Чтобы разрешить виртуальной машине *myVm2* проверять связь с *myVm1* на дальнейшем этапе, введите следующую команду:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Эта команда разрешает входящий трафик ICMP через брандмауэр Windows.

1. Закройте подключение к удаленному рабочему столу *myVm1*.

1. Повторите шаги, приведенные в разделе [Подключение к виртуальной машине из Интернета](#connect-to-a-vm-from-the-internet). На этот раз подключитесь к *myVm2*.

1. В командной строке на виртуальной машине *myVm2* введите `ping myvm1`.

    Вы получите сообщение, аналогичное следующему:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Вы получите ответы от *myVm1*, так как на предыдущем шаге разрешили использовать ICMP через брандмауэр Windows на виртуальной машине *myVm1*.

1. Закройте подключение к удаленному рабочему столу *myVm2*.

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы с виртуальной сетью и виртуальными машинами удалите группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

Следуя инструкциям в этом кратком руководстве, вы создали виртуальную сеть по умолчанию и две виртуальные машины. Затем вы подключились к одной виртуальной машине из Интернета и установили частную связь между двумя виртуальными машинами. Дополнительные сведения о параметрах виртуальной сети см. в статье об [управлении виртуальной сетью](manage-virtual-network.md).

Служба Azure не накладывает ограничения на частную связь между виртуальными машинами. По умолчанию она разрешает только входящие подключения к удаленному рабочему столу виртуальных машин Windows из Интернета. Дополнительные сведения о разрешении или ограничении различных типов сетевого взаимодействия с виртуальными машинами см. в статье [Руководство. Фильтрация сетевого трафика с помощью групп безопасности сети, используя портал Azure](tutorial-filter-network-traffic.md).
