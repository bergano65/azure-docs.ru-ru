---
title: При запуске виртуальная машина зависла на сообщении "Подготовка Windows. Не выключайте компьютер". в Azure | Документация Майкрософт
description: Здесь описаны шаги по решению проблемы, при которой во время запуска виртуальная машина зависает на сообщении "Подготовка Windows. Не выключайте компьютер".
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: e0ad7a8b083a72a9d3c630fa53601aa8fb6ad601
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48862752"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>При запуске виртуальная машина зависла на сообщении "Подготовка Windows. Не выключайте компьютер". в Azure

Эта статья помогает решить проблему, когда виртуальная машина зависает на сообщении "Подготовка Windows. Не выключайте компьютер". на этапе запуска.

## <a name="symptoms"></a>Проблемы

При использовании **диагностики загрузки** для получения снимка экрана виртуальной машины вы обнаружили, что операционная система не полностью запускается. Кроме того, на виртуальной машине отображается сообщение **Подготовка Windows. Не выключайте компьютер.** .

![Пример сообщения](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Пример сообщения](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Причина:

Обычно эта проблема возникает, когда сервер выполняет окончательную перезагрузку после изменения конфигурации. Изменение конфигурации может быть инициализировано обновлениями Windows либо изменением ролей или функций сервера. Если размер обновления, устанавливаемого в Центре обновления Windows, большой, операционной системе потребуется больше времени, чтобы перенастроить изменения.

## <a name="back-up-the-os-disk"></a>Создание резервной копии диска ОС

Прежде чем попытаться устранить проблему, создайте резервную копию диска ОС.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>На виртуальных машинах с зашифрованными дисками сначала необходимо разблокировать диски

Проверьте, является ли виртуальная машина зашифрованной. Для этого выполните следующие действия.

1. Откройте свою виртуальную машину на портале и перейдите к дискам.

2. Вы увидите столбец "Шифрование", где показано, включено ли шифрование.

Если диск ОС зашифрован, разблокируйте зашифрованный диск. Для этого выполните следующие действия.

1. Создайте виртуальную машину восстановления, которая размещена в той же группе ресурсов, учетной записи хранения и расположении, что и затронутая виртуальная машина.

2. На портале Azure удалите затронутую виртуальную машину и сохраните диск.

3. Откройте сеанс PowerShell от имени администратора.

4. Выполните следующий командлет, чтобы получить имя секрета.

    ```Powershell
    Login-AzureRmAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Получив имя секрета, выполните следующие команды в PowerShell:

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Преобразуйте значение в кодировке Base64 в байты и запишите выходные данные в файл. 

    > [!Note]
    > Имя файла BEK должно соответствовать исходному глобальному уникальному идентификатору BEK, если вы используете вариант разблокирования по USB. Кроме того, необходимо создать папку BEK на диске с буквой C, прежде чем выполнять следующие действия.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. После создания файла BEK на компьютере скопируйте этот файл на виртуальную машину восстановления, к которой подключен заблокированный диск ОС. Выполните следующую команду, используя расположение файла BEK:

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Необязательно**. В некоторых сценариях может потребоваться расшифровать диск с помощью следующей команды.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > В этой команде предполагается, что диск для шифрования расположен на диске F:

8. Если требуется собрать журналы, перейдите по пути **буква диска:\Windows\System32\winevt\Logs**.

9. Отсоедините диск от компьютера восстановления.

### <a name="create-a-snapshot"></a>Создание моментального снимка

Чтобы создать моментальный снимок, выполните действия, описанные в статье [Создание моментального снимка](..\windows\snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Сбор файла дампа памяти операционной системы

Следуйте инструкциям в разделе [Сбор файла дампа памяти](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), чтобы собрать файл дампа операционной системы, когда виртуальная машина зависает на этапе настройки.

## <a name="contact-microsoft-support"></a>Обратиться в службу поддержки Майкрософт

После сбора файла дампа обратитесь в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы проанализировать первопричину.


## <a name="rebuild-the-vm-using-powershell"></a>Повторное создание виртуальной машины с помощью PowerShell

После сбора файла дампа памяти выполните следующие шаги для повторного создания виртуальной машины.

**Для неуправляемых дисков**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID “SubscriptionID” | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Для управляемых дисков**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
$osDiskResouceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availabilty Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResouceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResouceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additnal Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
