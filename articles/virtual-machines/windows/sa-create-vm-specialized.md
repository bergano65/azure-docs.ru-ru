---
title: Создание виртуальной машины на основе специализированного диска в Azure
description: Создание виртуальной машины на основе подключенного специализированного неуправляемого диска в модели развертывания диспетчера ресурсов.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: d887ef2ef74bb433d6e8ae7f53cd0b77f5948303
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073349"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Создание виртуальной машины на основе специализированного VHD в учетной записи хранения

Создайте виртуальную машину, подключив специализированный неуправляемый диск в качестве диска ОС с помощью PowerShell. Специализированный диск — это копия виртуального жесткого диска виртуальной машины, в которой сохраняются учетные записи пользователей, приложения и другие данные о состоянии исходной виртуальной машины. 

Существует два варианта.
* [Передача VHD](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Копирование VHD, имеющейся виртуальной машины Azure](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm).

 


## <a name="option-1-upload-a-specialized-vhd"></a>Вариант 1. Передача специализированного VHD

Вы можете передать VHD из специализированной виртуальной машины, созданной с помощью локального средства виртуализации, например, Hyper-V или виртуальная машина, экспортированная из другого облака.

### <a name="prepare-the-vm"></a>Подготовка виртуальной машины
Вы можете передать специализированный VHD, созданный с помощью локальной виртуальной машины, или VHD, экспортированный из другого облака. На специализированном VHD сохраняются учетные записи пользователей, приложения и другие данные о состоянии исходной виртуальной машины. Если вы планируете использовать виртуальный жесткий диск "как есть" для создания виртуальной машины, то необходимо выполнить следующие действия: 
  
  * [Подготовьте виртуальный жесткий диск Windows к передаче в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Не выполняйте** подготовку виртуальной машины к использованию с помощью Sysprep.
  * Удалите все гостевые инструменты и агенты виртуализации, которые установлены на виртуальной машине (т. е. инструменты VMware).
  * Убедитесь, что виртуальная машина настроена на получение IP-адреса и параметров DNS через DHCP. Таким образом, сервер будет получать IP-адрес в виртуальной сети при запуске. 


### <a name="get-the-storage-account"></a>Получение учетной записи хранения
Вам необходима учетная запись хранения Azure для хранения переданного образа виртуальной машины. Можно использовать существующую учетную запись хранения или создать новую. 

Чтобы отобразить список доступных учетных записей хранения, введите:

```powershell
Get-AzStorageAccount
```

Если вы хотите использовать существующую учетную запись хранения, перейдите к разделу "Отправка образа виртуальной машины".

Если требуется создать учетную запись хранения, то выполните описанные ниже действия.

1. Необходимо указать имя группы ресурсов, в которой будет создана учетная запись хранения. Чтобы найти все группы ресурсов, существующие в вашей подписке, введите:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Чтобы создать группу ресурсов с именем **myResourceGroup** в регионе **Западная часть США**, введите:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. С помощью командлета **New-AzStorageAccount** создайте в этой группе ресурсов учетную запись хранения с именем [mystorageaccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount):
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Передача виртуального жесткого диска в учетную запись хранения
Используйте командлет [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), чтобы передать образ в контейнер в учетной записи хранения. В этом примере файл **myVHD.vhd** передается из расположения `"C:\Users\Public\Documents\Virtual hard disks\"` в учетную запись хранения **mystorageaccount**, входящую в группу ресурсов **myResourceGroup**. Файл будет помещен в контейнер с именем **mycontainer**; новое имя файла — **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


В случае успешного выполнения отобразится ответ, который выглядит следующим образом.

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

В зависимости от сетевого подключения и размера VHD-файла выполнение этой команды может занять некоторое время.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Вариант 2. Копирование VHD из имеющейся виртуальной машины Azure

Вы можете скопировать VHD в другую учетную запись хранения, чтобы использовать его при создании дублируемых виртуальных машин.

### <a name="before-you-begin"></a>Перед началом работы
Убедитесь, что выполнены следующие условия.

* У вас есть сведения об **исходной и целевой учетных записях хранения**. Для исходной виртуальной машины необходимы имена учетной записи хранения и контейнера. Как правило, имя контейнера — **vhds**. Необходимо также иметь целевую учетную запись хранения. Если вы ее еще не создали, то это можно сделать на портале (**Все службы** > Учетные записи хранения > Добавить) или с помощью командлета [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount). 
* Вы скачали и установили [инструмент AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Освобождение виртуальной машины
Освободите виртуальную машину, что позволит скопировать VHD. 

* **Портал**: щелкните **Виртуальные машины** > **myVM** > Остановить
* **PowerShell**. Используйте команду " [AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) " для завершения (освобождения) виртуальной машины с именем **myVM** в группе ресурсов **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

Вы увидите, что **состояние** виртуальной машины на портале Azure изменится с **Остановлено** на **Остановлено (освобождено)** .

### <a name="get-the-storage-account-urls"></a>Получение URL-адресов учетной записи хранения
Необходимо получить URL-адреса исходной и целевой учетных записей хранения. URL-адреса выглядят следующим образом: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Если вы уже знаете имена учетной записи хранения и контейнера, то можете просто подставить эти данные в скобки, чтобы создать URL-адрес. 

Для получения URL-адреса можно использовать портал Azure или Azure PowerShell:

* **Портал.** Щелкните **>** **Все службы** > **Учетные записи хранения** > *учетная запись хранения* > **Большие двоичные объекты**. Ваш исходный VHD-файл скорее всего будет находиться в контейнере **vhds**. Щелкните **Свойства** контейнера и скопируйте текст с пометкой **URL-адрес**. Вам понадобятся URL-адреса исходного и целевого контейнеров. 
* **PowerShell**. Используйте [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) , чтобы получить сведения для виртуальной машины с именем **myVM** в группе ресурсов **myResourceGroup**. В результатах просмотрите раздел **Storage profile** (Профиль хранилища) и найдите в нем **URI VHD**. Первая часть URI является URL-адресом контейнера, а последняя часть — именем VHD операционной системы для виртуальной машины.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Получение ключей доступа к хранилищу
Найдите ключи доступа для исходной и целевой учетных записей хранения. Дополнительные сведения о ключах доступа см. в статье [Об учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md).

* **Портал.** Щелкните **Все службы** > **Учетные записи хранения** > *учетная запись хранения* > **Ключи доступа**. Скопируйте ключ с пометкой **key1**.
* **PowerShell**. Используйте [Get-азсторажеаккаунткэй](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) , чтобы получить ключ хранилища для учетной записи хранения **mystorageaccount** в группе ресурсов **myResourceGroup**. Скопируйте ключ с пометкой **key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Копирование виртуального жесткого диска
С помощью AzCopy можно копировать файлы между учетными записями хранения. Если у вас нет указанного целевого контейнера, то он будет создан для вас. 

Чтобы воспользоваться AzCopy, откройте окно командной строки на локальном компьютере и перейдите к папке, в которой установлен инструмент AzCopy. Это будет папка вида *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*. 

Чтобы скопировать все файлы внутри контейнера, используйте параметр **/S**. Это действие можно использовать для копирования VHD операционной системы и всех дисков данных, если они находятся в одном контейнере. В этом примере показано, как скопировать все файлы из контейнера **mysourcecontainer** в учетной записи хранения **mysourcestorageaccount** в контейнер **mydestinationcontainer** в учетной записи хранения **mydestinationstorageaccount**. Замените имена учетных записей хранения и контейнеров своими собственными. Замените ключи `<sourceStorageAccountKey1>` и `<destinationStorageAccountKey1>` своими собственными.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Если требуется скопировать только один VHD из контейнера с несколькими файлами, то можно указать имя файла с помощью параметра /Pattern. В данном примере будет скопирован только файл с именем **myFileName.vhd**.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


По завершении отобразится сообщение следующего вида:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Устранение неполадок
* Если при использовании AZCopy отображается сообщение об ошибке "Серверу не удалось проверить подлинность этого запроса", убедитесь, что значение заголовка авторизации составлено правильно (содержит подпись). Если используется ключ key2 или вторичный ключ к хранилищу данных, попробуйте воспользоваться первичным ключом или первым ключом к хранилищу данных.

## <a name="create-the-new-vm"></a>Создание виртуальной машины 

Необходимо создать сетевые ресурсы и другие ресурсы виртуальной машины, которые будет использовать новая виртуальная машина.

### <a name="create-the-subnet-and-vnet"></a>Создание виртуальной сети и подсети

Создайте виртуальную сеть и подсеть [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

1. Создайте подсеть. В этом примере создается подсеть с именем **mySubnet** в группе ресурсов **myResourceGroup** и задается префикс адреса подсети **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Создайте виртуальную сеть. В этом примере задается имя виртуальной сети **myVnetName**, расположение **западная часть США** и префикс адреса виртуальной сети **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Создание группы безопасности сети и правила RDP
   Чтобы войти на виртуальную машину по протоколу удаленного рабочего стола, необходимо настроить правило безопасности, которое разрешает доступ по этому протоколу через порт 3389. Так как VHD для новой виртуальной машины создан на основе имеющейся специализированной виртуальной машины, то и существующую учетную запись (у которой есть разрешение на вход по протоколу удаленного рабочего стола) можно использовать для обеих этих виртуальных машин.
   Это нужно сделать до создания сетевого интерфейса, с которым он будет связан.  
   В этом примере для NSG задается имя **myNsg**, а для правила протокола удаленного рабочего стола — имя **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Дополнительные сведения о конечных точках и правилах NSG см. в статье [Открытие портов для виртуальной машины в Azure с помощью PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Создание общедоступного IP-адреса и сетевой карты
Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Создайте общедоступный IP-адрес. В этом примере открытому IP-адресу присвоено имя **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Создание сетевой карты. В этом примере сетевой карте присвоено имя **myNicName**. На этом шаге группа безопасности сети, созданная ранее, также связывается с этой сетевой картой.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Задание имени и размера виртуальной машины

В этом примере для имени виртуальной машины задается значение myVM, а для ее размера — Standard_A2.
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Добавление сетевой карты
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Настройка диска операционной системы

1. Задайте универсальный код ресурса (URI) VHD, который вы передали или скопировали. В этом примере VHD-файл **myOsDisk.vhd** хранится в учетной записи **myStorageAccount** в контейнере **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Добавьте диск операционной системы. В этом примере при создании диска ОС к имени виртуальной машины добавляется термин "osDisk" для создания имени диска операционной системы. В этом примере также указывается, что этот виртуальный жесткий диск на платформе Windows должен быть подключен к виртуальной машине в качестве диска ОС.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

(Необязательно.) При наличии дисков данных, которые необходимо подключить к виртуальной машине, добавьте их, используя URL-адреса виртуальных жестких дисков данных и соответствующий логический номер устройства (LUN).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

При использовании учетной записи хранения URL-адреса дисков данных и дисков ОС выглядят так: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Их можно узнать на портале. Для этого найдите целевой контейнер хранилища, щелкните скопированный виртуальный жесткий диск операционной системы или данных, а затем скопируйте содержимое URL-адреса.


### <a name="complete-the-vm"></a>Завершение процесса подготовки виртуальной машины 

Создайте виртуальную машину, используя созданную конфигурацию.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Если команда будет выполнена успешно, вы увидите примерно такой результат.

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Проверка создания виртуальной машины
Созданная виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите **Все службы** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Дополнительная информация
Войдите на свою новую виртуальную машину. Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](connect-logon.md).

