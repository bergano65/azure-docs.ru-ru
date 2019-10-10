---
title: Сценарии шифрования дисков Azure на виртуальных машинах Windows
description: В этой статье приведены инструкции по включению Microsoft Azure шифрования дисков для виртуальных машин Windows в различных сценариях.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 948712b684d1cd1b072862b7253d745f89b0cc56
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244997"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Сценарии шифрования дисков Azure на виртуальных машинах Windows

Шифрование дисков Azure использует средство защиты внешнего ключа BitLocker для обеспечения шифрования томов и дисков данных виртуальных машин Azure, а также интегрировано с Azure Key Vault, помогающее управлять ключами и секретами шифрования диска и управлять ими. Общие сведения о службе см. в статье [Шифрование дисков Azure для виртуальных машин Windows](disk-encryption-overview.md).

Существует много сценариев шифрования дисков, и эти действия могут отличаться в зависимости от сценария. В следующих разделах более подробно рассматриваются сценарии для виртуальных машин Windows.

Шифрование дисков можно применить только к виртуальным машинам [поддерживаемых размеров виртуальных машин и операционных систем](disk-encryption-overview.md#supported-vms-and-operating-systems). Необходимо также выполнить следующие предварительные требования.

- [Требования к сети](disk-encryption-overview.md#networking-requirements)
- [Требования к групповая политика](disk-encryption-overview.md#group-policy-requirements)
- [Требования к хранилищу ключей шифрования](disk-encryption-overview.md#encryption-key-storage-requirements)



>[!IMPORTANT]
> - Если вы ранее использовали шифрование дисков Azure с Azure AD для шифрования виртуальной машины, необходимо продолжить использовать этот параметр для шифрования виртуальной машины. Дополнительные сведения см. [в статье шифрование дисков Azure с помощью Azure AD (предыдущий выпуск)](disk-encryption-overview-aad.md) . 
>
> - Перед шифрованием дисков следует создать [моментальный снимок](snapshot-copy-managed-disk.md) и (или) выполнить резервное копирование. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. После создания резервной копии можно использовать [командлет Set-азвмдискенкриптионекстенсион](/powershell/module/az.compute/set-azvmdiskencryptionextension) для шифрования управляемых дисков, указав параметр-skipVmBackup. Дополнительные сведения о резервном копировании и восстановлении зашифрованных виртуальных машин см. в статье [резервное копирование и восстановление зашифрованной виртуальной машины Azure](../../backup/backup-azure-vms-encryption.md). 
>
> - Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины.


## <a name="install-tools-and-connect-to-azure"></a>Установка средств и подключение к Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Включение шифрования для существующей или работающей виртуальной машины Windows
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. Сведения о схеме для расширения виртуальной машины см. в статье [Шифрование дисков Azure для Windows ](../extensions/azure-disk-enc-windows.md).

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a>Включение шифрования для существующих или работающих виртуальных машин IaaS Windows
Шифрование можно включить с помощью шаблона, командлетов PowerShell или команд интерфейса командной строки. Сведения о схеме для расширения виртуальной машины см. в статье [Шифрование дисков Azure для Windows ](../extensions/azure-disk-enc-windows.md).

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Включение шифрования на существующих или выполняющихся виртуальных машинах с Azure PowerShell 
Используйте командлет [Set-азвмдискенкриптионекстенсион](/powershell/module/az.compute/set-azvmdiskencryptionextension) , чтобы включить шифрование на работающей виртуальной машине IaaS в Azure. 

-  **Шифрование работающей виртуальной машины:** Приведенный ниже скрипт инициализирует переменные и запускает командлет Set-Азвмдискенкриптионекстенсион. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените значения Микэйваултресаурцеграуп, Мивиртуалмачинересаурцеграуп, Мисекуревм и Мисекуреваулт значениями.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Шифрование работающей виртуальной машины с использованием KEK:** 

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Проверка того, что диски зашифрованы:** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте командлет [Get-азвмдискенкриптионстатус](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Отключение шифрования дисков:** Чтобы отключить шифрование, используйте командлет [Disable-азвмдискенкриптион](/powershell/module/az.compute/disable-azvmdiskencryption) . Отключение шифрования диска данных на виртуальной машине Windows, если шифрование дисков операционной системы и данных не работает должным образом. В этом случае отключите шифрование на всех дисках.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Включение шифрования на существующих или выполняющихся виртуальных машинах с Azure CLI
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
     > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей]. </br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Проверка того, что диски зашифрованы:** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте команду [AZ VM Encryption демонстрация](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Отключение шифрования:** чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Отключение шифрования диска данных на виртуальной машине Windows, если шифрование дисков операционной системы и данных не работает должным образом. В этом случае отключите шифрование на всех дисках.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Использование шаблона Resource Manager

Включить шифрование дисков на существующих или работающих виртуальных машинах IaaS под управлением Windows в Azure можно с помощью [этого шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение, параметры, условия использования и соглашение. Нажмите кнопку **Покупка**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.

В следующей таблице перечислены параметры шаблона Resource Manager для имеющихся или работающих виртуальных машин.

| Параметр | Описание |
| --- | --- |
| vmName | Имя виртуальной машины для выполнения операции шифрования. |
| keyVaultName | Имя хранилища ключей, в которое будет передан ключ BitLocker. Его можно получить с помощью командлета `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` или команды Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroup"`.|
| keyVaultResourceGroup | Имя группы ресурсов, содержащей хранилище ключей.|
|  keyEncryptionKeyURL | URL-адрес ключа шифрования ключа в формате https://@no__t -0keyvault-name&gt;.vault.azure.net/Кэй/&lt;key-Name @ no__t-3. Если вы не хотите использовать KEK, оставьте это поле пустым. |
| volumeType | Тип тома, для которого будет выполняться шифрование. Допустимые значения: _OS_, _Data_ и _All_. 
| forceUpdateTag | Передает уникальное значение, такое как GUID, каждый раз, когда операция должна выполняться принудительно. |
| resizeOSDisk | Если размер раздела ОС будет изменен и займет весь виртуальный жесткий диск ОС до разделения системного тома. |
| location | Расположение для всех ресурсов. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Новые виртуальные машины IaaS, созданные на основе зашифрованных клиентом виртуальных жестких дисков и ключей шифрования

В этом сценарии можно создать новую виртуальную машину на основе предварительно зашифрованного виртуального жесткого диска и связанных с ним ключей шифрования с помощью командлетов PowerShell или команд интерфейса командной строки. 

Используйте инструкции из статьи [Подготовка предварительно зашифрованного виртуального жесткого диска Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Шифрование виртуальных машин с помощью предварительно зашифрованных виртуальных жестких дисков с помощью Azure PowerShell
Вы можете включить шифрование дисков на зашифрованном виртуальном жестком диске с помощью командлета PowerShell [Set-азвмосдиск](/powershell/module/az.compute/set-azvmosdisk#examples). Приведенный ниже пример имеет некоторые общие параметры. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Включение шифрования на добавленном диске данных
[Добавить новый диск на виртуальную машину Windows можно с помощью PowerShell](attach-disk-ps.md) или [портала Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Включение шифрования на добавленном диске данных с помощью Azure PowerShell
 При использовании Powershell для шифрования нового диска для виртуальных машин Windows необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Приведенный ниже сценарий создает GUID для версии последовательности. В некоторых случаях новый добавленный диск данных может быть зашифрован автоматически с помощью расширения шифрования дисков Azure. Автоматическое шифрование обычно происходит, когда виртуальная машина перезапускается после включения нового диска. Обычно это происходит, так как для типа тома указано значение "Все", если ранее на виртуальной машине использовалось шифрование дисков. Если автоматическое шифрование выполняется на вновь добавленном диске данных, рекомендуется снова запустить командлет Set-Азвмдискенкриптионекстенсион с новой версией последовательности. Если для нового диска данных включено автоматическое шифрование и вы хотите отказаться от шифрования, сначала расшифруйте все диски, а затем повторите шифрование с новой версией последовательности, указав соответствующую операционную систему для типа тома. 
  
 

-  **Шифрование работающей виртуальной машины:** Приведенный ниже скрипт инициализирует переменные и запускает командлет Set-Азвмдискенкриптионекстенсион. Группа ресурсов, виртуальная машина и хранилище ключей должны быть уже созданы в качестве необходимых компонентов. Замените значения Микэйваултресаурцеграуп, Мивиртуалмачинересаурцеграуп, Мисекуревм и Мисекуреваулт значениями. В этом примере в качестве значения параметра VolumeType используется значение "All", что включает тома операционной системы и тома данных. Если требуется шифрование только томов операционной системы, используйте "OS" в качестве значения параметра VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Шифрование работающей виртуальной машины с использованием KEK:** В этом примере в качестве значения параметра VolumeType используется значение "All", что включает тома операционной системы и тома данных. Если требуется шифрование только томов операционной системы, используйте "OS" в качестве значения параметра VolumeType.

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Включение шифрования на добавленном диске данных с помощью интерфейса командной строки Azure
 Команда Azure CLI автоматически предоставит вам новую версию последовательности, когда вы запустите команду для включения шифрования. В этом примере используется "All" для параметра volume-type. Может потребоваться изменить значение параметра volume-type на"OS", если необходимо выполнить шифрование только диска операционной системы. В отличие от синтаксиса Powershell, интерфейс командной строки (CLI) не требует предоставления уникальной версии последовательности при включении шифрования. CLI автоматически создает и использует свое собственное уникальное значение версии последовательности.   

-  **Шифрование работающей виртуальной машины:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Шифрование работающей виртуальной машины с использованием KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Отключение шифрования
Вы можете отключить шифрование с помощью Azure PowerShell, Azure CLI или шаблона Resource Manager. Отключение шифрования диска данных на виртуальной машине Windows, если шифрование дисков операционной системы и данных не работает должным образом. В этом случае отключите шифрование на всех дисках.

- **Отключение шифрования дисков с помощью Azure PowerShell:** Чтобы отключить шифрование, используйте командлет [Disable-азвмдискенкриптион](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Отключение шифрования дисков с помощью Azure CLI:** чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Отключение шифрования с помощью шаблона Resource Manager:** 

    1. Нажмите кнопку **Deploy to Azure** (Развернуть в Azure) на странице шаблона [отключения шифрования дисков на виртуальной машине под управлением Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Выберите подписку, группу ресурсов, расположение, тип тома, виртуальную машину, условия использования и соглашение.
    3.  Нажмите кнопку **покупка**, чтобы отключить шифрование дисков на работающей виртуальной машине Windows. 

## <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

Шифрование дисков Azure не работает в следующих сценариях, функциях и технологиях:

- Шифрование ВИРТУАЛЬНЫХ машин базового уровня или виртуальных машин, созданных с помощью классического метода создания виртуальной машины.
- Шифрование виртуальных машин Windows, настроенных на основе программных RAID-систем.
- Интеграция с локальной системой управления ключами.
- служба файлов Azure (общая файловая система);
- сетевая файловая система (NFS);
- динамические тома;
- Контейнеры Windows Server, которые создают динамические тома для каждого контейнера.
- Временные диски ОС.
- Шифрование общих и распределенных файловых систем, таких как (но не ограничиваясь ими), DFS, GFS, ДРДБ и Цеффс.

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о шифровании дисков Azure](disk-encryption-overview.md)
- [Примеры сценариев шифрования дисков Azure](disk-encryption-sample-scripts.md)
- [Устранение неполадок шифрования дисков Azure](disk-encryption-troubleshooting.md)
