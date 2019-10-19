---
title: Создание виртуальной машины Windows с помощью специализированного VHD в Azure | Документация Майкрософт
description: Создание новой виртуальной машины Windows на основе подключенного специализированного управляемого диска операционной системы с использованием модели развертывания с помощью Resource Manager.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: be773779b25a32a5904012ae31950b18c33341dc
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553430"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Создание виртуальной машины Windows из специализированного диска с помощью PowerShell

Создайте виртуальную машину, подключив специализированный управляемый диск в качестве диска ОС. Специализированный диск — это копия VHD виртуальной машины, в которой содержатся учетные записи пользователей, приложения и другие данные о состоянии исходной виртуальной машины. 

При использовании специализированного VHD для создания новой виртуальной машины новая виртуальная машина сохраняет имя исходной виртуальной машины. Другие сведения о компьютере также сохраняются, и в некоторых случаях эти повторяющиеся данные могут вызвать проблемы. При копировании виртуальной машины следует учитывать, какие типы сведений о компьютере используют приложения.

Доступно несколько параметров.
* [Использование существующего управляемого диска.](#option-1-use-an-existing-disk) Этот параметр может помочь в случае, если виртуальная машина работает неправильно. Вы можете удалить виртуальную машину, а затем создать новую, которая будет использовать этот же управляемый диск. 
* [Передача VHD](#option-2-upload-a-specialized-vhd) 
* [Копирование существующей виртуальной машины Azure с использованием моментальных снимков](#option-3-copy-an-existing-azure-vm)

Кроме того, вы можете использовать портал Azure, чтобы [создать виртуальную машину из специализированного VHD](create-vm-specialized-portal.md).

В этой статье показано, как использовать управляемые диски. При наличии развертывания устаревших версий, для которой требуется использовать учетную запись хранения, см. статью [Создание виртуальной машины на основе специализированного VHD в учетной записи хранения](sa-create-vm-specialized.md).

Рекомендуется ограничить число параллельных развертываний на 20 виртуальных машин с одного виртуального жесткого диска или моментального снимка. 

## <a name="option-1-use-an-existing-disk"></a>Вариант 1. Использование существующего диска

Если вы удалили виртуальную машину и вам требуется повторно использовать диск операционной системы, чтобы создать новую, примените [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Теперь можно присоединить этот диск как диск ОС для [новой виртуальной Машины](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Вариант 2. Передача специализированного VHD

Вы можете передать VHD из специализированной виртуальной машины, созданной с помощью локального средства виртуализации, например, Hyper-V или виртуальная машина, экспортированная из другого облака.

### <a name="prepare-the-vm"></a>Подготовка виртуальной машины
Используйте VHD "как есть" для создания новой виртуальной машины. 
  
  * [Подготовьте виртуальный жесткий диск Windows к передаче в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Не выполняйте** подготовку виртуальной машины к использованию с помощью Sysprep.
  * Удалите все гостевые инструменты и агенты виртуализации, которые установлены на виртуальной машине (такие как инструменты VMware).
  * Убедитесь, что виртуальная машина настроена на получение IP-адреса и параметров DNS от DHCP-сервера. Таким образом, сервер будет получать IP-адрес в виртуальной сети при запуске. 


### <a name="upload-the-vhd"></a>Отправка виртуального жесткого диска

Теперь виртуальный жесткий диск можно передать прямо в управляемый диск. Инструкции см. в статье [Отправка виртуального жесткого диска в Azure с помощью Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Вариант 3. Копирование существующей виртуальной машины Azure

Вы можете создать копию виртуальной машины, использующую управляемые диски. Для этого создайте моментальный снимок виртуальной машины, а затем с его помощью создайте новый управляемый диск и новую виртуальную машину.

Если вы хотите скопировать существующую виртуальную машину в другой регион, вы можете использовать azcopy, чтобы [создать копию диска в другом регионе](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Создание моментального снимка диска операционной системы

Вы можете сделать моментальный снимок всей виртуальной машины (включая все диски) или одного диска. Ниже показано, как создать моментальный снимок только диска операционной системы виртуальной машины с помощью командлета [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot). 

Сначала задайте некоторые параметры. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Получите объект виртуальной машины.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Получите имя диска ОС.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Создайте конфигурацию моментального снимка. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Создайте моментальный снимок.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Чтобы использовать этот моментальный снимок для создания виртуальной машины, требующей высокой производительности, добавьте параметр `-AccountType Premium_LRS` в команду New-Азснапшотконфиг. Этот параметр создает моментальный снимок таким образом, чтобы он хранился в качестве управляемого диска уровня "Премиум". Такие диски дороже, чем стандартные, поэтому перед использованием этого параметра убедитесь, что вам необходимы именно управляемые диски уровня "Премиум".

### <a name="create-a-new-disk-from-the-snapshot"></a>Создайте новый диск из моментального снимка.

Создайте управляемый диск из моментального снимка, используя [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). В этом примере используется *myOSDisk* для имени диска.

Создайте группу ресурсов для новой виртуальной машины.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Задайте имя диска ОС. 

```powershell
$osDiskName = 'myOsDisk'
```

Создайте управляемый диск.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Создание виртуальной машины 

Создайте сетевые ресурсы и другие ресурсы виртуальной машины, которые будет использовать новая виртуальная машина.

### <a name="create-the-subnet-and-virtual-network"></a>Создание подсети и виртуальной сети

Создайте [виртуальную сеть](../../virtual-network/virtual-networks-overview.md) и подсеть виртуальной машины.

1. Создание подсети. В этом примере создается подсеть с именем *mySubnet* в группе ресурсов *myDestinationResourceGroup* и задается префикс адреса подсети *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Создание виртуальной сети. В этом примере задается имя виртуальной сети *myVnetName*, расположение *Западная часть США* и префикс адреса виртуальной сети *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Создание группы безопасности сети и правила RDP
Чтобы войти на виртуальную машину с помощью протокола удаленного рабочего стола (RDP), необходимо настроить правило безопасности, которое разрешает доступ RDP через порт 3389. В нашем примере VHD для новой виртуальной машины создан на основе имеющейся специализированной виртуальной машины, поэтому можно использовать учетную запись исходной виртуальной машины для входа по протоколу RDP.

В этом примере для группы безопасности сети (NSG) задается имя *myNsg*, а для правила протокола удаленного рабочего стола — имя *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Дополнительные сведения о конечных точках и правилах NSG см. в статье [Открытие портов для виртуальной машины в Azure с помощью PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Создание общедоступного IP-адреса и сетевой карты
Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Создайте общедоступный IP-адрес. В этом примере открытому IP-адресу присвоено имя *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Создание сетевой карты. В этом примере сетевой карте присвоено имя *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Задание имени и размера виртуальной машины

В этом примере для имени виртуальной машины задается значение *myVM*, а для ее размера — *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Добавление сетевой карты
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Добавление диска операционной системы 

Добавьте диск операционной системы в конфигурацию с помощью [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). В этом примере для диска устанавливается размер *128 ГБ*, а управляемый диск подключается в качестве диска ОС *Windows*.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Завершение процесса подготовки виртуальной машины 

Создайте виртуальную машину, используя созданную конфигурацию [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Если команда выполнена успешно, вы увидите примерно такой результат.

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Проверка создания виртуальной машины
Созданная виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите **Обзор** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Дальнейшие действия
Войдите на свою новую виртуальную машину. Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](connect-logon.md).

