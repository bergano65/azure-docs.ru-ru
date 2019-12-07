---
title: Отмена связи общедоступного IP-адреса с виртуальной машиной Azure
titlesuffix: Azure Virtual Network
description: Узнайте, как отменить связь между общедоступным IP-адресом и виртуальной машиной.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900747"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Отмена связи общедоступного IP-адреса с виртуальной машиной Azure 

Из этой статьи вы узнаете, как отменить связь с общедоступным IP-адресом из виртуальной машины Azure.

Чтобы отменить связь между общедоступным IP-адресом и виртуальной машиной, можно использовать [портал Azure](#azure-portal), [интерфейс командной строки](#azure-cli) Azure (CLI) или [PowerShell](#powershell) .

## <a name="azure-portal"></a>портала Azure

1. Войдите на [портале Azure](https://portal.azure.com).
2. Найдите виртуальную машину, для которой необходимо отменить связь с общедоступным IP-адресом, или найдите ее.
3. На странице Виртуальная машина выберите **Обзор**, выберите общедоступный IP-адрес, как показано на следующем рисунке.

   ![Выберите общедоступный IP-адрес](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. На странице общедоступный IP-адрес выберите **Обзор**, а затем **выберите отменить связь**, как показано на следующем рисунке.

    ![Отменить связь с общедоступным IP-адресом](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. В окне Отмена связи с **общедоступным IP-адресом**выберите **Да**.

## <a name="azure-cli"></a>Azure CLI

Установите [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)или используйте Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Нажмите кнопку **попробовать** в следующих командах интерфейса командной строки. При нажатии кнопки " **попробовать** " вызывается Cloud Shell, который можно использовать для входа в учетную запись Azure с помощью.

1. При локальном использовании интерфейса командной строки в Bash Войдите в Azure с помощью `az login`.
2. Общедоступный IP-адрес связан с IP-конфигурацией сетевого интерфейса, подключенного к виртуальной машине. Используйте команду [AZ Network NIC-IP-config Update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) , чтобы отменить связь между общедоступным IP-адресом из IP-конфигурации. В следующем примере не выполняется связь с общедоступным IP-адресом с именем *myVMPublicIP* из IP-конфигурации с именем *ипконфигмивм* существующего сетевого интерфейса с именем *myVMVMNic* , подключенного к виртуальной машине с именем *myVM* в группе ресурсов с именем *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Если вы не знакомы с именем сетевого интерфейса, подключенного к виртуальной машине, используйте команду [AZ VM NIC List](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) , чтобы просмотреть их. Например, следующая команда выводит список имен сетевых интерфейсов, подключенных к виртуальной машине с именем *myVM* в группе ресурсов с именем *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Выходные данные включают одну или несколько строк, которые похожи на приведенный ниже пример.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     В предыдущем примере *myVMVMNic* — это имя сетевого интерфейса.

   - Если имя IP-конфигурации для сетевого интерфейса неизвестно, воспользуйтесь командой [AZ Network NIC IP-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) , чтобы получить их. Например, следующая команда перечисляет имена общедоступных IP-конфигураций для сетевого интерфейса с именем *myVMVMNic* в группе ресурсов с именем *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Если вы не знакомы с именем общедоступной IP-конфигурации для сетевого интерфейса, воспользуйтесь командой [AZ Network NIC IP-config показывать](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) , чтобы получить их. Например, следующая команда перечисляет имена общедоступных IP-конфигураций для сетевого интерфейса с именем *myVMVMNic* в группе ресурсов с именем *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Установите [PowerShell](/powershell/azure/install-az-ps)или используйте Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка, которую можно запускать непосредственно на портале Azure. Он включает предварительно установленный PowerShell и настроен для использования с вашей учетной записью. Нажмите кнопку **попробовать** в следующих командах PowerShell. При нажатии кнопки " **попробовать** " вызывается Cloud Shell, который можно использовать для входа в учетную запись Azure с помощью.

1. Если вы используете PowerShell локально, войдите в Azure с помощью `Connect-AzAccount`.
2. Общедоступный IP-адрес связан с IP-конфигурацией сетевого интерфейса, подключенного к виртуальной машине. Используйте команду [Get-азнетворкинтерфаце](/powershell/module/Az.Network/Get-AzNetworkInterface) , чтобы получить сетевой интерфейс. Задайте для параметра общедоступный IP-адрес значение null, а затем используйте команду [Set-азнетворкинтерфаце](/powershell/module/Az.Network/Set-AzNetworkInterface) , чтобы записать новую IP-конфигурацию в сетевой интерфейс.

   В следующем примере общедоступный IP-адрес с именем *myVMPublicIP* удаляется из сетевого интерфейса с именем *myVMVMNic* , подключенного к виртуальной машине с именем *myVM*. Все ресурсы находятся в группе ресурсов с именем *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Если вы не знакомы с именем сетевого интерфейса, подключенного к виртуальной машине, используйте команду [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) , чтобы просмотреть их. Например, следующая команда выводит список имен сетевых интерфейсов, подключенных к виртуальной машине с именем *myVM* в группе ресурсов с именем *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Выходные данные включают одну или несколько строк, которые похожи на приведенный ниже пример. В примере выходных данных *myVMVMNic* — это имя сетевого интерфейса.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Если имя IP-конфигурации для сетевого интерфейса неизвестно, используйте команду [Get-азнетворкинтерфаце](/powershell/module/Az.Network/Get-AzNetworkInterface) , чтобы получить их. Например, следующая команда выводит список имен IP-конфигураций для сетевого интерфейса с именем *myVMVMNic* в группе ресурсов с именем *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Выходные данные включают одну или несколько строк, которые похожи на приведенный ниже пример. В примере выходных данных *ипконфигмивм* — это имя IP-конфигурации.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [связать общедоступный IP-адрес с виртуальной машиной](associate-public-ip-address-vm.md).
