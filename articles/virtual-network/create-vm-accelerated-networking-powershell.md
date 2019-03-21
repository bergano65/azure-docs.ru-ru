---
title: Создание виртуальной машины Azure с ускоренной сетью | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Linux с ускоренной сетью.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: c4567919490c8bc9094dea3dddbe22550d9eebb2
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192911"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Создание виртуальной машины Windows с ускоренной сетью

В этом руководстве вы узнаете, как создать виртуальную машину Windows с ускоренной сетью. Сведения о создании виртуальной машины Linux с ускоренной сетью см. в статье [Создание виртуальной машины Linux с ускоренной сетью](create-vm-accelerated-networking-cli.md). Функция ускорения сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот высокопроизводительный метод обеспечивает обход узла на пути к данным, что уменьшает задержку, дрожание и нагрузку ЦП. Он предназначен для ресурсоемких рабочих нагрузок сети на виртуальных машинах поддерживаемых типов. На следующем рисунке приведена схема обмена данными между двумя виртуальными машинами с функцией ускорения сети и без нее:

![Сравнение](./media/create-vm-accelerated-networking/accelerated-networking.png)

Без функции ускорения сети весь входящий и исходящий сетевой трафик виртуальной машины должен проходить через узел и виртуальный коммутатор. Виртуальный коммутатор обеспечивает принудительное применение всех политик, таких как группы безопасности сети, списки управления доступом, изоляция, и использование других служб виртуализации сети для сетевого трафика. Дополнительные сведения о виртуальных коммутаторов, см. в разделе [виртуализации сети Hyper-V и виртуальном коммутаторе](https://technet.microsoft.com/library/jj945275.aspx).

При использовании функции ускорения сети трафик поступает в сетевой интерфейс (NIC), а затем перенаправляется на виртуальную машину. Все сетевые политики, которые применяет виртуальный коммутатор, выгружены и применяются на аппаратном уровне. Благодаря применению политик на аппаратном уровне сетевая карта может перенаправлять сетевой трафик непосредственно на виртуальную машину в обход узла и виртуального коммутатора. При этом все политики, примененные на узле, сохраняются.

Возможности функции ускорения сети применяются только к той виртуальной машине, где она включена. Для получения наилучших результатов необходимо включить эту функцию по крайней мере на двух виртуальных машинах, подключенных к одной виртуальной сети Azure. При обмене данными между виртуальными или локальными сетями эта функция практически не влияет на общую задержку.

## <a name="benefits"></a>Преимущества
* **Уменьшение задержки (больше пакетов в секунду).** Благодаря обходу виртуального коммутатора на пути к данным на узле не выполняется обработка политики пакетов. Таким образом увеличивается число пакетов, которые могут быть обработаны на виртуальной машине.
* **Уменьшение дрожания.** Обработка с помощью виртуального коммутатора зависит от числа политик, которые необходимо применить, и рабочей нагрузки ЦП, выполняющего обработку. Так как принудительное применение политик осуществляется на аппаратном уровне, эта зависимость устраняется за счет доставки пакетов непосредственно на виртуальную машину. Это позволяет избежать обмена данными между узлом и виртуальной машиной, прерываний работы программного обеспечения и переключений контекста.
* **Уменьшение нагрузки ЦП.** Обход виртуального коммутатора на узле приводит к меньшему использованию ЦП для обработки сетевого трафика.

## <a name="limitations-and-constraints"></a>Ограничения

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
Без дополнительной настройки из коллекции Azure поддерживаются следующие дистрибутивы:
* **Windows Server 2016 Datacenter** 
* **Центр обработки данных Windows Server 2012 R2**

### <a name="supported-vm-instances"></a>Поддерживаемые экземпляры виртуальных машин
Функция ускорения сети поддерживается для большинства размеров экземпляров, оптимизированных для вычислений, и экземпляров общего назначения с количеством виртуальных ЦП от 2.  Поддерживаются серии D/DSv2 и F/Fs

На экземплярах, поддерживающих технологию Hyper-Threading, функция ускорения сети поддерживается в экземплярах виртуальных машин с количеством виртуальных ЦП от 4. Поддерживаются серии D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms-Mms и Ms/Mmsv2.

Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>регионы
Доступна во всех общедоступных регионах Azure и облаке Azure для государственных организаций.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Включение ускоренной сети для работающей виртуальной машины
Чтобы включить ускоренную сеть для виртуальной машины поддерживаемого размера, для которой эта функция отключена, виртуальную машину необходимо остановить и отменить ее распределение.

### <a name="deployment-through-azure-resource-manager"></a>Развертывание с помощью Azure Resource Manager
Классические виртуальные машины нельзя развернуть с включенной функцией ускорения сети.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Создание виртуальной машины Windows с ускорением сети Azure
## <a name="portal-creation"></a>Создание на портале
В этой статье описано, как создать виртуальную машину с ускоренной сетью, с помощью Azure Powershell, вы также можете [Создание виртуальной машины с ускоренной сетью, с помощью портала Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). При создании виртуальной машины на портале в **Создание виртуальной машины** колонке выберите **сети** вкладки.  На этой вкладке есть параметр для **ускоренная сеть**.  Если вы выбрали [поддерживаемая операционная система](#supported-operating-systems) и [размер виртуальной Машины](#supported-vm-instances), этот параметр будет автоматически заполнять «Включен».  В противном случае он заполнит параметр «Off» для повышения производительности сети и дать пользователю причины, почему он не будет включен.   
* *Примечание.* На портале можно включить только для поддерживаемых операционных систем.  Если вы используете пользовательский образ и образ поддерживающим ускоренную сеть, создайте виртуальную Машину с помощью интерфейса командной строки или Powershell. 

После создания виртуальной машины можно было подтвердить повышение производительности сети включена, следуя инструкциям в Подтвердите, что ускоренная сеть включена.

## <a name="powershell-creation"></a>Создание PowerShell
## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Установка [Azure PowerShell](/powershell/azure/install-az-ps) версии 1.0.0 или более поздней версии. Чтобы определить текущую версию, выполните командлет `Get-Module -ListAvailable Az`. Если необходимо выполнить установку или обновление, установите последнюю версию модуля Az [коллекции PowerShell](https://www.powershellgallery.com/packages/Az). В сеансе PowerShell войдите в учетную запись Azure с помощью [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount).

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *myNic* и *myVM*.

Создайте группу ресурсов с помощью командлета [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *centralus*.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Во-первых, создайте конфигурацию подсети с [New AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). В следующем примере создается подсеть с именем *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Создание виртуальной сети с помощью [New AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), с помощью *mySubnet* подсети.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Во-первых, создайте правило группы безопасности сети с [New AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Создайте группу безопасности сети с помощью [New AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) и назначить *Allow-RDP-All* ей правило безопасности. Кроме правила *Allow-RDP-All* группа безопасности сети содержит несколько стандартных правил. С помощью одного правила по умолчанию запрещается входящий трафик из Интернета, из-за чего правило *Allow-RDP-All* и назначается группе безопасности сети. Таким образом вы можете удаленно подключиться к виртуальной машине после ее создания.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Свяжите группу безопасности сети с *mySubnet* подсети с [AzVirtualNetworkSubnetConfig набора](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Правило в группе безопасности сети распространяется на все ресурсы, развернутые в подсети.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Создание сетевого интерфейса с функцией ускорения сети
Создайте общедоступный IP-адрес с помощью командлета [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Если вы не планируете входить на виртуальную машину из Интернета, общедоступный IP-адрес не требуется, однако он нужен, чтобы выполнить шаги, описанные в этой статье.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Создайте сетевой интерфейс с [New AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) с включенной функцией ускорения сети и назначить общедоступный IP-адрес сетевому интерфейсу. В следующем примере создается сетевой интерфейс с именем *myNic* в подсети *mySubnet* виртуальной сети *myVnet* и ему назначается общедоступный IP-адрес *myPublicIp*.

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Создание виртуальной машины

Задайте переменной `$cred` учетные данные виртуальной машины, используя командлет [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Во-первых, определите виртуальную Машину с [New AzVMConfig](/powershell/module/az.compute/new-azvmconfig). В следующем примере определяется виртуальная машина с именем *myVM* и используется размер виртуальной машины с поддержкой функции ускорения сети (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Чтобы просмотреть список всех размеров виртуальных машин и их характеристики, ознакомьтесь со статьей [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Создайте оставшуюся часть конфигурации виртуальной машины с помощью [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) и [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). В следующем примере создается виртуальная машина Windows Server 2016:

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Подключить этот сетевой интерфейс, созданный ранее с помощью [AzVMNetworkInterface добавить](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Наконец, создайте виртуальную Машину с [командлета New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Проверка установки драйвера в операционной системе

После создания виртуальной машины в Azure подключитесь к ней и проверьте, установлен ли драйвер в Windows.

1. Откройте [портал](https://portal.azure.com) Azure в браузере и войдите, используя учетную запись Azure.
2. В верхней области портала Azure в поле с текстом *Поиск ресурсов* введите *MyVm*. Когда виртуальная машина **MyVm** появится в результатах поиска, щелкните ее. Если под кнопкой **Подключиться** отображается элемент **Создание**, это означает, что виртуальная машина по-прежнему создается в Azure. Когда элемент **Создание** больше не будет отображаться под кнопкой **Подключиться**, вы можете нажать кнопку **подключения** в левом верхнем углу страницы обзора.
3. Введите имя пользователя и пароль, указанный при [создании виртуальной машины](#create-the-virtual-machine). Ознакомьтесь с разделом [Подключение к виртуальной машине](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine), если вы никогда не подключались к виртуальной машине Windows в Azure.
4. В главном меню Windows щелкните правой кнопкой мыши кнопку "Пуск" и выберите **Диспетчер устройств**. Разверните узел **Сетевые адаптеры**. Проверьте, есть ли в списке виртуальный адаптер **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**, как показано на следующем рисунке:

    ![Диспетчер устройств](./media/create-vm-accelerated-networking/device-manager.png)

Ускорение работы в сети теперь включено на вашей виртуальной машине.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Включение ускоренной сети для имеющихся виртуальных машин
Если вы создали виртуальную машину без ускоренной сети, для нее можно включить эту функцию позднее.  Виртуальная машина должна поддерживать ускоренную сеть, то есть соответствовать указанным ниже требованиям, которые также описаны выше.

* Виртуальная машина должна иметь размер, поддерживаемый функцией ускоренной сети.
* Виртуальная машина должна быть развернута из поддерживаемого образа коллекции Azure (и иметь поддерживаемую версию ядра для Linux).
* Все виртуальные машины в группе доступности или масштабируемом наборе виртуальных машин должны быть остановлены и освобождены перед включением ускоренной сети в любом из сетевых адаптеров.

### <a name="individual-vms--vms-in-an-availability-set"></a>Отдельные виртуальные машины и виртуальные машины в группе доступности
Сначала остановите виртуальную машину и отмените ее выделение. В случае с группой доступности сделайте это со всеми виртуальными машинами в наборе:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Обратите внимание на то, что если виртуальная машина была создана отдельно, то есть без группы доступности, для включения ускоренной сети необходимо остановить только эту виртуальную машину и отменить ее выделение.  Если виртуальная машина была создана с группой доступности, перед включением ускоренной сети в любом сетевом адаптере необходимо остановить все виртуальные машины в группе и отменить их выделение. 

После остановки виртуальной машины включите ускоренную сеть в ее сетевом адаптере:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Перезапуск виртуальной Машины или, если в группе доступности все виртуальные машины в наборе и убедитесь, что включено ускорение работы в сети:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>Масштабируемый набор виртуальных машин
Процедура для масштабируемого набора виртуальных машин немного отличается, но в целом похожа.  Сначала остановите виртуальные машины:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

После остановки виртуальных машин измените значение свойства ускоренной сети в сетевом интерфейсе:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Обратите внимание на то, что обновления к виртуальным машинам в масштабируемом наборе применяются тремя разными способами: автоматически, последовательно и вручную.  В рассматриваемом случае настроено автоматическое обновление, поэтому изменения применяются к масштабируемому набору виртуальных машин сразу после перезапуска.  Чтобы настроить автоматическое обновление, выполните следующую команду:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Наконец, перезапустите масштабируемый набор виртуальных машин:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

После перезапуска дождитесь завершения обновления. Как только оно закончится, в виртуальной машине появится виртуальная функция.  (Убедитесь в том, что вы используете поддерживаемую ОС и размер виртуальной машины.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Изменение размеров имеющихся виртуальных машин с ускоренной сетью

Размер виртуальных машин с ускоренной сетью можно изменить только на такой, который также поддерживает ускоренную сеть.  

Виртуальную машину с включенной виртуальной сетью нельзя изменить на экземпляр такого размера, который не поддерживает ускоренную сеть, с помощью операции изменения размера.  Вместо этого нужно выполнить указанные ниже действия.

* Остановите виртуальную машину и отмените ее выделение. В случае с группой доступности или масштабируемым набором виртуальных машин сделайте это со всеми виртуальными машинами в группе или наборе.
* Отключите ускоренную сеть в сетевом адаптере виртуальной машины.В случае с группой доступности или масштабируемым набором виртуальных машин сделайте это со всеми виртуальными машинами в группе или наборе.
* После отключения ускоренной сети можно изменить размер виртуальной машины, группы доступности или масштабируемого набора виртуальных машин на новый, который не поддерживает ускоренную сеть, а затем перезапустить виртуальные машины.
