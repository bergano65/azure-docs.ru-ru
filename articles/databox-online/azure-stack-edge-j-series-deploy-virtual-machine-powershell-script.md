---
title: Развертывание виртуальных машин на Azure Stack пограничном устройстве GPU с помощью Azure PowerShell
description: Описание создания виртуальных машин и управления ими на Azure Stack пограничном устройстве с помощью Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 6e776b6dfc233ffb12d3597a0e6bc203f1674abd
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147071"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell-script"></a>Развертывание виртуальных машин на пограничном устройстве Azure Stack с помощью скрипта Azure PowerShell

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

В этом руководстве описывается, как создать виртуальную машину на Azure Stack пограничном устройстве и управлять ею с помощью скрипта Azure PowerShell.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к созданию виртуальной машины на Azure Stack пограничном устройстве и управлении ею с помощью этого скрипта, необходимо убедиться, что выполнены предварительные требования, перечисленные в следующих шагах.

### <a name="for-azure-stack-edge-device-via-the-local-web-ui"></a>Для Azure Stack пограничных устройств через локальный веб-интерфейс

1. Вы завершили настройку сети на Azure Stack пограничном устройстве, как описано в разделе [Шаг 1. настройка Azure Stack пограничных устройств](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-device).

2. Включен сетевой интерфейс для вычислений. Этот IP-адрес сетевого интерфейса используется для создания виртуального коммутатора для развертывания виртуальной машины. Следующие шаги описывают процесс:

    1. Перейдите к **параметрам вычислений**. Выберите сетевой интерфейс, который будет использоваться для создания виртуального коммутатора.

        > [!IMPORTANT] 
        > Для вычислений можно настроить только один порт.

    2. Включите вычисление на сетевом интерфейсе. Azure Stack ребро создает виртуальный коммутатор, соответствующий этому сетевому интерфейсу, и управляет им.

3. Вы создали и установили все сертификаты на Azure Stack пограничном устройстве и в доверенном корневом хранилище клиента. Выполните процедуру, описанную в разделе [Шаг 2. Создание и установка сертификатов](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

### <a name="for-your-windows-client"></a>Для клиента Windows

1. Вы определили виртуальный IP-адрес (VIP) служб Azure на странице **сети** в локальном интерфейсе пользователя на устройстве. Этот виртуальный IP-адрес необходимо добавить в:

    - Файл узла на клиенте или,
    - Конфигурация DNS-сервера
    
    > [!IMPORTANT]
    > Рекомендуется изменить конфигурацию DNS-сервера для разрешения имен конечных точек.

    1. Запустите **Блокнот** от имени администратора (для сохранения файла требуются права администратора), а затем откройте файл **hosts** , расположенный по адресу `C:\Windows\System32\Drivers\etc` .
    
        ![Файл hosts проводника Windows](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Добавьте следующие записи в файл **hosts** , заменив соответствующими значениями для вашего устройства:
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        Для учетной записи хранения можно указать имя, которое сценарий будет использовать позже для создания новой учетной записи хранения. Сценарий не проверяет, существует ли эта учетная запись хранения.

    3. Используйте следующий образ для справки. Сохраните файл **hosts**.

        ![файл hosts в блокноте](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Скачайте скрипт PowerShell,](https://aka.ms/ase-vm-powershell) используемый в этой процедуре.

3. Убедитесь, что клиент Windows работает под управлением PowerShell 5,0 или более поздней версии.

4. Убедитесь, что `Azure.Storage Module version 4.5.0` установлен в системе. Этот модуль можно получить из [коллекция PowerShell](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0). Чтобы установить этот модуль, введите:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Чтобы проверить версию установленного модуля, введите:

    `Get-InstalledModule -name Azure.Storage`

    Чтобы удалить другие модули версии, введите:

    `Uninstall-Module -Name Azure.Storage`

5. [Скачайте AzCopy 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#download-azcopy) в клиент Windows. Запишите это расположение, так как оно будет передано в качестве параметра при выполнении скрипта.

6. Убедитесь, что клиент Windows работает под управлением TLS 1,2 или более поздней версии.


## <a name="create-a-vm"></a>Создание виртуальной машины

1. Откройте сеанс PowerShell от имени администратора.
2. Перейдите в папку, в которую был скачан скрипт на клиенте.  
3. Выполните следующую команду, чтобы запустить скрипт.
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    Ниже приведены примеры выполнения сценария для создания ВИРТУАЛЬНОЙ машины Windows и виртуальной машины Linux.

    **Для виртуальной машины Windows:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **Для виртуальной машины Linux:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. Чтобы очистить ресурсы, созданные сценарием, используйте следующие команды:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>Дальнейшие действия

[Развертывание виртуальных машин с помощью командлетов Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)
