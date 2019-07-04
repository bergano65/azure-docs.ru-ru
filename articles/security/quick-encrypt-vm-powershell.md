---
title: Краткое руководство. Шифрование виртуальной машины IaaS под управлением Windows с помощью Azure PowerShell | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как с помощью Azure PowerShell шифровать виртуальную машину IaaS под управлением Windows.
services: security
documentationcenter: na
author: msmbaldwin
manager: MBaldwin
ms.assetid: c8abd340-5ed4-42ec-b83f-4d679b61494d
ms.service: security
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: mbaldwin
ms.custom: seodec18
ms.openlocfilehash: c36f9506665f573a2e7990102af81a9de088e95b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501648"
---
# <a name="quickstart-encrypt-a-windows-iaas-vm-with-azure-powershell"></a>Краткое руководство. Шифрование виртуальной машины IaaS под управлением Windows с помощью Azure PowerShell

Служба шифрования дисков Azure помогает шифровать диски виртуальной машины IaaS под управлением Windows и Linux. Решение интегрируется с Azure Key Vault, помогая управлять ключами шифрования дисков и секретами. При использовании шифрования дисков Azure виртуальные машины гарантировано защищены с помощью отраслевой технологии шифрования. В этом кратком руководстве показано, как создать виртуальную машину Windows Server 2016 и зашифровать диск операционной системы.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Windows PowerShell ISE
- Установите модуль [Azure PowerShell последней версии](/powershell/azure/install-az-ps) или обновите модуль до последней версии
    - Версия модуля Az должна быть не ниже 1.0.0. Используйте `Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path` для проверки версии.
