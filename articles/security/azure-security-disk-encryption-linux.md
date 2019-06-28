---
title: Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Linux
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин IaaS под управлением Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 04/05/2019
ms.custom: seodec18
ms.openlocfilehash: ffdda51c4739dfad6c326a5c90f4a93ebfb321cd
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294901"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Linux 

Возможно несколько сценариев включения шифрования дисков, и последовательность действий для каждого может отличаться. В следующих разделах сценарии для виртуальных машин IaaS под управлением Linux описаны более подробно. Перед тем как использовать шифрование дисков [предварительных требованиях шифрования дисков Azure](azure-security-disk-encryption-prerequisites.md) должен быть завершен и [Дополнительные требования для виртуальных машин IaaS Linux](azure-security-disk-encryption-prerequisites.md#additional-prerequisites-for-linux-iaas-vms) разделе, необходимо проверить.

Создайте [моментальный снимок](../virtual-machines/windows/snapshot-copy-managed-disk.md) и (или) резервную копию перед шифрованием дисков. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. Как только будет установлено резервной копии, можно использовать командлет Set-AzVMDiskEncryptionExtension зашифровать управляемые диски, указав параметр - skipVmBackup. Дополнительные сведения см. в статье [Резервное копирование и восстановление зашифрованных виртуальных машин с помощью службы Azure Backup](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Если вы уже использовали [шифрование дисков Azure с помощью приложения Azure AD](azure-security-disk-encryption-prerequisites-aad.md) для шифрования этой виртуальной машины, этот способ нужно применять и далее для шифрования виртуальной машины. На этой зашифрованной виртуальной машине нельзя использовать [шифрование дисков Azure](azure-security-disk-encryption-prerequisites.md), так как этот сценарий не работает — переключение из приложения AAD для данной зашифрованной виртуальной машины сейчас не поддерживается.
 > - Для шифрования дисков Azure необходимо, чтобы Key Vault и виртуальные машины были расположены в одном регионе. Создайте и используйте хранилище ключей, которое находится в том же регионе, что и виртуальная машина, которую нужно зашифровать.
> - При шифровании тома операционной системы Linux, виртуальной Машины должен считаться недоступной. Настоятельно рекомендуется во избежание входа по протоколу SSH, чтобы избежать проблемы, препятствующие все открытые файлы, которые должны осуществляться во время шифрования во время шифрования. Чтобы проверить ход выполнения, [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) или [show шифрования виртуальной машины](/cli/azure/vm/encryption#az-vm-encryption-show) можно использовать команды. Этот процесс может занять несколько часов для тома операционной системы 30 ГБ, а также дополнительное время для шифрования томов данных. Если не используется параметр шифрования всех форматов, время шифрования тома данных будет соответствовать размеру и количеству томов данных. 
> - Отключение шифрования на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Включение шифрования на виртуальной машине IaaS под управлением Linux, которая уже существует или работает
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. Сведения о схеме для расширения виртуальной машины см. в статье [Шифрование дисков Azure для Linux ](../virtual-machines/extensions/azure-disk-enc-linux.md).

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. Как только будет установлено резервной копии, можно использовать командлет Set-AzVMDiskEncryptionExtension зашифровать управляемые диски, указав параметр - skipVmBackup. Команда Set-AzVMDiskEncryptionExtension завершится ошибкой для виртуальных машин на основе управляемых дисков, пока не сделана резервная копия, и этот параметр был указан. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью Azure CLI 
Можно включить шифрование дисков для зашифрованного виртуального жесткого диска, установив программу командной строки [Azure CLI 2.0](/cli/azure). Его можно использовать в браузере с [Azure Cloud Shell](../cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell. Чтобы включить шифрование на виртуальных машинах IaaS под управлением Linux, которые уже существуют или работают в Azure, используйте следующие команды CLI.

Используйте команду [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure.

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

- **Проверка того, что диски зашифрованы:** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте [show шифрования виртуальной машины az](/cli/azure/vm/encryption#az-vm-encryption-show) команды. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Отключение шифрования:** Чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью PowerShell
Используйте [AzVMDiskEncryptionExtension набора](/powershell/module/az.compute/set-azvmdiskencryptionextension) командлет, чтобы включить шифрование на работающей виртуальной машины IaaS в Azure. Воспользоваться [моментального снимка](../virtual-machines/windows/snapshot-copy-managed-disk.md) и/или резервное копирование виртуальной Машины с [Azure Backup](../backup/backup-azure-vms-encryption.md) прежде, чем диски шифруются. В скриптах PowerShell для шифрования на работающей виртуальной Машине Linux уже указан параметр - skipVmBackup.

-  **Шифрование работающей виртуальной машины:** Приведенный ниже сценарий инициализирует переменные и запускает командлет Set-AzVMDiskEncryptionExtension. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените значения MyVirtualMachineResourceGroup MySecureVM и MySecureVault. Измените параметр - VolumeType, чтобы указать, какие диски, в которой выполняется шифрование.

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
    
- **Проверка того, что диски зашифрованы:** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте [Get AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) командлета. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Отключение шифрования дисков:** Чтобы отключить шифрование, используйте [Disable AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) командлета. Для виртуальных машин Linux отключение шифрования возможно только для томов данных.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Включение шифрования на существующей или работающей виртуальной машине с помощью шаблона

Включить шифрование дисков на существующих или работающих виртуальных машинах IaaS под управлением Linux в Azure можно с помощью [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите кнопку **Создать**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.

В следующей таблице перечислены параметры шаблона Resource Manager для имеющихся или работающих виртуальных машин.

| Параметр | Описание |
| --- | --- |
| vmName | Имя виртуальной машины для выполнения операции шифрования. |
| keyVaultName | Имя хранилища ключей, в которое будет передан ключ BitLocker. Его можно получить с помощью командлета `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` или команды Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`.|
| keyVaultResourceGroup | Имя группы ресурсов, содержащей хранилище ключей.|
|  keyEncryptionKeyURL | URL-адрес ключа шифрования ключей, который используется для шифрования созданного ключа BitLocker. Это необязательный параметр, если выбрать **nokek** из раскрывающегося списка UseExistingKek. Если из раскрывающегося списка UseExistingKek выбрано значение **kek**, то потребуется ввести значение _keyEncryptionKeyURL_. |
| volumeType | Тип тома, для которого будет выполняться шифрование. Допустимые значения: _OS_, _Data_ и _All_. 
| forceUpdateTag | Передает уникальное значение, такое как GUID, каждый раз, когда операция должна выполняться принудительно. |
| resizeOSDisk | Если размер раздела ОС будет изменен и займет весь виртуальный жесткий диск ОС до разделения системного тома. |
| location | Расположение для всех ресурсов. |



## <a name="encrypt-virtual-machine-scale-sets"></a>Шифрование масштабируемых наборов виртуальных машин
[Масштабируемые наборы виртуальных машин Azure](../virtual-machine-scale-sets/overview.md) позволяют создавать и администрировать группы идентичных виртуальных машин с балансировкой нагрузки. Число экземпляров виртуальных машин может автоматически увеличиваться или уменьшаться в зависимости от спроса или по определенному расписанию. Используйте CLI или Azure PowerShell для шифрования масштабируемых наборов виртуальных машин. На виртуальных машинах Linux масштабируемых наборов поддерживается только для шифрования дисков данных.

Пример пакетного файла для шифрования дисков данных масштабируемого набора Linux можно найти на сайте [Github](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). Этот пример создает группу ресурсов и масштабируемый набор Linux, подключает диск данных размером 5 ГБ и шифрует этот масштабируемый набор виртуальных машин.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Шифрование масштабируемых наборов виртуальных машин с помощью Azure CLI

Используйте [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable), чтобы включить шифрование в масштабируемом наборе виртуальных машин Windows. Если для масштабируемого набора задана политика обновления вручную, запустите шифрование с помощью команды [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. 

-  **Шифрование работающего масштабируемого набора виртуальных машин**:
    ```azurecli-interactive
    az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault"
    ```

-  **Шифрование работающего масштабируемого набора виртуальных машин с использованием ключа шифрования ключей для упаковки ключа**:
     ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault"
     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Получение состояния шифрования для масштабируемого набора виртуальных машин:** используйте команду [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show).

    ```azurecli-interactive
    az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Отключение шифрования в масштабируемом наборе виртуальных машин**: используйте команду [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable).
    ```azurecli-interactive
    az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Шифрование масштабируемых наборов виртуальных машин с помощью Azure PowerShell

Используйте [AzVmssDiskEncryptionExtension набора](/powershell/module/az.compute/set-azvmssdiskencryptionextension) командлет, чтобы включить шифрование на масштабируемый набор виртуальных машин Windows. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов.

-  **Шифрование работающего масштабируемого набора виртуальных машин**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
      ```

-  **Шифрование выполняемого масштабируемого набора виртуальных машин с использованием ключа шифрования ключей для упаковки ключа**.
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $keyEncryptionKeyName = "MyKeyEncryptionKey";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl  -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
      ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Получение состояния шифрования для набора виртуальных машин:** Используйте [Get AzVmssVMDiskEncryption](/powershell/module/az.compute/get-azvmssvmdiskencryption) командлета.
    
    ```powershell
    Get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Отключение шифрования в масштабируемом наборе виртуальных машин**: Используйте [Disable AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) командлета. 

    ```powershell
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Шаблоны Azure Resource Manager для масштабируемых наборов виртуальных машин Linux

Для шифрования или расшифровки масштабируемых наборов виртуальных машин Linux используйте шаблоны Azure Resource Manager и приведенные ниже инструкции:

- [Включите шифрование в масштабируемом наборе виртуальных машин Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux).
- [Отключение шифрования на масштабируемый набор виртуальных машин Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. Нажмите кнопку **Развернуть в Azure**.
     2. Заполните обязательные поля, а затем согласитесь с условиями.
     3. Щелкните **Приобрести**, чтобы развернуть шаблон.

## <a name="bkmk_EFA"> </a>Использование функции EncryptFormatAll для дисков данных на виртуальных машинах IaaS под управлением Linux

Параметр **EncryptFormatAll** сокращает время шифрования дисков данных Linux. Разделы, отвечающие определенным критериям, будут отформатированы (с текущей файловой системой). Затем они будут обратно подключены там, где они были подключены до выполнения команды. Если вы хотите исключить диск данных, который соответствует критериям, можно отключить его перед выполнением команды.

 После выполнения этой команды все диски, которые были подключены ранее, будут отформатированы. Уровень шифрования запускается поверх уже теперь пустого диска. Если выбран этот параметр, временный диск с ресурсами, подключенный к виртуальной машине, также будет зашифрован. Если временный диск сбрасывается, при следующей возможности он будет переформатирован и повторно зашифрован для виртуальной машины с помощью шифрования дисков Azure.

>[!WARNING]
> Параметр EncryptFormatAll не следует использовать, если в томах данных виртуальной машины есть требуемые данные. Вы можете исключить диски из шифрования, отключив их. Сначала следует попробовать параметр EncryptFormatAll на тестовой виртуальной машине, понять его функцию и последствия, а уже потом использовать на рабочих виртуальных машинах. Параметр EncryptFormatAll форматирует диск данных, и все данные на этом диске будут потеряны. Прежде чем продолжить, убедитесь, что диски, которые вы хотите исключить, отключены надлежащим образом. </br></br>
 >Если этот параметр устанавливается при обновлении параметров шифрования, это может привести к перезагрузке перед фактическим шифрованием. В этом случае необходимо также удалить из FSTAB-файла диск, который не нужно форматировать. Аналогично, перед началом операции шифрования нужно добавить в FSTAB-файл раздел, который нужно отформатировать и зашифровать. 

### <a name="bkmk_EFACriteria"> </a> Критерии EncryptFormatAll
Параметр проходит через все разделы и шифрует их, если они соответствуют **всем** из приведенных ниже критериев: 
- это не корневой или загрузочный раздел, а также не раздел операционной системы;
- этот раздел еще не зашифрован;
- это не том BEK;
- это не том RAID;
- это не том LVM;
- он подключен.

Выполняйте шифрование дисков, которые составляют том RAID или LVM, а не самого тома RAID или LVM.

### <a name="bkmk_EFAPSH"> </a> Использование параметра EncryptFormatAll с Azure CLI
Используйте команду [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure.

-  **Шифрование работающей виртуальной машины с использованием параметра EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Использование параметра EncryptFormatAll с командлетом PowerShell
Используйте [AzVMDiskEncryptionExtension набора](/powershell/module/az.compute/set-azvmdiskencryptionextension) командлет с параметром EncryptFormatAll. 

**Шифрование работающей виртуальной машины с использованием параметра EncryptFormatAll:** Например приведенный ниже сценарий инициализирует переменные и запускает командлет Set-AzVMDiskEncryptionExtension с параметром EncryptFormatAll. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените значения MyVirtualMachineResourceGroup MySecureVM и MySecureVault.
  
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


### <a name="bkmk_EFALVM"> </a> Использование параметра EncryptFormatAll с диспетчером логических томов (LVM) 
Рекомендуем установить LVM-on-crypt. Для всех следующих примеров замените путь к устройству и точки подключения в соответствии с вашим вариантом использования. Эту настройку можно выполнить следующим образом.

- Добавить диски данных, которые составляют виртуальную машину.
- Форматировать, подключить и добавить эти диски в FSTAB-файл.

    1. Отформатируйте добавленный диск. Здесь мы используем символические ссылки, созданные Azure. Использование символических ссылок позволяет избежать проблем, связанных с изменениями имен устройств. Дополнительные сведения см. в статье [Устранение неполадок при изменении имени устройства виртуальной машины Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Подключите диски.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Добавьте сведения в FSTAB-файл.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Выполните командлет PowerShell Set-AzVMDiskEncryptionExtension с - EncryptFormatAll для шифрования этих дисков.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Настройте диспетчер логических томов поверх новых дисков. Обратите внимание, что зашифрованные диски разблокируются после завершения загрузки виртуальной машины. Таким образом, подключение диспетчера логических томов также придется отложить.


## <a name="bkmk_VHDpre"> </a> Новые виртуальные машины IaaS, при создании которых используются зашифрованные виртуальные жесткие диски и ключи шифрования
В этом сценарии шифрование можно включить с помощью командлетов PowerShell или команд интерфейса командной строки. 

Следуйте инструкциям, приведенным в приложении к этой статье, чтобы подготовить предварительно зашифрованные образы, которые можно использовать в Azure. После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.

* [Подготовка предварительно зашифрованного виртуального жесткого диска Windows](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Подготовка предварительно зашифрованного виртуального жесткого диска Linux](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. Как только будет установлено резервной копии, можно использовать командлет Set-AzVMDiskEncryptionExtension зашифровать управляемые диски, указав параметр - skipVmBackup. Команда Set-AzVMDiskEncryptionExtension завершится ошибкой для виртуальных машин на основе управляемых дисков, пока не сделана резервная копия, и этот параметр был указан. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 



### <a name="bkmk_VHDprePSH"> </a> Использование Azure PowerShell для шифрования виртуальных машин IaaS с предварительно зашифрованными виртуальными жесткими дисками 
Можно включить шифрование дисков для зашифрованного виртуального жесткого диска с помощью командлета PowerShell [AzVMOSDisk набора](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). Приведенный ниже пример имеет некоторые общие параметры. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Включение шифрования на добавленном диске данных

Новый диск данных можно добавить с помощью команды [az vm disk attach](../virtual-machines/linux/add-disk.md) или [через портал Azure](../virtual-machines/linux/attach-disk-portal.md). Прежде чем запустить шифрование, необходимо подключить только что присоединенный диск данных. Нужно запросить шифрование диска данных, так как диск будет неработоспособным во время шифрования. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Включение шифрования на добавленном диске данных с помощью интерфейса командной строки Azure

 Если виртуальная машина ранее была зашифрована со значением "All", то в дальнейшем значение параметра volume-type должно оставаться "All". Значение "All" включает как диски ОС, так и диски данных. Если виртуальная машина была зашифрована с типом тома "OS", в дальнейшем параметр --volume-type следует заменить на "All", чтобы были включены диск ОС и новый диск данных. Если виртуальная машина была зашифрована с типом тома "Data", то в дальнейшем он может оставаться со значением "Data", как это показано ниже. Добавления и присоединения нового диска данных к виртуальной машине недостаточно для подготовки к шифрованию. Вновь подключенный диск также должен быть отформатирован и правильно подключен к виртуальной машине перед включением шифрования. В Linux диск монтируется в /etc/fstab с [постоянным именем блочного устройства](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

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
 При использовании PowerShell для шифрования нового диска для Linux необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Приведенный ниже сценарий создает GUID для версии последовательности. Воспользоваться [моментального снимка](../virtual-machines/windows/snapshot-copy-managed-disk.md) и/или резервное копирование виртуальной Машины с [Azure Backup](../backup/backup-azure-vms-encryption.md) прежде, чем диски шифруются. В скриптах PowerShell для шифрования диска только что добавленные данные уже указан параметр - skipVmBackup.
 

-  **Шифрование томов данных работающей виртуальной машины:** Приведенный ниже сценарий инициализирует переменные и запускает командлет Set-AzVMDiskEncryptionExtension. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените значения MyVirtualMachineResourceGroup MySecureVM и MySecureVault. Допустимые значения параметра -VolumeType: "All", "OS" и "Data". Если виртуальная машина была зашифрована с типом тома "OS" или "All", в дальнейшем параметр -VolumeType следует заменить на "All", чтобы были включены диск ОС и новый диск данных.

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
    > Синтаксис для значения параметра дискового шифрования хранилище ключей — это полный идентификатор строка: / subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 


## <a name="disable-encryption-for-linux-vms"></a>Отключение шифрования для виртуальных машин Linux
Вы можете отключить шифрование с помощью Azure PowerShell, Azure CLI или шаблона Resource Manager. 

>[!IMPORTANT]
>Отключение шифрования, выполняемого с помощью службы шифрования Azure, на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

- **Отключение шифрования дисков с помощью Azure PowerShell:** Чтобы отключить шифрование, используйте [Disable AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) командлета. 
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
     3.  Нажмите кнопку **покупка**, чтобы отключить шифрование дисков на работающей виртуальной машине Windows. 


## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Включение шифрования дисков Azure для виртуальных машин Windows IaaS](azure-security-disk-encryption-windows.md)
