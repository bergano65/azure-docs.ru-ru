---
title: Связать публичный IP-адрес с виртуальной машиной
titlesuffix: Azure Virtual Network
description: Узнайте, как связать общедоступный IP-адрес с виртуальной машиной.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647464"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Связать публичный IP-адрес с виртуальной машиной

В этой статье вы узнаете, как связать публичный IP-адрес с существующей виртуальной машиной (VM). Если вы хотите подключиться к VM из Интернета, VM должен иметь общественный IP-адрес, связанный с ним. Если вы хотите создать новый VM с общедоступным IP-адресом, вы можете сделать это с помощью [портала Azure,](virtual-network-deploy-static-pip-arm-portal.md) [интерфейса командной строки Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md)или [PowerShell.](virtual-network-deploy-static-pip-arm-ps.md) За общедоступные IP-адреса взимается номинальная плата. Подробные сведения см. в разделе [Цены](https://azure.microsoft.com/pricing/details/ip-addresses/). Существует ограничение на количество общедоступных IP-адресов, которые можно использовать за подписку. Для получения подробной [информации см.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)

Вы можете использовать [портал Azure,](#azure-portal) [интерфейс командной строки](#azure-cli) Azure (CLI) или [PowerShell,](#powershell) чтобы связать общедоступный IP-адрес с VM.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).
2. Просматривайте или ищите виртуальную машину, на которую вы хотите добавить общедоступный IP-адрес, а затем выберите его.
3. В **настройках**выберите **Networking,** а затем выберите сетевой интерфейс, к который необходимо добавить общедоступный IP-адрес, как показано на следующем рисунке:

   ![Выберите сетевой интерфейс](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Публичные IP-адреса связаны с сетевыми интерфейсами, прикрепленными к VM. На предыдущем снимке VM имеет только один сетевой интерфейс. Если бы у VM было несколько сетевых интерфейсов, все они появились бы, и вы бы выбрали сетевой интерфейс, с которым вы хотите связать общедоступный IP-адрес.

4. Выберите **конфигурации IP,** а затем выберите конфигурацию IP, как показано на следующем рисунке:

   ![Выберите конфигурацию IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Публичные IP-адреса связаны с конфигурациями IP для сетевого интерфейса. На предыдущем снимке сетевой интерфейс имеет одну конфигурацию IP- Если бы в сетевом интерфейсе было несколько конфигураций IP, все они отображались бы в списке, и вы бы выбрали конфигурацию IP, с которой вы хотите связать общедоступный IP-адрес.

5. Выберите **Enabled,** затем выберите **IP-адрес *(настроите необходимые настройки).*** Выберите существующий общедоступный IP-адрес, который автоматически закрывает поле **для публичного IP-адреса Select.** Если у вас нет доступных общедоступных IP-адресов, вам необходимо создать их. Чтобы узнать, как, см [Создать общедоступный IP-адрес](virtual-network-public-ip-address.md#create-a-public-ip-address). Выберите **Сохранить**, как показано на рисунке, которое следует, а затем закрыть поле для конфигурации IP.

   ![Включить публичный IP-адрес](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Публичные IP-адреса, которые появляются являются те, которые существуют в том же регионе, как VM. Если в регионе создано несколько общедоступных IP-адресов, все они будут отображаться здесь. Если какой-либо из них серый, это потому, что адрес уже связан с другим ресурсом.

6. Просмотр общедоступного IP-адреса, присвоенного конфигурации IP, как показано на следующем рисунке. Появление IP-адреса может занять несколько секунд.

   ![Просмотр назначенного общедоступного IP-адреса](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Адрес присваивается из пула адресов, используемых в каждом регионе Azure. Чтобы просмотреть список пулов адресов, [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)используемых в каждом регионе, см. Назначенный адрес может быть любым адресом в пулах, используемых для региона. Если вам нужен адрес, который будет назначен из определенного пула в регионе, [используйте префикс публичного IP-адреса.](public-ip-address-prefix.md)

7. [Разрешить сетевой трафик на VM](#allow-network-traffic-to-the-vm) с правилами безопасности в группе сетевой безопасности.

## <a name="azure-cli"></a>Azure CLI

Установите [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)или воспользуйтесь оболочкой Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Выберите кнопку **«Попробуй те»** в последующих командах CLI. Выбрав **пробующий сярют** в облачную оболочку, с помощью которого вы можете войти в свою учетную запись Azure.

1. При использовании CLI локально в Bash, восвай в Azure с `az login`.
2. Публичный IP-адрес связан с конфигурацией IP сетевого интерфейса, прикрепленного к VM. Используйте команду [обновления обновления nic-ip-conf-config сети,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) чтобы связать общедоступный IP-адрес с конфигурацией IP. Следующий пример связывает существующий публичный IP-адрес под названием *myVMPublicIP* с конфигурацией IP под названием *ipconfigmyVM* существующего сетевого интерфейса под названием *myVMVMNic,* который существует в группе ресурсов под названием *myResourceGroup.*
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Если у вас нет существующего общедоступного IP-адреса, используйте команду [public-ip сети az](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) для создания. Например, следующая команда создает общедоступный IP-адрес под названием *myVMPublicIP* в группе ресурсов под названием *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Предыдущая команда создает общедоступный IP-адрес со значениями по умолчанию для нескольких параметров, которые можно настроить. Чтобы узнать больше обо всех общедоступных настройках IP-адреса, [см.](virtual-network-public-ip-address.md#create-a-public-ip-address) Адрес присваивается из пула общедоступных IP-адресов, используемых для каждого региона Azure. Чтобы просмотреть список пулов адресов, [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)используемых в каждом регионе, см.

   - Если вы не знаете названия сетевого интерфейса, прикрепленного к вашему VM, используйте команду [списка az vm nic](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) для их просмотра. Например, в следующей команде перечислены имена сетевых интерфейсов, прикрепленных к VM под названием *myVM,* в группе ресурсов под названием *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Выход включает одну или несколько строк, похожих на следующий пример:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     В предыдущем примере *myVMVMNic* — это название сетевого интерфейса.

   - Если вы не знаете название конфигурации IP для сетевого интерфейса, используйте команду [списка ip-конфигурации сети,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) чтобы получить их. Например, в следующей команде перечислены имена конфигураций IP для сетевого интерфейса под названием *myVMVMNic* в группе ресурсов под названием *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Просмотр общедоступного IP-адреса, присвоенного конфигурации IP, с командой [списка az vm-ip-адресов.](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) Ниже приводится IP-адреса, присвоенные существующему VM под названием *myVM* в группе ресурсов под названием *myResourceGroup.*

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Адрес присваивается из пула адресов, используемых в каждом регионе Azure. Чтобы просмотреть список пулов адресов, [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)используемых в каждом регионе, см. Назначенный адрес может быть любым адресом в пулах, используемых для региона. Если вам нужен адрес, который будет назначен из определенного пула в регионе, [используйте префикс публичного IP-адреса.](public-ip-address-prefix.md)

4. [Разрешить сетевой трафик на VM](#allow-network-traffic-to-the-vm) с правилами безопасности в группе сетевой безопасности.

## <a name="powershell"></a>PowerShell

Установите [PowerShell](/powershell/azure/install-az-ps)или используйте облачную оболочку Azure. Azure Cloud Shell — это бесплатная оболочка, которую можно запускать непосредственно на портале Azure. Он включает предварительно установленный PowerShell и настроен для использования с вашей учетной записью. Выберите кнопку **«Попробуй те»** в последующих командах PowerShell. Выбрав **пробующий сярют** в облачную оболочку, с помощью которого вы можете войти в свою учетную запись Azure.

1. При использовании PowerShell локально, вопийво в Azure с `Connect-AzAccount`помощью .
2. Публичный IP-адрес связан с конфигурацией IP сетевого интерфейса, прикрепленного к VM. Используйте команды [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) и [Get-AzVirtualNetworkSubnetConfig,](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) чтобы получить виртуальную сеть и подсеть, в которую находится сетевой интерфейс. Затем используйте команду [Get-AzNetworkInterface,](/powershell/module/Az.Network/Get-AzNetworkInterface) чтобы получить сетевой интерфейс и команду [Get-AzPublicIpAddress,](/powershell/module/az.network/get-azpublicipaddress) чтобы получить существующий общедоступный IP-адрес. Затем используйте команду [Set-AzNetworkInterfaceIpConfig,](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) чтобы связать общедоступный IP-адрес с конфигурацией IP и команду [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) для записи новой конфигурации IP с сетевым интерфейсом.

   Следующий пример связывает существующий публичный IP-адрес под названием *myVMPublicIP* с конфигурацией IP под названием *ipconfigmyVM* существующего сетевого интерфейса под названием *myVMVMNic,* который существует в подсети под названием *myVMSubnet* в виртуальной сети под названием *myVMVNet.* Все ресурсы находятся в ресурсной группе под названием *myResourceGroup.*
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Если у вас нет существующего общедоступного IP-адреса, используйте команду [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) для его создания. Например, следующая команда создает *динамический* общедоступный IP-адрес под названием *myVMPublicIP* в группе ресурсов под названием *myResourceGroup* в регионе *Восток.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Предыдущая команда создает общедоступный IP-адрес со значениями по умолчанию для нескольких параметров, которые можно настроить. Чтобы узнать больше обо всех общедоступных настройках IP-адреса, [см.](virtual-network-public-ip-address.md#create-a-public-ip-address) Адрес присваивается из пула общедоступных IP-адресов, используемых для каждого региона Azure. Чтобы просмотреть список пулов адресов, [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)используемых в каждом регионе, см.

   - Если вы не знаете имя сетевого интерфейса, прикрепленного к вашему VM, используйте команду [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) для их просмотра. Например, в следующей команде перечислены имена сетевых интерфейсов, прикрепленных к VM под названием *myVM,* в группе ресурсов под названием *myResourceGroup:*

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Выход включает одну или несколько строк, похожих на приведенный ниже пример. В примере *выход, myVMVMNic* это имя сетевого интерфейса.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Если вы не знаете название виртуальной сети или подсети, в которую находится сетевой интерфейс, используйте `Get-AzNetworkInterface` команду для просмотра информации. Например, следующая команда получает информацию о виртуальной сети и подсети для сетевого интерфейса под названием *myVMVMNic* в группе ресурсов под названием *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Выход включает одну или несколько строк, похожих на приведенный ниже пример. В примере *выход, myVMVNET* это имя виртуальной сети и *myVMSubnet* это имя подсети.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Если вы не знаете название конфигурации IP для сетевого интерфейса, используйте команду [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) для их получения. Например, в следующей команде перечислены имена конфигураций IP для сетевого интерфейса под названием *myVMVMNic* в группе ресурсов под названием *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Выход включает одну или несколько строк, похожих на приведенный ниже пример. В примере *выход ipconfigmyVM* — это название конфигурации IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Просмотр общедоступного IP-адреса, присвоенного конфигурации IP, с помощью команды [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) В следующем примере показан адрес, присвоенный общедоступному IP-адресу под названием *myVMPublicIP,* в группе ресурсов под названием *myResourceGroup.*

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Если вы не знаете имя общедоступного IP-адреса, назначенного конфигурации IP, запустите следующие команды, чтобы получить его:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Выход включает одну или несколько строк, похожих на приведенный ниже пример. В примере *myVMPublicIP* — это имя общедоступного IP-адреса, присвоенного конфигурации IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Адрес присваивается из пула адресов, используемых в каждом регионе Azure. Чтобы просмотреть список пулов адресов, [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)используемых в каждом регионе, см. Назначенный адрес может быть любым адресом в пулах, используемых для региона. Если вам нужен адрес, который будет назначен из определенного пула в регионе, [используйте префикс публичного IP-адреса.](public-ip-address-prefix.md)

4. [Разрешить сетевой трафик на VM](#allow-network-traffic-to-the-vm) с правилами безопасности в группе сетевой безопасности.

## <a name="allow-network-traffic-to-the-vm"></a>Разрешить сетевой трафик на VM

Прежде чем вы сможете подключиться к общедоступному IP-адресу из Интернета, убедитесь, что у вас есть необходимые порты, открытые в любой группе сетевой безопасности, которые вы могли бы связаны с сетевым интерфейсом, подсетью сетевого интерфейса, или и то, и другое. Хотя группы безопасности фильтруют трафик на частный IP-адрес сетевого интерфейса, как только входящий интернет-трафик поступает на общедоступный IP-адрес, Azure переводит общедоступный адрес на частный IP-адрес, так что если группа сетевой безопасности предотвращает поток трафика, связь с общедоступным IP-адресом выходит из строя. Вы можете просмотреть эффективные правила безопасности для сетевого интерфейса и его подсети с помощью [портала,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal) [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)или [PowerShell.](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)

## <a name="next-steps"></a>Дальнейшие действия

Разрешить входящий интернет-трафик на ваш VM с группой сетевой безопасности. Чтобы узнать, как создать группу сетевой безопасности, [см.](manage-network-security-group.md#work-with-network-security-groups) Чтобы узнать больше о [Security groups](security-overview.md)группах сетевой безопасности, см.