- Копия сценария [предварительных требований для шифрования дисков Azure](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
    - Если у вас уже есть этот сценарий, загрузите новую копию, так как он был недавно изменен. 
    - Используя клавиши **CTRL+A**, выделите весь текст на странице, а затем нажмите **CTRL+C**, чтобы скопировать весь текст в Блокнот.
    - Сохраните файл как **ADEPrereqScript.ps1**.


## <a name="sign-in-to-azure"></a>Вход в Azure

1. Щелкните правой кнопкой мыши **Интегрированная среда сценариев Windows PowerShell** и выберите пункт **Запуск от имени администратора**.
1. В окне **Administrator: Windows PowerShell ISE** (Администратор: интегрированная среда сценариев Windows PowerShell) щелкните **Представление**, а затем — **Show Script Pane** (Показать область сценариев).
1. В области сценариев введите следующий командлет: 

     ```azurepowershell
      Connect-AzAccount
     ```

1. Щелкните зеленую стрелку для **запуска сценария** или воспользуйтесь клавишей F5. 
2. Используйте интерактивный вход, чтобы завершить подключение к учетной записи Azure.
3. Скопируйте возвращенный **идентификатор подписки** для использования при запуске следующего сценария PowerShell. 

## <a name="bkmk_PrereqScript"></a> Запуск сценария предварительных требований для шифрования дисков Azure
 Файл **ADEPrereqScript.ps1** создаст группу ресурсов, хранилище ключей и настроит политику доступа к хранилищу ключей. Сценарий также создает блокировку ресурса для хранилища ключей, чтобы защитить его от случайного удаления.  

1. В окне **Administrator: Windows PowerShell ISE** (Администратор: интегрированная среда сценариев Windows PowerShell) щелкните **Файл**, а затем — **Открыть**. Перейдите к файлу **ADEPrereqScript.ps1** и дважды щелкните его. Сценарий откроется в области сценариев.
2. Щелкните зеленую стрелку для **запуска сценария** или воспользуйтесь клавишей F5, чтобы запустить сценарий. 
3. Введите имена для новой **группы ресурсов** и нового **хранилища ключей**. Не используйте существующую группу ресурсов или хранилище ключей для этого краткого руководства, так как позже мы ее удалим. 
4. Введите расположение, в котором вы хотите создать ресурсы, например **EastUS**. Получите список расположений с помощью команды `Get-AzLocation`.
5. Скопируйте ваш **идентификатор подписки**. Вы можете получить идентификатор подписки с помощью команды `Get-AzSubscription`.  
6. Щелкните зеленую стрелку для **запуска сценария**. 
7. Скопируйте возвращенные значения **DiskEncryptionKeyVaultUrl** и **DiskEncryptionKeyVaultId**, которые будут использоваться позже.

![Сценарий предварительных требований для шифрования дисков Azure, выполняемый в интегрированной среде сценариев PowerShell](media/azure-security-disk-encryption/ade-prereq-script.PNG)


## <a name="create-a-virtual-machine"></a>Создание виртуальной машины 
Теперь необходимо создать виртуальную машину, чтобы вы могли зашифровать ее диск. Сценарий, который вы будете использовать, создает виртуальную машину Windows Server 2016 с 8 ГБ ОЗУ и диском ОС размером 30 ГБ. 

1. Скопируйте сценарий в область сценариев **Administrator: Windows PowerShell ISE** (Администратор: интегрированная среда сценариев Windows PowerShell) и измените первые три переменные. Группа ресурсов и расположение должны быть такими же, как для [сценария предварительных требований](#bkmk_PrereqScript).  

   ```azurepowershell
    # Variables for common values
    $resourceGroup = "MySecureRG"
    $location = "EastUS"
    $vmName = "MySecureVM"
    
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
    
    # Create a resource group
    New-AzResourceGroup -Name $resourceGroup -Location $location
    
    # Create a subnet configuration
    $subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
    
    # Create a virtual network
    $vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
      -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
    
    # Create a public IP address and specify a DNS name
    $pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
      -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
    
    # Create an inbound network security group rule for port 3389
    $nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
      -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
      -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
    
    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
      -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    
    # Create a virtual machine configuration
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D2_v3 | `
    Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter-smalldisk -Version latest | `
    Add-AzVMNetworkInterface -Id $nic.Id
    
    # Create a virtual machine
    New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
   ```

2. Щелкните зеленую стрелку для **запуска сценария**, чтобы создать виртуальную машину.  


## <a name="encrypt-the-disk-of-the-vm"></a>Шифрование диска виртуальной машины
Теперь, после создания и настройки хранилища ключей и виртуальной машины, можно зашифровать диск с помощью командлета **Set-AzVmDiskEncryptionExtension**. 
 
1. Выполните следующий командлет, чтобы зашифровать диск виртуальной машины:

    ```azurepowershell
     Set-AzVmDiskEncryptionExtension -ResourceGroupName "MySecureRG" -VMName "MySecureVM" `
     -DiskEncryptionKeyVaultId "<Returned by the prerequisites script>" -DiskEncryptionKeyVaultUrl "<Returned by the prerequisites script>"
     ```


1. После завершения шифрования можно проверить, зашифрован ли диск, с помощью следующего командлета: 

     ```azurepowershell
     Get-AzVmDiskEncryptionStatus -ResourceGroupName "MySecureRG" -VMName "MySecureVM"
     ```
    ![выходные данные Get-AzVmDiskEncryptionStatus.](media/azure-security-disk-encryption/ade-get-encryption-status.PNG)
    
## <a name="clean-up-resources"></a>Очистка ресурсов
 Файл **ADEPrereqScript.ps1** создает блокировку ресурса для хранилища ключей. Чтобы очистить ресурсы, использованные в этом кратком руководстве, необходимо сначала удалить блокировку ресурса, а затем — группу ресурсов. 

1. Удалите блокировку ресурса из хранилища ключей.

     ```azurepowershell
     $LockId =(Get-AzResourceLock -ResourceGroupName "MySecureRG" -ResourceName "MySecureVault" -ResourceType "Microsoft.KeyVault/vaults").LockID 
     Remove-AzResourceLock -LockID $LockId
      ```
    
2. Удалите группу ресурсов. Это также приведет к удалению всех ресурсов в группе. 
     ```azurepowershell
      Remove-AzResourceGroup -Name "MySecureRG"
      ```

## <a name="next-steps"></a>Дополнительная информация
Перейдите к следующей статье, чтобы узнать больше о предварительных требованиях для шифрования дисков Azure для виртуальных машин IaaS.

> [!div class="nextstepaction"]
> [Предварительные требования для шифрования дисков Azure](azure-security-disk-encryption-prerequisites.md)
