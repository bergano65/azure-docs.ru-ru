---
title: Создание управляемого образа в Azure | Документация Майкрософт
description: Создание управляемого образа универсальной виртуальной машины или виртуального жесткого диска в Azure. Образы можно использовать для создания нескольких виртуальных машин, использующих управляемые диски.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 54743b191b0334c92c63f374d38870a7dbfd9a38
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749619"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Создание управляемого образа универсальной виртуальной машины в Azure

Ресурс управляемого образа можно создать из универсальной виртуальной машины, которая хранится в виде управляемого диска или неуправляемых дисков в учетной записи хранения. Затем образ можно использовать для создания нескольких виртуальных машин. Сведения о выставлении счетов за управляемые образы см. в статье [Цены на управляемые диски](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Подготовка виртуальной машины Windows к использованию с помощью Sysprep

Sysprep удаляет все сведения о вашей учетной записи и безопасности, а затем подготавливает машину к использованию в качестве образа. Дополнительные сведения о Sysprep приведены в [обзоре Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в разделе [Поддержка Sysprep для ролей сервера](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) и [неподдерживаемые сценарии](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> После применения Sysprep на виртуальной машине она считается *универсальной*, что препятствует ее перезапуску. Процесс обобщения виртуальной машины необратим. Если необходимо сохранить исходную виртуальную машину в рабочем состоянии, нужно создать [копию виртуальной машины](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) и обобщить эту копию. 
>
> Если вы хотите запустить Sysprep перед отправкой виртуального жесткого диска в Azure в первый раз, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Чтобы обобщить виртуальную машину Windows, выполните следующие действия:

1. Войдите на виртуальную машину Windows.
   
2. Откройте окно командной строки с правами администратора. Измените каталог на %windir%\system32\sysprep и запустите файл `sysprep.exe`.
   
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и установите флажок **Подготовка к использованию**.
   
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
   
5. Нажмите кнопку **ОК**.
   
    ![Запустите Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. Не перезапускайте виртуальную машину.

> [!TIP]
> **Необязательно** Используйте [DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) для оптимизации образа и сокращения времени первой загрузки виртуальной машины.
>
> Чтобы оптимизировать образ, подключите виртуальный жесткий диск, дважды щелкнув его в проводнике Windows, а затем запустите DISM с параметром `/optimize-image`.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Где D — путь к подключенному виртуальному жесткому диску.
>
> Запуск `DISM /optimize-image` должен быть последним изменением, внесенным в виртуальный жесткий диск. Если перед развертыванием внести какие-либо изменения в виртуальный жесткий диск, необходимо будет снова запустить `DISM /optimize-image`.

## <a name="create-a-managed-image-in-the-portal"></a>Создание управляемого образа на портале 

1. Откройте [портал Azure](https://portal.azure.com).

2. В меню слева выберите **Виртуальные машины**, а затем выберите виртуальную машину из списка.

3. На странице своей **виртуальной машины** в верхнем меню выберите **Запись**.

   Появится страница **Создание образа**.

4. В поле **имя** оставьте предварительно заполненное имя или введите то, которое вы хотите использовать для образа.

5. В поле **Группа ресурсов** выберите **Создать** и введите имя или выберите **Использовать существующую** и выберите из раскрывающегося списка используемую группу ресурсов.

6. Если вы хотите удалить исходную виртуальную машину после создания образа, выберите **Automatically delete this virtual machine after creating the image** (Автоматически удалить эту виртуальную машину после создания образа).

7. Если требуется возможность использовать образ в любой [зоне доступности](../../availability-zones/az-overview.md), установите значение **Вкл.** для параметра **устойчивости зоны**.

8. Нажмите кнопку **Создать** для создания образа.

9. После создания образа вы можете найти его в виде ресурса **Образ** в списке ресурсов группы ресурсов.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Создание образа виртуальной машины с помощью PowerShell

 

Создание образа непосредственно из виртуальной машины гарантирует, что он будет содержать все ее диски, включая диск ОС и диски данных. В этом примере показано, как создать управляемый образ из виртуальной машины,которая использует управляемые диски.

Прежде чем начать, убедитесь, что у вас установлена последняя версия модуля Azure PowerShell. Выполните `Get-Module -ListAvailable Az` в PowerShell, чтобы узнать версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей [Установка Azure PowerShell в ОС Windows с помощью PowerShellGet](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, выполните командлет `Connect-AzAccount`, чтобы создать подключение к Azure.


> [!NOTE]
> Перед сохранением образа в хранилище, избыточном между зонами, его необходимо создать в регионе, который поддерживает [зоны доступности](../../availability-zones/az-overview.md) и в котором конфигурация образа содержит параметр `-ZoneResilient` (команды `New-AzImageConfig`).

Чтобы создать образ виртуальной машины, выполните следующие действия.

1. Создайте несколько переменных.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Убедитесь, что виртуальная машина была освобождена.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Теперь задайте для виртуальной машины состояние **Generalized**(Универсальная). 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Получите виртуальную машину. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Создайте конфигурацию образа.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Создайте образ.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Создание образа из управляемого диска с помощью PowerShell

Если вы хотите создать образ только диска операционной системы, укажите идентификатор управляемого диска в качестве диска ОС.

    
1. Создайте несколько переменных. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Получите виртуальную машину.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Получите идентификатор управляемого диска.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Создайте конфигурацию образа.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Создайте образ.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Создание образа из моментального снимка с помощью PowerShell

Вы можете создать управляемый образ из моментального снимка универсальной виртуальной машины, выполнив следующие действия.

    
1. Создайте несколько переменных. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Получите моментальный снимок.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Создайте конфигурацию образа.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Создайте образ.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Создание образа из виртуальной машины, использующей учетную запись хранения

Чтобы создать управляемый образ из виртуальной машины, которая не использует управляемые диски, вам потребуется универсальный код ресурса (URI) VHD операционной системы в учетной записи хранения в следующем формате: https://*mystorageaccount*. blob.core.windows.net/*вхдконтаинер*/*вхдфиленаме. VHD.* . В этом примере VHD находится в *mystorageaccount* в контейнере с именем *vhdcontainer*, а имя файла VHD — *vhdfilename.vhd*.


1.  Создайте несколько переменных.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Остановите виртуальную машину и отмените ее выделение.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Пометьте виртуальную машину как универсальную.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Создайте образ с помощью универсального виртуального жесткого диска ОС.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Дальнейшие действия
- [Создание виртуальной машины из управляемого образа](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)    

