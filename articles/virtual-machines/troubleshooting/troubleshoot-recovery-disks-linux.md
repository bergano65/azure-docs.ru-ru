---
title: Использование виртуальной машины Linux для устранения неполадок с помощью Azure CLI | Документы Майкрософт
description: Узнайте, как устранять неполадки виртуальных машин Linux, подключив диск операционной системы к виртуальной машине восстановления с помощью Azure CLI
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 49ee83e451e9d555a7fe5fca57bc58d6616334da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641057"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Устранение неполадок виртуальной машины Linux путем подключения диска ОС к виртуальной машине восстановления с помощью Azure CLI
Если возникает проблема с загрузкой или диском на виртуальной машине Linux, возможно, вам нужно устранить неполадки, связанные с самим виртуальным жестким диском. Например, такая ситуация возникает из-за неправильной записи в `/etc/fstab`, которая мешает успешно загрузить виртуальную машину. В этой статье подробно описано, как с помощью Azure CLI подключить виртуальный жесткий диск к другой виртуальной машине Linux для устранения ошибок, а затем восстановить исходную виртуальную машину. 

## <a name="recovery-process-overview"></a>Обзор процесса восстановления
Процесс устранения неполадок выглядит следующим образом.

1. Остановите затронутую виртуальную машину.
1. Создание моментального снимка с диска операционной системы виртуальной машины.
1. Создайте диск из моментального снимка диска операционной системы.
1. Подключите новый диск ОС к другой виртуальной машине Linux для устранения неполадок.
1. Подключитесь к этой виртуальной машине. Измените файлы или запустите инструменты, чтобы устранить проблемы на новом диске ОС.
1. Отключите и отсоедините новый диск ОС от виртуальной машины для устранения неполадок.
1. Измените диск операционной системы для затронутой виртуальной машины.

Чтобы выполнить эти действия по устранению неполадок, нужно установить последнюю версию [Azure CLI](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index).

> [!Important]
> Скрипты в этой статье применяются только к виртуальным машинам, которые используют [управляемый диск](../linux/managed-disks-overview.md). 

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup` и `myVM`.

## <a name="determine-boot-issues"></a>Выявление проблем при загрузке
Изучите последовательные выходные данные, чтобы определить, почему виртуальная машина не может правильно загрузиться. Например, причиной может быть некорректная запись в `/etc/fstab`, удаление или перемещение базового виртуального жесткого диска.

Получите журналы загрузки с помощью команды [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). В следующем примере возвращаются последовательные выходные данные с виртуальной машины `myVM` в группе ресурсов `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Просмотрите последовательные выходные данные, чтобы определить причину сбоя загрузки виртуальной машины. Если они не помогли определить причину сбоя, может потребоваться просмотреть файлы журналов в каталоге `/var/log` после подключения виртуального жесткого диска к виртуальной машине для устранению неполадок.

## <a name="stop-the-vm"></a>Остановка виртуальной машины

В следующем примере прекращается работа виртуальной машины `myVM` из группы ресурсов `myResourceGroup`:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Подождите завершения удаления виртуальной машины перед выполнением следующего шага.

## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Создание моментального снимка на основе диска ОС виртуальной машины

Моментальный снимок — это полная копия виртуального жесткого диска, доступная только для чтения. Его нельзя присоединить к виртуальной машине. На следующем шаге мы создадим диск из этого моментального снимка. В следующем примере создается моментальный снимок с именем `mySnapshot` из диска операционной системы виртуальной машины "myVM". 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```
## <a name="create-a-disk-from-the-snapshot"></a>Создание диска из моментального снимка

Этот скрипт создает управляемый диск с именем `newOSDisk` из моментального снимка `mysnapshot`.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Теперь у вас есть копия исходного диска ОС. Этот новый диск можно подключить к другой виртуальной машине Windows для устранения неполадок.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Подключение нового виртуального жесткого диска к другой виртуальной машине
В следующих нескольких шагах описывается использование другой виртуальной машины для устранения неполадок. Вы подключаете диск к этой виртуальной машине для устранения неполадок, чтобы просматривать и изменять содержимое диска. Этот процесс позволяет, например, исправить ошибки конфигурации или изучить дополнительные журналы протоколов или системы. Выберите или создайте другую виртуальную машину, которая будет использоваться для устранения неполадок.

Подключите существующий виртуальный жесткий диск с помощью команды [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk). При присоединении существующего виртуального жесткого диска укажите универсальный код ресурса (URI) адрес диска, полученный в предыдущей команде `az vm show`. В следующем примере существующий виртуальный жесткий диск присоединяется к виртуальной машине для устранения неполадок `myVMRecovery` в группе ресурсов `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```
## <a name="mount-the-attached-data-disk"></a>Подключение присоединенного диска данных

> [!NOTE]
> В следующих примерах описана процедура для виртуальной машины Ubuntu. Если вы используете другой дистрибутив Linux, например Red Hat Enterprise Linux или SUSE, команды `mount` и расположение файлов журнала могут немного отличаться. Используйте документацию к своему дистрибутиву, чтобы скорректировать команды соответствующим образом.

1. Подключитесь к виртуальной машине для устранения неполадок по протоколу SSH, используя соответствующие учетные данные. Если присоединенный диск является первым диском данных на этой виртуальной машине, он скорее всего подключен к `/dev/sdc`. Запустите команду `dmseg`, чтобы просмотреть присоединенные диски:

    ```bash
    dmesg | grep SCSI
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    В предыдущем примере диск операционной системы расположен в `/dev/sda`, а временный диск, предоставляемый для каждой виртуальной машины, расположен в `/dev/sdb`. Если у вас несколько дисков данных, они будут присоединены как `/dev/sdd`, `/dev/sde` и т. д.

