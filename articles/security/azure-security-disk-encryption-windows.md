---
title: Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Windows
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин IaaS под управлением Windows.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/17/2018
ms.custom: seodec18
ms.openlocfilehash: 0051c7ca66d30730e6fc25b8b9d3edec91c43f07
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548652"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Windows

Возможно несколько сценариев включения шифрования дисков, и последовательность действий для каждого может отличаться. В следующих разделах сценарии для виртуальных машин IaaS под управлением Windows описаны более подробно. Чтобы использовать шифрование дисков Azure, нужно выполнить [эти предварительные требования](../security/azure-security-disk-encryption-prerequisites.md). 

Создайте [моментальный снимок](../virtual-machines/windows/snapshot-copy-managed-disk.md) и (или) резервную копию перед шифрованием дисков. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension с параметром -skipVmBackup. Дополнительные сведения см. в статье [Резервное копирование и восстановление зашифрованных виртуальных машин с помощью службы Azure Backup](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Если вы уже использовали [шифрование дисков Azure с помощью приложения Azure AD](azure-security-disk-encryption-prerequisites-aad.md) для шифрования этой виртуальной машины, этот способ нужно применять и далее для шифрования виртуальной машины. На этой зашифрованной виртуальной машине нельзя использовать [шифрование дисков Azure](azure-security-disk-encryption-prerequisites.md), так как этот сценарий не работает — переключение из приложения AAD для данной зашифрованной виртуальной машины сейчас не поддерживается. 
> - Для шифрования дисков Azure необходимо, чтобы Key Vault и виртуальные машины были расположены в одном регионе. Создайте и используйте хранилище ключей, которое находится в том же регионе, что и виртуальная машина, которую нужно зашифровать. 


## <a name="bkmk_RunningWinVM"></a> Включение шифрования на виртуальной машине IaaS Windows, которая уже существует или работает
В этом сценарии шифрование можно включить с помощью шаблона, командлетов PowerShell или команд интерфейса командной строки. В следующих разделах более подробно описано, как включить шифрование дисков Azure. Сведения о схеме для расширения виртуальной машины см. в статье [Шифрование дисков Azure для Windows ](../virtual-machines/extensions/azure-disk-enc-windows.md).

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension, указав параметр -skipVmBackup. Команда Set-AzureRmVMDiskEncryptionExtension будет завершаться ошибкой при использовании с виртуальными машинами на основе управляемых дисков, пока не будет сделана резервная копия и указан определенный параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Включение шифрования на существующих или работающих виртуальных машинах с помощью Azure PowerShell 
Используйте командлет [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure. 

-  **Шифрование работающей виртуальной машины:** приведенный ниже сценарий позволяет инициализировать переменные и запустить командлет Set-AzureRmVMDiskEncryptionExtension. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените MySecureRg, MySecureVM и MySecureVault своими значениями.

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Шифрование работающей виртуальной машины с использованием KEK:** 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Проверка того, что диски зашифрованы:** чтобы проверить состояние шифрования виртуальной машины IaaS, используйте командлет [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Отключение шифрования дисков:** для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Отключение шифрования диска данных на виртуальной машине Windows в случае, когда зашифрованы оба типа дисков (диск ОС и диск данных), не сработает. В этом случае отключите шифрование на всех дисках.

     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Включение шифрования на существующих или работающих виртуальных машинах с помощью Azure CLI
Используйте команду [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure.

-  **Шифрование работающей виртуальной машины:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Шифрование работающей виртуальной машины с использованием KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей]. </br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Проверка того, что диски зашифрованы:** чтобы проверить состояние шифрования виртуальной машины IaaS, используйте команду [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Отключение шифрования:** чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Отключение шифрования диска данных на виртуальной машине Windows в случае, когда зашифрованы оба типа дисков (диск ОС и диск данных), не сработает. В этом случае отключите шифрование на всех дисках.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension, указав параметр -skipVmBackup. Эта команда будет завершаться ошибкой при использовании с виртуальными машинами на основе управляемых дисков, пока не будет сделана резервная копия и указан определенный параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 

### <a name="bkmk_RunningWinVMwRM"> </a>Использование шаблона Resource Manager
Включить шифрование дисков на существующих или работающих виртуальных машинах IaaS под управлением Windows в Azure можно с помощью [этого шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение, параметры, условия использования и соглашение. Нажмите кнопку **Покупка**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.

В следующей таблице перечислены параметры шаблона Resource Manager для имеющихся или работающих виртуальных машин.

| Параметр | ОПИСАНИЕ |
| --- | --- |
| vmName | Имя виртуальной машины для выполнения операции шифрования. |
| keyVaultName | Имя хранилища ключей, в которое будет передан ключ BitLocker. Его можно получить с помощью командлета `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` или команды Azure CLI az keyvault list --resource-group "MySecureGroup" |ConvertFrom-JSON`|
| keyVaultResourceGroup | Имя группы ресурсов, содержащей хранилище ключей.|
|  keyEncryptionKeyURL | URL-адрес ключа шифрования ключей, который используется для шифрования созданного ключа BitLocker. Это необязательный параметр, если выбрать **nokek** из раскрывающегося списка UseExistingKek. Если из раскрывающегося списка UseExistingKek выбрано значение **kek**, то потребуется ввести значение _keyEncryptionKeyURL_. |
| volumeType | Тип тома, для которого будет выполняться шифрование. Допустимые значения: _OS_, _Data_ и _All_. 
| forceUpdateTag | Передает уникальное значение, такое как GUID, каждый раз, когда операция должна выполняться принудительно. |
| resizeOSDisk | Если размер раздела ОС будет изменен и займет весь виртуальный жесткий диск ОС до разделения системного тома. |
| location | Расположение для всех ресурсов. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Шифрование масштабируемых наборов виртуальных машин

[Масштабируемые наборы виртуальных машин Azure](../virtual-machine-scale-sets/overview.md) позволяют создавать и администрировать группы идентичных виртуальных машин с балансировкой нагрузки. Число экземпляров виртуальных машин может автоматически увеличиваться или уменьшаться в зависимости от спроса или по определенному расписанию. Используйте CLI или Azure PowerShell для шифрования масштабируемых наборов виртуальных машин.


### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Регистрация для получения предварительной версии службы шифрования дисков с помощью Azure Powershell

Для работы предварительной версии службы шифрования дисков Azure для масштабируемых наборов виртуальных машин необходимо самостоятельно зарегистрировать подписку с помощью команды [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). Указанные ниже действия нужно выполнить только при первом использовании предварительной версии функции шифрования дисков.

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Распространение запроса на регистрацию может занять 10 минут. Состояние регистрации можно проверить с помощью команды [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Когда `RegistrationState` будет иметь значение *Registered*, повторно зарегистрируйте поставщик *Miсrosoft.Compute* с помощью команды [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Шифрование масштабируемых наборов виртуальных машин с помощью Azure PowerShell

Используйте командлет [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension), чтобы включить шифрование в масштабируемом наборе виртуальных машин Windows. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов.

-  **Шифрование работающего масштабируемого набора виртуальных машин**:
    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **Получение состояния шифрования для масштабируемого набора виртуальных машин:** используйте командлет [Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption).
    
    ```azurepowershell-interactive
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **Отключение шифрования в масштабируемом наборе виртуальных машин**: используйте командлет [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption). 

    ```azurepowershell-interactive
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Регистрация для получения предварительной версии службы шифрования дисков с помощью Azure CLI

Для работы предварительной версии службы шифрования дисков Azure для масштабируемых наборов виртуальных машин необходимо самостоятельно зарегистрировать подписку с помощью команды [az feature register](/cli/azure/feature#az-feature-register). Указанные ниже действия нужно выполнить только при первом использовании предварительной версии функции шифрования дисков.

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Распространение запроса на регистрацию может занять 10 минут. Состояние регистрации можно проверить с помощью команды [az feature show](/cli/azure/feature#az-feature-show). Когда `State` будет иметь значение *Registered*, повторно зарегистрируйте поставщик *Microsoft.Compute* с помощью команды [az provider register](/cli/azure/provider#az-provider-register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```



###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Шифрование масштабируемых наборов виртуальных машин с помощью Azure CLI

Используйте [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable), чтобы включить шифрование в масштабируемом наборе виртуальных машин Windows. Если для масштабируемого набора задана политика обновления вручную, запустите шифрование с помощью команды [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов.

-  **Шифрование работающего масштабируемого набора виртуальных машин**:
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Шифрование работающего масштабируемого набора виртуальных машин с использованием ключа шифрования ключей для упаковки ключа**:
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **Получение состояния шифрования для масштабируемого набора виртуальных машин:** используйте команду [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show).

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **Отключение шифрования в масштабируемом наборе виртуальных машин**: используйте команду [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable).
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Шаблоны Azure Resource Manager для масштабируемых наборов виртуальных машин Windows

Для шифрования или расшифровки масштабируемых наборов виртуальных машин Windows используйте шаблоны Azure Resource Manager и приведенные ниже инструкции:

- [Включение шифрования в масштабируемом наборе виртуальных машин Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Развертывание масштабируемого набора виртуальных машин Windows с помощью Jumpbox и включение шифрования в этом наборе Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)
- [Отключение шифрования в масштабируемом наборе виртуальных машин Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Нажмите кнопку **Развернуть в Azure**.
     2. Заполните обязательные поля, а затем согласитесь с условиями.
     3. Щелкните **Приобрести**, чтобы развернуть шаблон.

## <a name="bkmk_VHDpre"> </a> Новые виртуальные машины IaaS, при создании которых используются зашифрованные виртуальные жесткие диски и ключи шифрования

В этом сценарии шифрование можно включить с помощью командлетов PowerShell или команд интерфейса командной строки. 

Следуйте инструкциям, приведенным в приложении к этой статье, чтобы подготовить предварительно зашифрованные образы, которые можно использовать в Azure. После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.

* [Подготовка предварительно зашифрованного виртуального жесткого диска Windows](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Подготовка предварительно зашифрованного виртуального жесткого диска Linux](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension, указав параметр -skipVmBackup. Команда Set-AzureRmVMDiskEncryptionExtension будет завершаться ошибкой при использовании с виртуальными машинами на основе управляемых дисков, пока не будет сделана резервная копия и указан определенный параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 


### <a name="bkmk_VHDprePSH"> </a> Шифрование виртуальных машин с предварительно зашифрованными виртуальными жесткими дисками с помощью Azure PowerShell
Можно включить шифрование дисков для зашифрованного виртуального жесткого диска с помощью командлета PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). Приведенный ниже пример имеет некоторые общие параметры. 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Включение шифрования на добавленном диске данных
[Добавить новый диск на виртуальную машину Windows можно с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md) или [портала Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Включение шифрования на добавленном диске данных с помощью Azure PowerShell
 При использовании Powershell для шифрования нового диска для виртуальных машин Windows необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Приведенный ниже сценарий создает GUID для версии последовательности. В некоторых случаях новый добавленный диск данных может быть зашифрован автоматически с помощью расширения шифрования дисков Azure. Автоматическое шифрование обычно происходит, когда виртуальная машина перезапускается после включения нового диска. Обычно это происходит, так как для типа тома указано значение "Все", если ранее на виртуальной машине использовалось шифрование дисков. Если выполняется шифрование на новом диске данных, мы рекомендуем снова выполнить командлет Set-AzureRmVmDiskEncryptionExtension с новой версией последовательности. Если для нового диска данных включено автоматическое шифрование и вы хотите отказаться от шифрования, сначала расшифруйте все диски, а затем повторите шифрование с новой версией последовательности, указав соответствующую операционную систему для типа тома. 
  
 

-  **Шифрование работающей виртуальной машины:** приведенный ниже сценарий позволяет инициализировать переменные и запустить командлет Set-AzureRmVMDiskEncryptionExtension. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените MySecureRg, MySecureVM и MySecureVault своими значениями. В этом примере в качестве значения параметра VolumeType используется значение "All", что включает тома операционной системы и тома данных. Если требуется шифрование только томов операционной системы, используйте "OS" в качестве значения параметра VolumeType. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Шифрование работающей виртуальной машины с использованием KEK:** В этом примере в качестве значения параметра VolumeType используется значение "All", что включает тома операционной системы и тома данных. Если требуется шифрование только томов операционной системы, используйте "OS" в качестве значения параметра VolumeType.

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Включение шифрования на добавленном диске данных с помощью интерфейса командной строки Azure
 Команда Azure CLI автоматически предоставит вам новую версию последовательности, когда вы запустите команду для включения шифрования. В этом примере используется "All" для параметра volume-type. Может потребоваться изменить значение параметра volume-type на"OS", если необходимо выполнить шифрование только диска операционной системы. В отличие от синтаксиса Powershell, интерфейс командной строки (CLI) не требует предоставления уникальной версии последовательности при включении шифрования. CLI автоматически создает и использует свое собственное уникальное значение версии последовательности.   

-  **Шифрование работающей виртуальной машины:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Шифрование работающей виртуальной машины с использованием KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Отключение шифрования
Вы можете отключить шифрование с помощью Azure PowerShell, Azure CLI или шаблона Resource Manager. Отключение шифрования диска данных на виртуальной машине Windows в случае, когда зашифрованы оба типа дисков (диск ОС и диск данных), не сработает. В этом случае отключите шифрование на всех дисках.

- **Отключение шифрования дисков с помощью Azure PowerShell:** для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Отключение шифрования дисков с помощью Azure CLI:** чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type "all"
     ```
- **Отключение шифрования с помощью шаблона Resource Manager:** 

    1. Нажмите кнопку **Deploy to Azure** (Развернуть в Azure) на странице шаблона [отключения шифрования дисков на виртуальной машине под управлением Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Выберите подписку, группу ресурсов, расположение, тип тома, виртуальную машину, условия использования и соглашение.
    3.  Нажмите кнопку **покупка**, чтобы отключить шифрование дисков на работающей виртуальной машине Windows. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Linux](azure-security-disk-encryption-linux.md)
