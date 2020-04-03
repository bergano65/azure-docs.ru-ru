---
title: Azure Disk Encryption scenarios on Linux VMs (Сценарии шифрования дисков Azure для виртуальных машин Linux)
description: В этой статье содержатся инструкции по шифрованию дисков Microsoft Azure для Linux VMs для различных сценариев
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: cf6f53639cff23a9e709b44a1ddf1332df2164b7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586044"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption scenarios on Linux VMs (Сценарии шифрования дисков Azure для виртуальных машин Linux)

Лазурное шифрование дисков использует функцию DM-Crypt Linux для обеспечения громкости шифрования для ОС и дисков данных виртуальных машин Azure (VM) и интегрировано с Azure Key Vault, чтобы помочь вам управлять и управлять ключами шифрования диска и секретами. Для получения обзора службы [см.](disk-encryption-overview.md)

Существует множество сценариев шифрования дисков, и шаги могут меняться в зависимости от сценария. Следующие разделы более подробно охватывают сценарии для Linux VMs.

Вы можете применять шифрование диска только к виртуальным машинам [поддерживаемых размеров VM и операционным системам.](disk-encryption-overview.md#supported-vms-and-operating-systems) Вы также должны выполнить следующие предпосылки:

- [Дополнительные требования к ВМ](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Требования к сети](disk-encryption-overview.md#networking-requirements)
- [Требования к хранению ключей шифрования](disk-encryption-overview.md#encryption-key-storage-requirements)

Во всех случаях следует [сделать снимок](snapshot-copy-managed-disk.md) и/или создать резервную перепалку до шифрования дисков. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. После создания резервного копирования можно использовать [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) для шифрования управляемых дисков, указав параметр -skipVmBackup. Дополнительные сведения см. в статье [Резервное копирование и восстановление зашифрованных виртуальных машин с помощью службы Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Если ранее для шифрования VM использовалось шифрование Azure Disk с Azure AD, необходимо продолжить использование этой опции для шифрования VM. Подробнее о [шифровании azure Disk с помощью Azure AD (предыдущий релиз).](disk-encryption-overview-aad.md) 
>
> - При шифровании объемов Linux OS VM следует считать недоступным. Мы настоятельно рекомендуем избегать логинов SSH во время шифрования, чтобы избежать проблем, блокирующих любые открытые файлы, которые должны быть доступны во время процесса шифрования. Чтобы проверить прогресс, используйте [Get-AzVMDiskEncryptionStatus PowerShell](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet или [vm шифрование показать](/cli/azure/vm/encryption#az-vm-encryption-show) команду CLI. Этот процесс может занять несколько часов для тома операционной системы 30 ГБ, а также дополнительное время для шифрования томов данных. Если не используется параметр шифрования всех форматов, время шифрования тома данных будет соответствовать размеру и количеству томов данных. 
> - Отключение шифрования на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

## <a name="install-tools-and-connect-to-azure"></a>Установка инструментов и подключение к Azure

Шифрование azure Disk может быть включено и управляется через [Azure CLI](/cli/azure) и [Azure PowerShell.](/powershell/azure/new-azureps-module-az) Для этого необходимо установить инструменты локально и подключиться к подписке Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) — это интерфейс командной строки для управления ресурсами Azure. Этот интерфейс обеспечивает гибкие функции подачи запросов, выполнение длительных операций без блокировки и простое создание скриптов. Вы можете установить его локально, выяснив следующие шаги в [установке Azure CLI.](/cli/azure/install-azure-cli?view=azure-cli-latest)

 

Чтобы [войти в учетную запись Azure с помощью Azure CLI,](/cli/azure/authenticate-azure-cli)используйте команду [входа в az.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Если вы хотите использовать для входа определенный клиент, выполните команду:
    
```azurecli
az login --tenant <tenant>
```

Если у вас есть несколько подписок и нужно указать конкретную, получите список подписок с помощью команды [az account list](/cli/azure/account#az-account-list) и задайте требуемую, используя команду [az account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Дополнительные сведения можно найти в документации по [началу работы с Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Модуль Azure PowerShell az](/powershell/azure/new-azureps-module-az) предоставляет набор cmdlets, который использует модель управления [ресурсами Azure](../../azure-resource-manager/management/overview.md) для управления ресурсами Azure. Вы можете использовать его в браузере с [Azure Cloud Shell,](../../cloud-shell/overview.md)или вы можете установить его на локальной машине, используя инструкции в [модуле Установить Azure PowerShell.](/powershell/azure/install-az-ps) 

Если вы уже установили его локально, убедитесь, что вы используете последнюю версию версии пакета SDK для Azure PowerShell для настройки шифрования дисков Azure. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Чтобы [войти в учетную запись Azure в Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)используйте cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Если у вас несколько подписок и вы хотите указать одну из них, используйте cmdlet [Get-AzSubscription,](/powershell/module/Az.Accounts/Get-AzSubscription) чтобы перечислить их, а затем Смдлет [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Запуск cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) позволит проверить, выбрана ли была выбрана правильная подписка.

Для подтверждения установки cmdlets шифрования дисков Azure, используйте [Cmdlet Get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Для получения дополнительной [Getting started with Azure PowerShell](/powershell/azure/get-started-azureps)информации см. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Включить шифрование на существующем или запущенном Linux VM
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. Сведения о схеме для расширения виртуальной машины см. в статье [Шифрование дисков Azure для Linux ](../extensions/azure-disk-enc-linux.md).

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Снимок управляемого диска можно взять с портала или через [резервное копирование Azure.](../../backup/backup-azure-vms-encryption.md) Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервного копирования cmdlet Set-AzVMDiskEncryptionExtension можно использовать для шифрования управляемых дисков, указав параметр -skipVmBackup. Команда Set-AzVMDiskEncryptionExtension выйдет из строя против управляемых дисковых VMs до тех пор, пока не будет сделана резервная система и не будет указан этот параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью Azure CLI 

Вы можете включить шифрование диска на зашифрованном VHD, установив и используя инструмент командной строки [Azure CLI.](/cli/azure/?view=azure-cli-latest) Его можно использовать в браузере с [Azure Cloud Shell](../../cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell. Для включения шифрования существующих или запущенных Linux VM в Azure используйте следующие команды CLI:

Используйте [шифрование az vm, позволяющее](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) включить шифрование на работающей виртуальной машине в Azure.

- **Шифрование работающей виртуальной машины:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Шифрование работающей виртуальной машины с использованием KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br>
Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Проверка дисков зашифрованы:** Чтобы проверить состояние шифрования VM, используйте команду [шоу-шоу-шоу az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Отключение шифрования.** Чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью PowerShell
Используйте [cmdlet Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) чтобы включить шифрование на работающей виртуальной машине в Azure. [Сфотографать](snapshot-copy-managed-disk.md) и/или резервное копирование VM с [помощью резервного копирования Azure](../../backup/backup-azure-vms-encryption.md) перед шифрованием дисков. Параметр -skipVmBackup уже указан в сценариях PowerShell для шифрования подработаного Linux VM.

-  **Шифрование бегущего VM:** Приведенный ниже сценарий инициализирует переменные и запускает cmdlet Set-AzVMDiskEncryptionExtension. В качестве предварительных условий была создана группа ресурсов VM и хранилище ключей. Замените MyVirtualMachineResourceGroup, MySecureVM и MySecureVault своими ценностями. Измените параметр -VolumeType, чтобы указать, какие диски вы шифруете.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Шифрование работающей виртуальной машины с использованием KEK.** При шифровании дисков данных, а не диска операционной системы, может потребоваться добавить параметр -VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 
    
- **Проверка дисков зашифрованы:** Чтобы проверить состояние шифрования VM, используйте [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Отключить шифрование диска:** Чтобы отключить шифрование, используйте смдлет ['Ddlet Отключительного-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Для виртуальных машин Linux отключение шифрования возможно только для томов данных.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Включить шифрование на существующем или запущенном Linux VM с шаблоном

Вы можете включить шифрование диска на существующем или запущенном Linux VM в Azure с помощью [шаблона Resource Manager.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)

1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите **Создать** для включения шифрования на существующих или запущенных VM.

В следующей таблице перечислены параметры шаблона Resource Manager для имеющихся или работающих виртуальных машин.

| Параметр | Описание |
| --- | --- |
| vmName | Имя виртуальной машины для выполнения операции шифрования. |
| keyVaultName | Название хранилища ключей, на которое должен быть загружен ключ шифрования. Вы можете получить его с `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` помощью cmdlet `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`или команды Azure CLI.|
| keyVaultResourceGroup | Название группы ресурсов, содержащей хранилище ключей. |
|  keyEncryptionKeyURL | URL ключа шифрования, который используется для шифрования ключа шифрования. Это необязательный параметр, если выбрать **nokek** из раскрывающегося списка UseExistingKek. Если из раскрывающегося списка UseExistingKek выбрано значение **kek**, то потребуется ввести значение _keyEncryptionKeyURL_. |
| volumeType | Тип тома, для которого будет выполняться шифрование. Допустимые значения: _OS_, _Data_ и _All_. 
| forceUpdateTag | Передает уникальное значение, такое как GUID, каждый раз, когда операция должна выполняться принудительно. |
| location | Расположение для всех ресурсов. |

Для получения дополнительной информации о настройке шаблона [Azure Disk Encryption for Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/azure-disk-enc-linux)шифрования дисков Linux VM см.

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Используйте функцию EncryptFormatAll для дисков данных на Linux VMs

Параметр **EncryptFormatAll** сокращает время шифрования дисков данных Linux. Разделы, отвечающие определенным критериям, будут отформатированы (с текущей файловой системой), а затем будут восстановлены туда, где она находилась до выполнения команды. Если вы хотите исключить диск данных, который соответствует критериям, можно отключить его перед выполнением команды.

 После запуска этой команды все диски, которые были установлены ранее, будут отформатированы, а слой шифрования будет запущен поверх теперь пустого диска. Если выбран этот параметр, временный диск с ресурсами, подключенный к виртуальной машине, также будет зашифрован. Если временный диск сбрасывается, при следующей возможности он будет переформатирован и повторно зашифрован для виртуальной машины с помощью шифрования дисков Azure. Как только диск ресурса будет зашифрован, [агент Microsoft Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) не сможет управлять диском ресурса и включить файл своп, но вы можете вручную настроить файл подкачки.

>[!WARNING]
> Параметр EncryptFormatAll не следует использовать, если в томах данных виртуальной машины есть требуемые данные. Вы можете исключить диски из шифрования, отключив их. Сначала следует попробовать параметр EncryptFormatAll на тестовой виртуальной машине, понять его функцию и последствия, а уже потом использовать на рабочих виртуальных машинах. Параметр EncryptFormatAll форматирует диск данных, и все данные на этом диске будут потеряны. Прежде чем продолжить, убедитесь, что диски, которые вы хотите исключить, отключены надлежащим образом. </br></br>
 >Если вы устанавливаете этот параметр при обновлении настроек шифрования, это может привести к перезагрузке перед фактическим шифрованием. В этом случае вы также хотите удалить диск, который вы не хотите отформатировать из файла fstab. Аналогично, перед началом операции шифрования нужно добавить в FSTAB-файл раздел, который нужно отформатировать и зашифровать. 

### <a name="encryptformatall-criteria"></a> Критерии EncryptFormatAll
Параметр проходит через все разделы и шифрует их, если они соответствуют **всем** из приведенных ниже критериев: 
- это не корневой или загрузочный раздел, а также не раздел операционной системы;
- этот раздел еще не зашифрован;
- это не том BEK;
- это не том RAID;
- это не том LVM;
- он подключен.

Выполняйте шифрование дисков, которые составляют том RAID или LVM, а не самого тома RAID или LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a> Использование параметра EncryptFormatAll с Azure CLI
Используйте [шифрование az vm, позволяющее](/cli/azure/vm/encryption#az-vm-encryption-enable) включить шифрование на работающей виртуальной машине в Azure.

-  **Шифрование работающей виртуальной машины с использованием параметра EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a> Использование параметра EncryptFormatAll с командлетом PowerShell
Используйте [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet с параметром EncryptFormatAll. 

**Шифрование запущенного VM с помощью EncryptFormatAll:** Например, приведенный ниже сценарий инициализирует переменные и запускает cmdlet Set-AzVMDiskEncryptionExtension с параметром EncryptFormatAll. В качестве предварительных условий были созданы ресурсная группа, VM и хранилище ключей. Замените MyVirtualMachineResourceGroup, MySecureVM и MySecureVault своими ценностями.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a> Использование параметра EncryptFormatAll с диспетчером логических томов (LVM) 
Рекомендуем установить LVM-on-crypt. Для всех следующих примеров замените путь к устройству и точки подключения в соответствии с вашим вариантом использования. Эту настройку можно выполнить следующим образом.

- Добавить диски данных, которые составляют виртуальную машину.
- Форматировать, подключить и добавить эти диски в FSTAB-файл.

    1. Выберите стандарт раздела, создайте раздел, охватывающий весь диск, а затем отформатируйте раздел. Здесь мы используем символические ссылки, созданные Azure. Использование символических ссылок позволяет избежать проблем, связанных с изменениями имен устройств. Дополнительные сведения см. в статье [Устранение неполадок при изменении имени устройства виртуальной машины Linux](troubleshoot-device-names-problems.md).
    
         ```azurepowershell-interactive
         parted /dev/disk/azure/scsi1/lun0 mklabel gpt
         parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
         
         mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
         ```
    
    1. Подключите диски.
         
         `mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint`
    
    1. Добавьте сведения в FSTAB-файл.
         
        `echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab`
    
    1. Выполнить Set-AzVMDiskEncryptionExtension PowerShell cmdlet с -EncryptFormatAll для шифрования этих дисков.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Настройте диспетчер логических томов поверх новых дисков. Обратите внимание, что зашифрованные диски разблокируются после завершения загрузки виртуальной машины. Таким образом, подключение диспетчера логических томов также придется отложить.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Новые VMs, созданные из шифрования клиентов VHD и ключей шифрования
В этом сценарии шифрование можно включить с помощью командлетов PowerShell или команд интерфейса командной строки. 

Используйте инструкции в тех же скриптах шифрования Azure Disk для подготовки предварительно зашифрованных изображений, которые могут быть использованы в Azure. После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.

* [Подготовка предварительно зашифрованного виртуального жесткого диска Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Снимок управляемого диска можно взять с портала или использовать [резервное копирование Azure.](../../backup/backup-azure-vms-encryption.md) Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервного копирования cmdlet Set-AzVMDiskEncryptionExtension можно использовать для шифрования управляемых дисков, указав параметр -skipVmBackup. Команда Set-AzVMDiskEncryptionExtension выйдет из строя против управляемых дисковых VMs до тех пор, пока не будет сделана резервная система и не будет указан этот параметр. 
>
> Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Используйте Azure PowerShell для шифрования VMs с предварительно зашифрованными VHD 
Вы можете включить шифрование диска на зашифрованном VHD с помощью Смдлета PowerShell [Set-AzVMOSDisk.](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) Приведенный ниже пример имеет некоторые общие параметры. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Включение шифрования на добавленном диске данных

Новый диск данных можно добавить с помощью команды [az vm disk attach](add-disk.md) или [через портал Azure](attach-disk-portal.md). Прежде чем запустить шифрование, необходимо подключить только что присоединенный диск данных. Нужно запросить шифрование диска данных, так как диск будет неработоспособным во время шифрования. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Включение шифрования на добавленном диске данных с помощью интерфейса командной строки Azure

 Если VM ранее был зашифрован с "Все", то параметр типа тома должен оставаться "Все". Значение "All" включает как диски ОС, так и диски данных. Если VM ранее был зашифрован с типом громкости "OS", то параметр типа --тома должен быть изменен на "Все", так что и ОС и новый диск данных будут включены. Если виртуальная машина была зашифрована с типом тома "Data", то в дальнейшем он может оставаться со значением "Data", как это показано ниже. Добавления и присоединения нового диска данных к виртуальной машине недостаточно для подготовки к шифрованию. Вновь подключенный диск также должен быть отформатирован и правильно подключен к виртуальной машине перед включением шифрования. В Linux диск монтируется в /etc/fstab с [постоянным именем блочного устройства](troubleshoot-device-names-problems.md).  

В отличие от синтаксиса Powershell, интерфейс командной строки (CLI) не требует предоставления уникальной версии последовательности при включении шифрования. CLI автоматически создает и использует свое собственное уникальное значение версии последовательности.

-  **Шифрование томов данных работающей виртуальной машины:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Шифрование томов данных работающей виртуальной машины с использованием KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Включение шифрования на добавленном диске данных с помощью Azure PowerShell
 При использовании PowerShell для шифрования нового диска для Linux необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Приведенный ниже сценарий создает GUID для версии последовательности. [Сфотографать](snapshot-copy-managed-disk.md) и/или резервное копирование VM с [помощью резервного копирования Azure](../../backup/backup-azure-vms-encryption.md) перед шифрованием дисков. Параметр -skipVmBackup уже указан в скриптах PowerShell для шифрования недавно добавленного диска данных.
 

-  **Шифрование объемов данных бегущего VM:** Приведенный ниже сценарий инициализирует переменные и запускает cmdlet Set-AzVMDiskEncryptionExtension. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените MyVirtualMachineResourceGroup, MySecureVM и MySecureVault своими ценностями. Допустимые значения параметра -VolumeType: "All", "OS" и "Data". Если VM ранее был зашифрован с типом громкости "OS" или "All", то параметр -VolumeType должен быть изменен на "Все", так что и ОС и новый диск данных будут включены.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Шифрование томов данных работающей виртуальной машины с использованием KEK.** Допустимые значения для параметра -VolumeType: "All", "OS" и "Data". Если виртуальная машина была зашифрована с типом тома "OS" или "All", в дальнейшем параметр -VolumeType следует заменить на "All", чтобы были включены диск ОС и новый диск данных.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Синтаксис по стоимости параметра диск-шифрования-ключевого ключа представляет собой полную строку идентификатора: /подписка-id-guid/resourceGroups/"KVresource-group-name"/providers/Microsoft.KeyVault/vaults/</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 


## <a name="disable-encryption-for-linux-vms"></a>Отключение шифрования для виртуальных машин Linux
Вы можете отключить шифрование с помощью Azure PowerShell, Azure CLI или шаблона Resource Manager. 

>[!IMPORTANT]
>Отключение шифрования, выполняемого с помощью службы шифрования Azure, на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

- **Отключено шифрование диска с помощью Azure PowerShell:** Чтобы отключить шифрование, используйте смдлет ['Ddlet Отключительного-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Отключение шифрования с помощью Azure CLI.** Для отключения шифрования используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Отключить шифрование с помощью шаблона «Менеджер ресурсов»:** Используйте [шифрование Отключительных на бегущий шаблон Linux VM,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) чтобы отключить шифрование.
     1. Нажмите кнопку **Развернуть в Azure**.
     2. Выберите подписку, группу ресурсов, расположение, виртуальную машину, условия использования и соглашение.

## <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

Шифрование azure Disk не работает для следующих сценариев Linux, функций и технологий:

- Шифрование базового уровня VM или VM, созданных с помощью классического метода создания VM.
- Отключение шифрования на диске ОС или диске данных Linux VM при шифровании диска ОС.
- Шифрование диска ОС для наборов виртуальных машин Linux.
- Шифрование пользовательских изображений на Linux VMs.
- Интеграция с системой управления ключами.
- служба файлов Azure (общая файловая система);
- сетевая файловая система (NFS);
- динамические тома;
- Эфемерные оС диски.
- Шифрование общих/распределенных файловых систем, таких как (но не ограничиваемый): DFS, GFS, DRDB и CephFS.
- Перемещение зашифрованного VM на другую подписку.
- Краш-дамт ядра (kdump).
- Oracle ACFS (система файлов кластера ASM)
- VMs Gen2 (см.: [Поддержка поколения 2 VMs на Azure](generation-2.md#generation-1-vs-generation-2-capabilities))
- VMs серии Lsv2 (см.: [Lsv2-серия](../lsv2-series.md))

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о шифровании дисков Azure](disk-encryption-overview.md)
- [Примеры скриптов шифрования дисков Azure](disk-encryption-sample-scripts.md)
- [Устранение неполадок с шифрованием дисков Azure](disk-encryption-troubleshooting.md)