2. Создайте каталог для подключения существующего виртуального жесткого диска. Следующая команда создает каталог с именем `troubleshootingdisk`.

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Если на существующем виртуальном жестком диске есть несколько разделов, подключите нужный. Следующая команда подключает первый основной разделу к каталогу `/dev/sdc1`.

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Мы рекомендуем подключать диски данных к виртуальным машинам Azure с использованием глобального уникального идентификатора (UUID) виртуального жесткого диска. Для нашего упрощенного примера по устранению неполадок можно и не использовать UUID для подключения диска. Но в обычной ситуации, если вы измените `/etc/fstab` так, чтобы виртуальные жесткие диски подключались по имени устройства вместо UUID, у виртуальной машины могут быть проблемы с загрузкой.


## <a name="fix-issues-on-the-new-os-disk"></a>Устранение проблем на новом диске ОС
Теперь, когда существующий виртуальный жесткий диск подключен, вы можете выполнить любые необходимые действия по обслуживанию и (или) устранению неполадок. После устранения проблем выполните следующие действия.


## <a name="unmount-and-detach-the-new-os-disk"></a>Отключение и отсоединение нового диска ОС
После устранения ошибок отключите и отсоедините существующий виртуальный жесткий диск от виртуальной машины, использованный для устранения неполадок. Виртуальный жесткий диск нельзя использовать с другой виртуальной машиной, пока вы не отмените аренду, присоединяющую виртуальный жесткий диск к виртуальной машине для устранения неполадок.

1. Используя открытый сеанс подключения по SSH к виртуальной машине для устранения неполадок, отключите существующий виртуальный жесткий диск. Прежде всего выйдите из каталога, в котором создана точка подключения.

    ```bash
    cd /
    ```

    Теперь отключите существующий виртуальный жесткий диск. В следующем примере диск отключается от каталога `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Теперь отсоедините виртуальный жесткий диск от виртуальной машины. Выйдите из сеанса SSH и вернитесь к виртуальной машине для устранения неполадок. Выведите список дисков данных, подключенные к виртуальной машине для устранения неполадок, выполнив команду [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk). В следующем примере указываются диски данных, подключенные к виртуальной машине `myVMRecovery` в группе ресурсов `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Обратите внимание на имя существующего виртуального жесткого диска. Например, имя диска с URI **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** — **myVHD**. 

    Отключите диск данных от виртуальной машины командой [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk). В следующем примере диск `myVHD` отключается от виртуальной машины `myVMRecovery` в группе ресурсов `myResourceGroup`.

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Изменение диска операционной системы для затронутой виртуальной машины

Для переключения дисков ОС можно использовать Azure PowerShell. Нет необходимости удалять и повторно создавать виртуальную машину.

Этот пример останавливает виртуальную машину `myVM`, а затем присваивает диск `newOSDisk` в качестве нового диска ОС. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Следующие шаги
При возникновении проблем с подключением к виртуальной машине см. статью [Устранение неполадок с SSH-подключением к виртуальной машине Azure Linux: сбой, ошибка или отклонение](troubleshoot-ssh-connection.md). Для решения проблем с доступом к приложениям, выполняющимся на виртуальной машине, см. статью [Устранение проблем с подключением к приложениям на виртуальных машинах Linux в Azure](troubleshoot-app-connection.md).

