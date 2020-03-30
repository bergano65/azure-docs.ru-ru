---
title: Отмежевать общедоступный IP-адрес от Azure VM
titlesuffix: Azure Virtual Network
description: Узнайте, как отделить общедоступный IP-адрес от VM
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900747"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Отмежевать общедоступный IP-адрес от Azure VM 

В этой статье вы узнаете, как отделить общедоступный IP-адрес от виртуальной машины Azure (VM).

Вы можете использовать [портал Azure,](#azure-portal) [интерфейс командной строки](#azure-cli) Azure (CLI) или [PowerShell,](#powershell) чтобы отмежевать общедоступный IP-адрес от VM.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).
2. Просматривайте или ищите виртуальную машину, с которую вы хотите отмежеваться от общедоступного IP-адреса, а затем выберите его.
3. На странице VM выберите **Обзор**, выберите общедоступный IP-адрес, показанный на следующем изображении:

   ![Выберите публичный IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. На общедоступной странице IP-адреса выберите **Обзор,** а затем выберите **Dissociate**, как показано на следующем рисунке:

    ![Разъединить общественную ИС](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. В **Dissociate публичный IP-адрес,** выберите **Да**.

## <a name="azure-cli"></a>Azure CLI

Установите [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)или воспользуйтесь оболочкой Azure Cloud Shell. Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Выберите кнопку **«Попробуй те»** в последующих командах CLI. Выбрав **пробующий сярют** в облачную оболочку, с помощью которого вы можете войти в свою учетную запись Azure.

1. При использовании CLI локально в Bash, восвай в Azure с `az login`.
2. Публичный IP-адрес связан с конфигурацией IP сетевого интерфейса, прикрепленного к VM. Используйте команду [обновления обновления nic-ip-conf-config сети,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) чтобы отделить общедоступный IP-адрес от конфигурации IP. Следующий пример разъединяет публичный IP-адрес под названием *myVMPublicIP* от конфигурации IP под названием *ipconfigmyVM* существующего сетевого интерфейса под названием *myVMVMNic,* который присоединен к VM под названием *myVM* в группе ресурсов под названием *myResourceGroup.*
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Если вы не знаете названия сетевого интерфейса, прикрепленного к вашему VM, используйте команду [списка az vm nic](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) для их просмотра. Например, в следующей команде перечислены имена сетевых интерфейсов, прикрепленных к VM под названием *myVM,* в группе ресурсов под названием *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Выход включает одну или несколько строк, похожих на следующий пример:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     В предыдущем примере *myVMVMNic* — это название сетевого интерфейса.

   - Если вы не знаете название конфигурации IP для сетевого интерфейса, используйте команду [списка ip-конфигурации сети,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) чтобы получить их. Например, в следующей команде перечислены имена общедоступных конфигураций IP для сетевого интерфейса под названием *myVMVMNic* в группе ресурсов под названием *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Если вы не знаете названия общедоступной конфигурации IP для сетевого интерфейса, используйте команду [шоу-шоу az network nic ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) для их получения. Например, в следующей команде перечислены имена общедоступных конфигураций IP для сетевого интерфейса под названием *myVMVMNic* в группе ресурсов под названием *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Установите [PowerShell](/powershell/azure/install-az-ps)или используйте облачную оболочку Azure. Azure Cloud Shell — это бесплатная оболочка, которую можно запускать непосредственно на портале Azure. Он включает предварительно установленный PowerShell и настроен для использования с вашей учетной записью. Выберите кнопку **«Попробуй те»** в последующих командах PowerShell. Выбрав **пробующий сярют** в облачную оболочку, с помощью которого вы можете войти в свою учетную запись Azure.

1. При использовании PowerShell локально, вопийво в Azure с `Connect-AzAccount`помощью .
2. Публичный IP-адрес связан с конфигурацией IP сетевого интерфейса, прикрепленного к VM. Используйте команду [Get-AzNetworkInterface,](/powershell/module/Az.Network/Get-AzNetworkInterface) чтобы получить сетевой интерфейс. Установите значение общедоступного IP-адреса на нулевых, а затем используйте команду [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) для записи новой конфигурации IP в сетевой интерфейс.

   Следующий пример отделяет общественный IP-адрес под названием *myVMPublicIP* от сетевого интерфейса под названием *myVMVMNic,* который присоединен к VM под названием *myVM.* Все ресурсы находятся в ресурсной группе под названием *myResourceGroup.*
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Если вы не знаете имя сетевого интерфейса, прикрепленного к вашему VM, используйте команду [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) для их просмотра. Например, в следующей команде перечислены имена сетевых интерфейсов, прикрепленных к VM под названием *myVM,* в группе ресурсов под названием *myResourceGroup:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Выход включает одну или несколько строк, похожих на приведенный ниже пример. В примере *выход, myVMVMNic* это имя сетевого интерфейса.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Если вы не знаете название конфигурации IP для сетевого интерфейса, используйте команду [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) для их получения. Например, в следующей команде перечислены имена конфигураций IP для сетевого интерфейса под названием *myVMVMNic* в группе ресурсов под названием *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Выход включает одну или несколько строк, похожих на приведенный ниже пример. В примере *выход ipconfigmyVM* — это название конфигурации IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [связать публичный IP-адрес с VM.](associate-public-ip-address-vm.md)
