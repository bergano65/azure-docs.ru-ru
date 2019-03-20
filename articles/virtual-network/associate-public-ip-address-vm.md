---
title: Связать общедоступный IP-адрес, к виртуальной машине
titlesuffix: Azure Virtual Network
description: Узнайте, как связать общедоступный IP-адрес, к виртуальной машине.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: jdial
ms.openlocfilehash: ce573ff8fe61f2e1d4c88963e0f21fc9402776e9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083221"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Связать общедоступный IP-адрес, к виртуальной машине

В этой статье вы узнаете, как связать общедоступный IP-адрес, к существующей виртуальной машины (VM). Если вы хотите подключиться к виртуальной Машине из Интернета, виртуальная машина должна иметь общедоступный IP-адрес, связанного с ним. Если вы хотите создать новую виртуальную Машину с помощью общедоступного IP-адреса, можно сделать с помощью [портала Azure](virtual-network-deploy-static-pip-arm-portal.md), [интерфейса командной строки Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md), или [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). За общедоступные IP-адреса взимается номинальная плата. Подробные сведения см. в разделе [Цены](https://azure.microsoft.com/pricing/details/ip-addresses/). Ограничено число общедоступных IP-адресов, которые можно использовать в рамках одной подписки. Дополнительные сведения см. в разделе [ограничения](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits-1).

Можно использовать [портала Azure](#azure-portal), Azure [интерфейса командной строки](#azure-cli) (CLI), или [PowerShell](#powershell) связан общедоступный IP-адрес, к виртуальной Машине.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портале Azure](https://portal.azure.com).
2. Перейдите к или поиск виртуальной машины, который требуется добавить общедоступный IP-адрес, а затем выберите его.
3. В разделе **параметры**выберите **сети**, а затем выберите сетевой интерфейс, вы хотите добавить общедоступный IP-адрес, как показано на следующем рисунке:

   ![Выбрать сетевой интерфейс](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Общедоступные IP-адреса связаны с сетевыми интерфейсами, подключенного к виртуальной Машине. На предыдущем рисунке виртуальная машина имеет только один сетевой интерфейс. Если виртуальная машина имеет несколько сетевых интерфейсов, все они будут отображаться, и выберите сетевой интерфейс, необходимо связать общедоступный IP-адрес.

4. Выберите **IP-конфигурации** и выберите IP-конфигурации, как показано на следующем рисунке:

   ![Выберите IP-конфигурацию](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Общедоступные IP-адреса связаны с IP-конфигурации сетевого интерфейса. На предыдущем рисунке сетевой интерфейс имеет один IP-конфигурации. Если сетевой интерфейс имеет несколько IP-конфигураций, все они будут отображаться в списке и следует выбрать IP-конфигурации, необходимо связать общедоступный IP-адрес.

5. Выберите **включено**, а затем выберите **IP-адрес (*настроить обязательные параметры*)**. Выберите существующий общедоступный IP-адрес, который автоматически закрывает **выберите общедоступный IP-адрес** поле. Если у вас все доступные общедоступный IP-адресов, необходимо создать его. Чтобы узнать как это сделать, см. в разделе [создать общедоступный IP-адрес](virtual-network-public-ip-address.md#create-a-public-ip-address). Выберите **Сохранить**, как показано на рисунке ниже, а затем закройте поле для IP-конфигурации.

   ![Включите общедоступный IP-адрес](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Общедоступные IP-адреса, которые отображаются, которые существуют в том же регионе, что и виртуальная машина. Если у вас есть несколько общих IP-адресов в регионе, все будут показаны здесь. Если любой выделены серым, это потому, что адрес уже связан другой ресурс.

6. Просмотрите общедоступный IP-адрес, назначенный IP-конфигурации, как показано на рисунке ниже. Может занять несколько секунд на IP-адрес для отображения.

   ![Просмотр назначенных общедоступный IP-адрес](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Адрес назначается из пула адресов, используемых в каждом регионе Azure. Чтобы просмотреть список пулов адресов, используемых в каждом регионе, см. в разделе [Microsoft Azure Datacenter IP-диапазонов](https://www.microsoft.com/download/details.aspx?id=41653). Адрес, назначенный может быть любой адрес в пулов, используемых для региона. Если вам требуется адрес, назначенный из определенного пула в регионе, используйте [префикс общедоступный IP-адреса](public-ip-address-prefix.md).

7. [Разрешить сетевой трафик к виртуальной Машине](#allow-network-traffic-to-the-vm) с правилами группы безопасности в группу безопасности сети.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Установка [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), или использовать Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Выберите **попробовать** ниже команды элементов управления button в интерфейсе командной строки. Выбрав **попробовать** вызывает Cloud Shell, который вы можете войти в учетную запись Azure с помощью.

1. При использовании интерфейса командной строки локально, в Bash, войдите Azure с помощью `az login`.
2. Общедоступный IP-адрес, связанный с IP-конфигурацией сетевого интерфейса, подключенного к виртуальной Машине. Используйте [az сети nic-ip-config обновления](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) команду, чтобы связать общедоступный IP-адрес, IP-конфигурации. Следующий пример связывает существующий общедоступный IP-адрес с именем *myVMPublicIP* IP-конфигурации с именем *ipconfigmyVM* из имеющийся сетевой интерфейс с именем *myVMVMNic* , хранящиеся в группу ресурсов с именем *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Если у вас нет существующего общедоступного IP-адреса, используйте [создать az сети public-ip](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) команду, чтобы создать его. Например, следующая команда создает общедоступный IP-адрес с именем *myVMPublicIP* в группе ресурсов с именем *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Предыдущая команда создает общедоступный IP-адрес со значениями по умолчанию для несколько параметров, которые требуется настроить. Дополнительные сведения обо всех параметрах общедоступный IP-адрес, см. в разделе [создать общедоступный IP-адрес](virtual-network-public-ip-address.md#create-a-public-ip-address). Адрес назначается из пула общедоступных IP-адреса, используемые для каждого региона Azure. Чтобы просмотреть список пулов адресов, используемых в каждом регионе, см. в разделе [Microsoft Azure Datacenter IP-диапазонов](https://www.microsoft.com/download/details.aspx?id=41653).

   - Если вы не знаете имя сетевого интерфейса, подключенного к виртуальной Машине, используйте [списке сетевой адаптер виртуальной машины az](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) команду, чтобы просмотреть их. Например, следующая команда перечисляет имена сетевых интерфейсов, подключенных к виртуальной Машине с именем *myVM* в группе ресурсов с именем *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Выходные данные содержат одну или несколько строк, которые похожи на следующем примере:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     В предыдущем примере *myVMVMNic* имя сетевого интерфейса.

   - Если вы не знаете имя IP-конфигурацию сетевого интерфейса, используйте [список ip конфигурация сетевого адаптера сети az](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) команду, чтобы получить их. Например, следующая команда перечисляет имена конфигураций IP-адрес для сетевого интерфейса с именем *myVMVMNic* в группе ресурсов с именем *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Просмотреть общедоступный IP-адрес, назначенный IP-конфигурации с [az vm list-ip-addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) команды. В следующем примере показано, IP-адресов, назначенных существующей виртуальной Машины с именем *myVM* в группе ресурсов с именем *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Адрес назначается из пула адресов, используемых в каждом регионе Azure. Чтобы просмотреть список пулов адресов, используемых в каждом регионе, см. в разделе [Microsoft Azure Datacenter IP-диапазонов](https://www.microsoft.com/download/details.aspx?id=41653). Адрес, назначенный может быть любой адрес в пулов, используемых для региона. Если вам требуется адрес, назначенный из определенного пула в регионе, используйте [префикс общедоступный IP-адреса](public-ip-address-prefix.md).

4. [Разрешить сетевой трафик к виртуальной Машине](#allow-network-traffic-to-the-vm) с правилами группы безопасности в группу безопасности сети.

## <a name="powershell"></a>PowerShell

Установка [PowerShell](/powershell/azure/install-az-ps), или использовать Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка, которую можно запускать непосредственно на портале Azure. Он включает предварительно установленный PowerShell и настроен для использования с вашей учетной записью. Выберите **попробовать** ниже команды элементов управления button в PowerShell. Выбрав **попробовать** вызывает Cloud Shell, который вы можете войти в учетную запись Azure с помощью.

1. Если локально с помощью PowerShell, войдите Azure с помощью `Connect-AzAccount`.
2. Общедоступный IP-адрес, связанный с IP-конфигурацией сетевого интерфейса, подключенного к виртуальной Машине. Используйте [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) и [Get AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) команды, чтобы получить виртуальную сеть и подсеть, сетевой интерфейс. Затем используйте [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) команду, чтобы получить сетевой интерфейс и [Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) команду, чтобы получить существующий общедоступный IP-адрес. Затем с помощью [набора AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) команду, чтобы связать общедоступный IP-адрес, IP-конфигурации и [AzNetworkInterface набора](/powershell/module/Az.Network/Set-AzNetworkInterface) команду, чтобы новая конфигурация IP-адрес для записи сетевой интерфейс.

   Следующий пример связывает существующий общедоступный IP-адрес с именем *myVMPublicIP* IP-конфигурации с именем *ipconfigmyVM* из имеющийся сетевой интерфейс с именем *myVMVMNic* , хранящиеся в подсеть с именем *myVMSubnet* в виртуальной сети с именем *myVMVNet*. Все ресурсы, в группу ресурсов с именем *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Если у вас нет существующего общедоступного IP-адреса, используйте [New AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) команду, чтобы создать его. Например, следующая команда создает *динамическое* общедоступный IP-адрес с именем *myVMPublicIP* в группе ресурсов с именем *myResourceGroup* в  *eastus* регион.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Предыдущая команда создает общедоступный IP-адрес со значениями по умолчанию для несколько параметров, которые требуется настроить. Дополнительные сведения обо всех параметрах общедоступный IP-адрес, см. в разделе [создать общедоступный IP-адрес](virtual-network-public-ip-address.md#create-a-public-ip-address). Адрес назначается из пула общедоступных IP-адреса, используемые для каждого региона Azure. Чтобы просмотреть список пулов адресов, используемых в каждом регионе, см. в разделе [Microsoft Azure Datacenter IP-диапазонов](https://www.microsoft.com/download/details.aspx?id=41653).

   - Если вы не знаете имя сетевого интерфейса, подключенного к виртуальной Машине, используйте [командлета Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) команду, чтобы просмотреть их. Например, следующая команда перечисляет имена сетевых интерфейсов, подключенных к виртуальной Машине с именем *myVM* в группе ресурсов с именем *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Выходные данные содержат одну или несколько строк, которые похожи на приведенный ниже пример. В приведенном примере *myVMVMNic* имя сетевого интерфейса.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Если вы не знаете имя виртуальной сети или подсети, в которой находится сетевой интерфейс в, используйте `Get-AzNetworkInterface` команду, чтобы просмотреть сведения. Например, следующая команда возвращает сведения виртуального сеть и подсеть для сетевого интерфейса с именем *myVMVMNic* в группе ресурсов с именем *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Выходные данные содержат одну или несколько строк, которые похожи на приведенный ниже пример. В приведенном примере *myVMVNET* — имя виртуальной сети и *myVMSubnet* — имя подсети.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Если вы не знаете имя IP-конфигурацию сетевого интерфейса, используйте [Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) команду, чтобы получить их. Например, следующая команда перечисляет имена конфигураций IP-адрес для сетевого интерфейса с именем *myVMVMNic* в группе ресурсов с именем *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Выходные данные содержат одну или несколько строк, которые похожи на приведенный ниже пример. В приведенном примере *ipconfigmyVM* имя IP-конфигурации.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Просмотреть общедоступный IP-адрес, назначенный IP-конфигурации с [Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) команды. В следующем примере показано адрес, назначенный общедоступный IP-адрес с именем *myVMPublicIP* в группе ресурсов с именем *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Если вы не знаете имя общедоступного IP-адреса, назначенные IP-конфигурацию, выполните следующие команды, чтобы получить его:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Выходные данные содержат одну или несколько строк, которые похожи на приведенный ниже пример. В приведенном примере *myVMPublicIP* имя общедоступного IP-адреса, назначенного IP-конфигурации.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Адрес назначается из пула адресов, используемых в каждом регионе Azure. Чтобы просмотреть список пулов адресов, используемых в каждом регионе, см. в разделе [Microsoft Azure Datacenter IP-диапазонов](https://www.microsoft.com/download/details.aspx?id=41653). Адрес, назначенный может быть любой адрес в пулов, используемых для региона. Если вам требуется адрес, назначенный из определенного пула в регионе, используйте [префикс общедоступный IP-адреса](public-ip-address-prefix.md).

4. [Разрешить сетевой трафик к виртуальной Машине](#allow-network-traffic-to-the-vm) с правилами группы безопасности в группу безопасности сети.

## <a name="allow-network-traffic-to-the-vm"></a>Разрешить сетевой трафик к виртуальной Машине

Перед подключением общедоступный IP-адрес из Интернета, убедитесь, что у вас есть необходимые порты в любой группе безопасности сети, возможно связанным сетевому интерфейсу или подсети, в которой находится сетевой интерфейс. Хотя группы безопасности: фильтрации трафика частный IP-адрес сетевого интерфейса, когда входящий Интернет-трафик поступает на общедоступный IP-адрес, Azure преобразует общедоступный адрес на частный IP-адрес адрес, поэтому, если группа безопасности сети предотвращает поток трафика, взаимодействие с общедоступный IP-адрес завершается ошибкой. Можно просмотреть действующие правила безопасности для сетевого интерфейса и ее подсети с помощью [портала](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), или [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Дальнейшие действия

Разрешить входящий Интернет-трафик к виртуальной Машине с помощью группы безопасности сети. Чтобы узнать, как создать группу безопасности сети, см. в разделе [работа с группами безопасности сети](manage-network-security-group.md#work-with-network-security-groups). Дополнительные сведения о группах безопасности сети, см. в разделе [группы безопасности](security-overview.md).