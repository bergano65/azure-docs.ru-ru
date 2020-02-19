---
title: Шифрование управляемых дисков Azure на стороне сервера (PowerShell)
description: Служба хранилища Azure защищает ваши данные путем шифрования неактивных данных перед их сохранением в кластерах хранилища. Для шифрования управляемых дисков можно использовать ключи, управляемые корпорацией Майкрософт, или ключи, управляемые клиентом, для управления шифрованием с помощью собственных ключей.
author: roygara
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: bc15ee42fd7ef8e41b332104b28af808c336789f
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430420"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Шифрование на стороне сервера для управляемых дисков Azure

Управляемые диски Azure автоматически шифруют данные по умолчанию при сохранении их в облаке. Шифрование на стороне сервера защищает ваши данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации. Данные на управляемых дисках Azure шифруются прозрачно с помощью 256-битного [шифрования AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), одним из наиболее подданных блоков блочных шифров и совместимым с FIPS 140-2.   

Шифрование не влияет на производительность управляемых дисков. Дополнительные затраты на шифрование не взимается.

Дополнительные сведения о криптографических модулях, лежащих в основе управляемых дисков Azure, см. в разделе [API шифрования: следующее поколение.](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Сведения об управлении ключами шифрования

Для шифрования управляемого диска можно использовать ключи, управляемые платформой, или можно управлять шифрованием с помощью собственных ключей. Если вы решили управлять шифрованием с помощью собственных ключей, можно указать ключ, *управляемый клиентом* , который будет использоваться для шифрования и расшифровки всех данных на управляемых дисках. 

В следующих разделах каждый из параметров управления ключами описан более подробно.

## <a name="platform-managed-keys"></a>Ключи, управляемые платформой

По умолчанию управляемые диски используют управляемые платформой ключи шифрования. Начиная с 10 июня 2017, все новые управляемые диски, моментальные снимки, образы и новые данные, записанные на существующие управляемые диски, автоматически шифруются с помощью управляемых платформой ключей. 

## <a name="customer-managed-keys"></a>Ключи, управляемые клиентом

Вы можете управлять шифрованием на уровне каждого управляемого диска с помощью собственных ключей. Шифрование на стороне сервера для управляемых дисков с ключами, управляемыми клиентом, обеспечивает интегрированную работу с Azure Key Vault. Вы можете либо импортировать [ключи RSA](../../key-vault/key-vault-hsm-protected-keys.md) в Key Vault, либо создать новые ключи rsa в Azure Key Vault. Управляемые диски Azure обрабатывают шифрование и расшифровку полностью прозрачным способом с помощью [шифрования конвертов](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Он шифрует данные с помощью ключа шифрования данных на основе [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), который, в свою очередь, защищен с помощью ключей. Вы должны предоставить доступ к управляемым дискам в Key Vault, чтобы использовать ключи для шифрования и расшифровки DEK. Это обеспечивает полный контроль над данными и ключами. Вы можете отключить ключи или отозвать доступ к управляемым дискам в любое время. Кроме того, можно проводить аудит использования ключа шифрования с Azure Key Vault мониторинга, чтобы обеспечить доступ к ключам только управляемых дисков или других доверенных служб Azure.

На следующей схеме показано, как управляемые диски используют Azure Active Directory и Azure Key Vault для выполнения запросов с помощью управляемого клиентом ключа:

![Управляемый диск и рабочий процесс с управляемыми клиентом ключами. Администратор создает Azure Key Vault, создает набор шифрования дисков и настраивает набор шифрования дисков. Набор связан с виртуальной машиной, что позволяет диску использовать Azure AD для проверки подлинности.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


В следующем списке приведено более подробное описание схемы:

1. Администратор Azure Key Vault создает ресурсы хранилища ключей.
1. Администратор хранилища ключей импортирует ключи RSA в Key Vault или создает новые ключи RSA в Key Vault.
1. Этот администратор создает экземпляр ресурса для набора шифрования диска, указывая идентификатор Azure Key Vault и URL-адрес ключа. Набор шифрования дисков — это новый ресурс, который появился для упрощения управления ключами для управляемых дисков. 
1. При создании набора шифрования дисков [назначенное системой управляемое удостоверение](../../active-directory/managed-identities-azure-resources/overview.md) создается в Azure Active Directory (AD) и связывается с набором шифрования дисков. 
1. Затем администратор хранилища ключей Azure предоставляет управляемому удостоверению разрешение на выполнение операций в хранилище ключей.
1. Пользователь виртуальной машины создает диски, связывая их с набором шифрования дисков. Пользователь виртуальной машины может также включить шифрование на стороне сервера с помощью управляемых клиентом ключей для существующих ресурсов, связав их с набором шифрования дисков. 
1. Управляемые диски используют управляемое удостоверение для отправки запросов в Azure Key Vault.
1. Для чтения или записи данных управляемые диски отправляют запросы в Azure Key Vault для шифрования (переноса) и расшифровки (распаковки) ключа шифрования данных для выполнения шифрования и расшифровки данных. 

Чтобы отозвать доступ к ключам, управляемым клиентом, см. статью [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) и [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Отзыв доступа блокирует доступ ко всем данным в учетной записи хранения, так как ключ шифрования недоступен для службы хранилища Azure.

### <a name="supported-regions"></a>Поддерживаемые регионы

В настоящее время поддерживаются только следующие регионы:

- Доступно как общедоступное предложение в восточной части США, Западная часть США 2, в Южной Центральной части США, южная часть Соединенного Королевства регионах.
- Доступно в виде общедоступной предварительной версии в западной части США, восточной части США 2, в центральной части Канады и в Северной Европе.

### <a name="restrictions"></a>Ограничения

Сейчас ключи, управляемые клиентом, имеют следующие ограничения.

- Поддерживаются только [«мягкие» и «жесткие» ключи RSA](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) размером 2080, а другие ключи и размеры отсутствуют.
- Диски, созданные на основе пользовательских образов, зашифрованных с помощью шифрования на стороне сервера и ключей, управляемых клиентом, должны быть зашифрованы с помощью тех же самых управляемых клиентом ключей и должны находиться в одной подписке.
- Моментальные снимки, созданные на основе дисков, зашифрованных с помощью шифрования на стороне сервера и ключей, управляемых клиентом, должны быть зашифрованы с помощью тех же управляемых клиентом ключей.
- Пользовательские образы, зашифрованные с помощью шифрования на стороне сервера и управляемых клиентом ключей, нельзя использовать в коллекции общих образов.
- Все ресурсы, связанные с ключами, управляемыми клиентом (хранилища ключей Azure, наборы шифрования дисков, виртуальные машины, диски и моментальные снимки), должны находиться в одной подписке и регионе.
- Диски, моментальные снимки и образы, зашифрованные с помощью управляемых клиентом ключей, не могут переместиться в другую подписку.
- Если вы используете портал Azure для создания набора шифрования диска, использовать моментальные снимки сейчас нельзя.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Настройка Azure Key Vault и Дискенкриптионсет

1. Убедитесь, что установлена последняя [версия Azure PowerShell](/powershell/azure/install-az-ps), и вы вошли в учетную запись Azure с помощью Connect-азаккаунт.

1. Создайте экземпляр Azure Key Vault и ключ шифрования.

    При создании экземпляра Key Vault необходимо включить защиту с обратимым удалением и очисткой. Обратимое удаление гарантирует, что Key Vault будет содержать удаленный ключ для заданного срока хранения (90 день по умолчанию). Очистка защиты гарантирует, что удаленный ключ нельзя удалить навсегда, пока не истечет срок хранения. Эти параметры защищают от потери данных из-за случайного удаления. Эти параметры являются обязательными при использовании Key Vault для шифрования управляемых дисков.

    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Создайте экземпляр Дискенкриптионсет. 
    
    ```powershell
    $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned

    $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
    ```

1.  Предоставьте ресурсу Дискенкриптионсет доступ к хранилищу ключей.

    > [!NOTE]
    > Создание удостоверения Дискенкриптионсет в Azure Active Directory может занять несколько минут Azure. Если при выполнении следующей команды появляется сообщение об ошибке, например "не удается найти объект Active Directory", подождите несколько минут и повторите попытку.
    
    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
     
    New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Создание виртуальной машины с помощью образа Marketplace, шифрование дисков операционной системы и данных с помощью управляемых клиентом ключей

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Создайте пустой диск, зашифрованный с помощью шифрования на стороне сервера, с ключами, управляемыми клиентом, и подключите его к виртуальной машине.

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-unattached-managed-disks"></a>Шифровать существующие неподключенные управляемые диски 

Существующие диски не должны быть подключены к работающей виртуальной машине, чтобы их можно было зашифровать с помощью следующего скрипта:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Создание масштабируемого набора виртуальных машин с помощью образа Marketplace, шифрование дисков операционной системы и данных с помощью управляемых клиентом ключей

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

> [!IMPORTANT]
> Управляемые клиентом ключи используют управляемые удостоверения для ресурсов Azure, функции Azure Active Directory (Azure AD). При настройке управляемых пользователем ключей управляемое удостоверение автоматически назначается вашим ресурсам. При последующем перемещении подписки, группы ресурсов или управляемого диска из одного каталога Azure AD в другой управляемое удостоверение, связанное с управляемыми дисками, не передается в новый клиент, поэтому управляемые клиентом ключи могут перестать работать. Дополнительные сведения см. [в статье передача подписки между каталогами Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Управляемые клиентом ключи используют управляемые удостоверения для ресурсов Azure, функции Azure Active Directory (Azure AD). При настройке управляемых пользователем ключей управляемое удостоверение автоматически назначается вашим ресурсам. При последующем перемещении подписки, группы ресурсов или управляемого диска из одного каталога Azure AD в другой управляемое удостоверение, связанное с управляемыми дисками, не передается в новый клиент, поэтому управляемые клиентом ключи могут перестать работать. Дополнительные сведения см. [в статье передача подписки между каталогами Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Шифрование на стороне сервера и шифрование дисков Azure

[Шифрование дисков Azure](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) использует функцию [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) в Windows и функцию [DM-](https://en.wikipedia.org/wiki/Dm-crypt) Encryption в Linux для шифрования управляемых дисков с помощью управляемых клиентом ключей на гостевой виртуальной машине.  Шифрование на стороне сервера с помощью управляемых клиентом ключей улучшает работу с ADE, позволяя использовать любые типы ОС и образы для виртуальных машин путем шифрования данных в службе хранилища.

## <a name="next-steps"></a>Дальнейшие действия

- [Изучите шаблоны Azure Resource Manager для создания зашифрованных дисков с помощью управляемых клиентом ключей](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Что такое хранилище ключей Azure?](../../key-vault/key-vault-overview.md)
- [Репликация компьютеров с помощью дисков с поддержкой ключей, управляемых клиентом](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Настройка аварийного восстановления виртуальных машин VMware в Azure с помощью PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Настройка аварийного восстановления в Azure для виртуальных машин Hyper-V с помощью PowerShell и Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
