---
title: Сценарии шифрования дисков Azure на виртуальных машинах Linux
description: В этой статье приведены инструкции по включению Microsoft Azure шифрования дисков для виртуальных машин Linux в различных сценариях.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e7f8c029c5ceff0865b060cf8edc794454d6c282
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828470"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Сценарии шифрования дисков Azure на виртуальных машинах Linux

Шифрование дисков Azure использует функцию DM-Encryption в Linux для обеспечения шифрования томов и дисков данных виртуальных машин Azure, а также интегрируется с Azure Key Vault, помогающим управлять ключами и секретами шифрования диска и управлять ими. Общие сведения о службе см. в статье [Шифрование дисков Azure для виртуальных машин Linux](disk-encryption-overview.md).

Существует много сценариев шифрования дисков, и эти действия могут отличаться в зависимости от сценария. В следующих разделах более подробно рассматриваются сценарии для виртуальных машин Linux.

Шифрование дисков можно применить только к виртуальным машинам [поддерживаемых размеров виртуальных машин и операционных систем](disk-encryption-overview.md#supported-vms-and-operating-systems). Необходимо также выполнить следующие предварительные требования.

- [Дополнительные требования для виртуальных машин](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Требования к сети](disk-encryption-overview.md#networking-requirements)
- [Требования к хранилищу ключей шифрования](disk-encryption-overview.md#encryption-key-storage-requirements)

Во всех случаях следует создать [моментальный снимок](snapshot-copy-managed-disk.md) и (или) резервную копию перед шифрованием дисков. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. После создания резервной копии можно использовать [командлет Set-азвмдискенкриптионекстенсион](/powershell/module/az.compute/set-azvmdiskencryptionextension) для шифрования управляемых дисков, указав параметр-skipVmBackup. Дополнительные сведения см. в статье [Резервное копирование и восстановление зашифрованных виртуальных машин с помощью службы Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Если вы ранее использовали шифрование дисков Azure с Azure AD для шифрования виртуальной машины, необходимо продолжить использовать этот параметр для шифрования виртуальной машины. Дополнительные сведения см. [в статье шифрование дисков Azure с помощью Azure AD (предыдущий выпуск)](disk-encryption-overview-aad.md) . 
>
> - При шифровании томов Linux ОС виртуальная машина должна считаться недоступной. Настоятельно рекомендуется избегать входа по протоколу SSH во время шифрования, чтобы избежать проблем с блокированием открытых файлов, к которым необходимо получить доступ во время процесса шифрования. Чтобы проверить ход выполнения, используйте командлет PowerShell [Get-азвмдискенкриптионстатус](/powershell/module/az.compute/get-azvmdiskencryptionstatus) или команду CLI для [шифрования виртуальных машин](/cli/azure/vm/encryption#az-vm-encryption-show) . Этот процесс может занять несколько часов для тома операционной системы 30 ГБ, а также дополнительное время для шифрования томов данных. Если не используется параметр шифрования всех форматов, время шифрования тома данных будет соответствовать размеру и количеству томов данных. 
> - Отключение шифрования на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

## <a name="install-tools-and-connect-to-azure"></a>Установка средств и подключение к Azure

Шифрование дисков Azure можно включить и управлять с помощью [Azure CLI](/cli/azure) и [Azure PowerShell](/azure/new-azureps-module-az). Для этого необходимо установить средства локально и подключиться к подписке Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) — это интерфейс командной строки для управления ресурсами Azure. Этот интерфейс обеспечивает гибкие функции подачи запросов, выполнение длительных операций без блокировки и простое создание скриптов. Его можно установить локально, выполнив действия, описанные в разделе [установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Чтобы [войти в учетную запись Azure с помощью Azure CLI](/cli/azure/authenticate-azure-cli), используйте команду [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

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
[Модуль Azure PowerShell AZ](/azure/new-azureps-module-az) предоставляет набор командлетов, использующих модель [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) для управления ресурсами Azure. Его можно использовать в браузере с [Azure Cloud Shell](../../cloud-shell/overview.md)или установить на локальном компьютере, выполнив инструкции из статьи [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps). 

Если вы уже установили его локально, убедитесь, что вы используете последнюю версию версии пакета SDK для Azure PowerShell для настройки шифрования дисков Azure. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Чтобы [войти в учетную запись Azure с помощью Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), используйте командлет [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Если у вас несколько подписок и вы хотите указать одну из них, используйте командлет [Get-азсубскриптион](/powershell/module/Az.Accounts/Get-AzSubscription) , чтобы вывести их список, а затем командлет [Set-азконтекст](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

При выполнении командлета [Get-азконтекст](/powershell/module/Az.Accounts/Get-AzContext) будет проверена правильность выбранной подписки.

Чтобы убедиться, что командлеты шифрования дисков Azure установлены, используйте командлет [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Дополнительные сведения см. в статье [Приступая к работе с Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Включение шифрования на существующей или работающей виртуальной машине Linux
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. Сведения о схеме для расширения виртуальной машины см. в статье [Шифрование дисков Azure для Linux ](../extensions/azure-disk-enc-linux.md).

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно получить на портале или с помощью [Azure Backup](../../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии командлет Set-Азвмдискенкриптионекстенсион можно использовать для шифрования управляемых дисков, указав параметр-skipVmBackup. Команда Set-Азвмдискенкриптионекстенсион завершится сбоем для виртуальных машин на основе управляемых дисков, пока не будет создана резервная копия и не задан этот параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью Azure CLI 

Вы можете включить шифрование дисков на зашифрованном виртуальном жестком диске, установив и используя средство командной строки [Azure CLI](/cli/azure/?view=azure-cli-latest) . Его можно использовать в браузере с [Azure Cloud Shell](../../cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell. Чтобы включить шифрование на существующих или работающих виртуальных машинах Linux в Azure, используйте следующие команды интерфейса командной строки:

Чтобы включить шифрование на работающей виртуальной машине в Azure, используйте команду [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) .

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

- **Проверка того, что диски зашифрованы:** Чтобы проверить состояние шифрования виртуальной машины, используйте команду [AZ VM Encryption демонстрация](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Отключение шифрования:** Чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью PowerShell
Используйте командлет [Set-азвмдискенкриптионекстенсион](/powershell/module/az.compute/set-azvmdiskencryptionextension) , чтобы включить шифрование на работающей виртуальной машине в Azure. Создайте [моментальный снимок](snapshot-copy-managed-disk.md) и (или) создайте резервную копию виртуальной машины с [Azure Backup](../../backup/backup-azure-vms-encryption.md) перед шифрованием дисков. Параметр-skipVmBackup уже указан в сценариях PowerShell для шифрования работающей виртуальной машины Linux.

-  **Шифрование работающей виртуальной машины:** Приведенный ниже скрипт инициализирует переменные и запускает командлет Set-Азвмдискенкриптионекстенсион. Группа ресурсов, виртуальная машина и хранилище ключей были созданы в качестве необходимых компонентов. Замените Мивиртуалмачинересаурцеграуп, Мисекуревм и Мисекуреваулт своими значениями. Измените параметр-параметра volumetype значение, чтобы указать диски, для которых выполняется шифрование.

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
- **Шифрование работающей виртуальной машины с использованием KEK:** При шифровании дисков данных, а не диска операционной системы, может потребоваться добавить параметр -VolumeType. 

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
    
- **Проверка того, что диски зашифрованы:** Чтобы проверить состояние шифрования виртуальной машины, используйте командлет [Get-азвмдискенкриптионстатус](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Отключение шифрования дисков:** Чтобы отключить шифрование, используйте командлет [Disable-азвмдискенкриптион](/powershell/module/az.compute/disable-azvmdiskencryption) . Для виртуальных машин Linux отключение шифрования возможно только для томов данных.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью шаблона

Вы можете включить шифрование дисков на существующей или работающей виртуальной машине Linux в Azure с помощью [шаблона диспетчер ресурсов](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите кнопку **создать** , чтобы включить шифрование на существующей или работающей виртуальной машине.

В следующей таблице перечислены параметры шаблона Resource Manager для имеющихся или работающих виртуальных машин.

| Параметр | Описание |
| --- | --- |
| vmName | Имя виртуальной машины для выполнения операции шифрования. |
| keyVaultName | Имя хранилища ключей, в которое должен быть отправлен ключ шифрования. Его можно получить с помощью командлета `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` или команды Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`.|
| keyVaultResourceGroup | Имя группы ресурсов, содержащей хранилище ключей. |
|  keyEncryptionKeyURL | URL-адрес ключа шифрования ключа, который используется для шифрования ключа шифрования. Это необязательный параметр, если выбрать **nokek** из раскрывающегося списка UseExistingKek. Если из раскрывающегося списка UseExistingKek выбрано значение **kek**, то потребуется ввести значение _keyEncryptionKeyURL_. |
| volumeType | Тип тома, для которого будет выполняться шифрование. Допустимые значения: _OS_, _Data_ и _All_. 
| forceUpdateTag | Передает уникальное значение, такое как GUID, каждый раз, когда операция должна выполняться принудительно. |
| resizeOSDisk | Если размер раздела ОС будет изменен и займет весь виртуальный жесткий диск ОС до разделения системного тома. |
| расположение | Расположение для всех ресурсов. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Использование функции Енкриптформаталл для дисков данных на виртуальных машинах Linux

Параметр **EncryptFormatAll** сокращает время шифрования дисков данных Linux. Секции, удовлетворяющие определенным условиям, будут отформатированы (с текущей файловой системой), а затем снова подключены к месту выполнения команды. Если вы хотите исключить диск данных, который соответствует критериям, можно отключить его перед выполнением команды.

 После выполнения этой команды все подключенные ранее диски будут отформатированы, а уровень шифрования будет запущен поверх пустого диска. Если выбран этот параметр, временный диск с ресурсами, подключенный к виртуальной машине, также будет зашифрован. Если временный диск сбрасывается, при следующей возможности он будет переформатирован и повторно зашифрован для виртуальной машины с помощью шифрования дисков Azure. После шифрования диска ресурсов [агент Microsoft Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) не сможет управлять диском ресурсов и включать файл подкачки, но вы можете вручную настроить файл подкачки.

>[!WARNING]
> Параметр EncryptFormatAll не следует использовать, если в томах данных виртуальной машины есть требуемые данные. Вы можете исключить диски из шифрования, отключив их. Сначала следует попробовать параметр EncryptFormatAll на тестовой виртуальной машине, понять его функцию и последствия, а уже потом использовать на рабочих виртуальных машинах. Параметр EncryptFormatAll форматирует диск данных, и все данные на этом диске будут потеряны. Прежде чем продолжить, убедитесь, что диски, которые вы хотите исключить, отключены надлежащим образом. </br></br>
 >Если этот параметр устанавливается при обновлении параметров шифрования, это может привести к перезагрузке перед фактическим шифрованием. В этом случае необходимо также удалить из FSTAB-файла диск, который не нужно форматировать. Аналогично, перед началом операции шифрования нужно добавить в FSTAB-файл раздел, который нужно отформатировать и зашифровать. 

### <a name="encryptformatall-criteria"></a>Критерии Енкриптформаталл
Параметр проходит через все разделы и шифрует их, если они соответствуют **всем** из приведенных ниже критериев: 
- это не корневой или загрузочный раздел, а также не раздел операционной системы;
- этот раздел еще не зашифрован;
- это не том BEK;
- это не том RAID;
- это не том LVM;
- он подключен.

Выполняйте шифрование дисков, которые составляют том RAID или LVM, а не самого тома RAID или LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Используйте параметр Енкриптформаталл с Azure CLI
Чтобы включить шифрование на работающей виртуальной машине в Azure, используйте команду [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) .

-  **Шифрование работающей виртуальной машины с использованием параметра EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Использование параметра Енкриптформаталл с командлетом PowerShell
Используйте командлет [Set-азвмдискенкриптионекстенсион](/powershell/module/az.compute/set-azvmdiskencryptionextension) с параметром енкриптформаталл. 

**Шифрование работающей виртуальной машины с использованием параметра EncryptFormatAll:** Например, приведенный ниже скрипт инициализирует переменные и выполняет командлет Set-Азвмдискенкриптионекстенсион с параметром Енкриптформаталл. Группа ресурсов, виртуальная машина и хранилище ключей были созданы в качестве необходимых компонентов. Замените Мивиртуалмачинересаурцеграуп, Мисекуревм и Мисекуреваулт своими значениями.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Использование параметра Енкриптформаталл с диспетчером логических томов (LVM) 
Рекомендуем установить LVM-on-crypt. Для всех следующих примеров замените путь к устройству и точки подключения в соответствии с вашим вариантом использования. Эту настройку можно выполнить следующим образом.

- Добавить диски данных, которые составляют виртуальную машину.
- Форматировать, подключить и добавить эти диски в FSTAB-файл.

    1. Отформатируйте добавленный диск. Здесь мы используем символические ссылки, созданные Azure. Использование символических ссылок позволяет избежать проблем, связанных с изменениями имен устройств. Дополнительные сведения см. в статье [Устранение неполадок при изменении имени устройства виртуальной машины Linux](troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Подключите диски.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Добавьте сведения в FSTAB-файл.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Выполните командлет PowerShell Set-Азвмдискенкриптионекстенсион с параметром-Енкриптформаталл, чтобы зашифровать эти диски.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Настройте диспетчер логических томов поверх новых дисков. Обратите внимание, что зашифрованные диски разблокируются после завершения загрузки виртуальной машины. Таким образом, подключение диспетчера логических томов также придется отложить.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Новые виртуальные машины, созданные на основе зашифрованных клиентом виртуальных жестких дисков и ключей шифрования
В этом сценарии шифрование можно включить с помощью командлетов PowerShell или команд интерфейса командной строки. 

Используйте инструкции из раздела шифрование дисков Azure для подготовки предварительно зашифрованных образов, которые можно использовать в Azure. После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.

* [Подготовка предварительно зашифрованного виртуального жесткого диска Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии командлет Set-Азвмдискенкриптионекстенсион можно использовать для шифрования управляемых дисков, указав параметр-skipVmBackup. Команда Set-Азвмдискенкриптионекстенсион завершится сбоем для виртуальных машин на основе управляемых дисков, пока не будет создана резервная копия и не задан этот параметр. 
>
> Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Использование Azure PowerShell для шифрования виртуальных машин с предварительно зашифрованными виртуальными жесткими дисками 
Вы можете включить шифрование дисков на зашифрованном виртуальном жестком диске с помощью командлета PowerShell [Set-азвмосдиск](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). Приведенный ниже пример имеет некоторые общие параметры. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Включение шифрования на добавленном диске данных

Новый диск данных можно добавить с помощью команды [az vm disk attach](add-disk.md) или [через портал Azure](attach-disk-portal.md). Прежде чем запустить шифрование, необходимо подключить только что присоединенный диск данных. Нужно запросить шифрование диска данных, так как диск будет неработоспособным во время шифрования. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Включение шифрования на добавленном диске данных с помощью интерфейса командной строки Azure

 Если виртуальная машина была ранее зашифрована с помощью "ALL", параметр--Volume-Type должен оставаться равным "ALL". Значение "All" включает как диски ОС, так и диски данных. Если виртуальная машина была ранее зашифрована с типом тома "OS", параметр--Volume-Type следует изменить на "ALL", чтобы в него включалась операционная система и новый диск данных. Если виртуальная машина была зашифрована с типом тома "Data", то в дальнейшем он может оставаться со значением "Data", как это показано ниже. Добавления и присоединения нового диска данных к виртуальной машине недостаточно для подготовки к шифрованию. Вновь подключенный диск также должен быть отформатирован и правильно подключен к виртуальной машине перед включением шифрования. В Linux диск монтируется в /etc/fstab с [постоянным именем блочного устройства](troubleshoot-device-names-problems.md).  

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
 При использовании PowerShell для шифрования нового диска для Linux необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Приведенный ниже сценарий создает GUID для версии последовательности. Создайте [моментальный снимок](snapshot-copy-managed-disk.md) и (или) создайте резервную копию виртуальной машины с [Azure Backup](../../backup/backup-azure-vms-encryption.md) перед шифрованием дисков. Параметр-skipVmBackup уже указан в сценариях PowerShell для шифрования только что добавленного диска данных.
 

-  **Шифрование томов данных работающей виртуальной машины:** Приведенный ниже скрипт инициализирует переменные и запускает командлет Set-Азвмдискенкриптионекстенсион. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените Мивиртуалмачинересаурцеграуп, Мисекуревм и Мисекуреваулт своими значениями. Допустимые значения параметра -VolumeType: "All", "OS" и "Data". Если виртуальная машина была ранее зашифрована с типом тома "OS" или "ALL", параметр-параметра volumetype значение следует изменить на "ALL", чтобы были включены как ОС, так и новый диск данных.

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
- **Шифрование томов данных работающей виртуальной машины с использованием KEK:** Допустимые значения параметра -VolumeType: "All", "OS" и "Data". Если виртуальная машина была зашифрована с типом тома "OS" или "All", в дальнейшем параметр -VolumeType следует заменить на "All", чтобы были включены диск ОС и новый диск данных.

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
    > Синтаксис значения параметра Disk-Encryption-keyvault — это полная строка идентификатора:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[Квресаурце-Group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 


## <a name="disable-encryption-for-linux-vms"></a>Отключение шифрования для виртуальных машин Linux
Вы можете отключить шифрование с помощью Azure PowerShell, Azure CLI или шаблона Resource Manager. 

>[!IMPORTANT]
>Отключение шифрования, выполняемого с помощью службы шифрования Azure, на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

- **Отключение шифрования дисков с помощью Azure PowerShell:** Чтобы отключить шифрование, используйте командлет [Disable-азвмдискенкриптион](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Отключение шифрования дисков с помощью Azure CLI:** чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Отключение шифрования с помощью шаблона Resource Manager:** используйте шаблон [отключения шифрования на работающей виртуальной машине Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) для отключения шифрования.
     1. Нажмите кнопку **Развернуть в Azure**.
     2. Выберите подписку, группу ресурсов, расположение, виртуальную машину, условия использования и соглашение.

## <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

Шифрование дисков Azure не работает в следующих сценариях, функциях и технологиях Linux:

- Шифрование ВИРТУАЛЬНЫХ машин базового уровня или виртуальных машин, созданных с помощью классического метода создания виртуальной машины.
- Отключение шифрования диска операционной системы или диска данных виртуальной машины Linux при шифровании диска ОС.
- Шифрование диска ОС для масштабируемых наборов виртуальных машин Linux.
- Шифрование пользовательских образов на виртуальных машинах Linux.
- Интеграция с локальной системой управления ключами.
- служба файлов Azure (общая файловая система);
- сетевая файловая система (NFS);
- динамические тома;
- Временные диски ОС.
- Шифрование общих и распределенных файловых систем, таких как (но не ограниченных): DFS, GFS, ДРДБ и Цеффс.

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о шифровании дисков Azure](disk-encryption-overview.md)
- [Примеры сценариев шифрования дисков Azure](disk-encryption-sample-scripts.md)
- [Устранение неполадок шифрования дисков Azure](disk-encryption-troubleshooting.md)
