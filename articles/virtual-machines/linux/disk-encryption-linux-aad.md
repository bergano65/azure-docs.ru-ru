---
title: Шифрование дисков Azure с использованием виртуальных машин IaaS под управлением Linux для Azure AD App (предыдущий выпуск)
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин IaaS под управлением Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 35d14599ca9a7abdad88603deeb8431f77e92a92
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748940"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Включение шифрования дисков Azure с помощью Azure AD на виртуальных машинах Linux (предыдущий выпуск)

**Новый выпуск шифрования дисков Azure исключает необходимость предоставления параметра приложения Azure AD для включения шифрования дисков виртуальной машины. В новом выпуске больше не требуется предоставлять учетные данные Azure AD на этапе включения шифрования. Все новые виртуальные машины должны быть зашифрованы без параметров приложения Azure AD с помощью нового выпуска. Инструкции по включению шифрования дисков виртуальной машины с помощью нового выпуска см. в статье [Шифрование дисков Azure для виртуальных машин Linux](disk-encryption-linux.md). Виртуальные машины, которые уже были зашифрованы с помощью параметров приложения Azure AD, по-прежнему поддерживаются и должны продолжать поддерживаться с использованием синтаксиса AAD.**

Возможно несколько сценариев включения шифрования дисков, и последовательность действий для каждого может отличаться. В следующих разделах сценарии для виртуальных машин IaaS под управлением Linux описаны более подробно. Шифрование дисков можно применить только к виртуальным машинам [поддерживаемых размеров виртуальных машин и операционных систем](disk-encryption-overview.md#supported-vms-and-operating-systems). Необходимо также выполнить следующие предварительные требования.

- [Дополнительные требования для виртуальных машин](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Сетевые подключения и групповая политика](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Требования к хранилищу ключей шифрования](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Создайте [моментальный снимок](snapshot-copy-managed-disk.md) и (или) резервную копию перед шифрованием дисков. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. После создания резервной копии можно использовать командлет Set-Азвмдискенкриптионекстенсион для шифрования управляемых дисков, указав параметр-skipVmBackup. Дополнительные сведения см. в статье [Резервное копирование и восстановление зашифрованных виртуальных машин с помощью службы Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Если вы уже использовали [шифрование дисков Azure с помощью приложения Azure AD](disk-encryption-overview-aad.md) для шифрования этой виртуальной машины, этот способ нужно применять и далее для шифрования виртуальной машины. На этой зашифрованной виртуальной машине нельзя использовать [шифрование дисков Azure](disk-encryption-overview.md), так как этот сценарий не работает — переключение из приложения AAD для данной зашифрованной виртуальной машины сейчас не поддерживается.
 > - Чтобы убедиться, что секреты шифрования не пересекают региональные границы, шифрованию дисков Azure требуется, чтобы хранилище ключей и виртуальные машины были расположены в одном регионе. Создайте и используйте хранилище ключей, которое находится в том же регионе, что и виртуальная машина, которую нужно зашифровать.
 > - Шифрование томов операционной системы Linux может занять несколько часов. Обычно для шифрования томов ОС Linux требуется больше времени, чем для томов данных.
> - При шифровании томов Linux ОС виртуальная машина должна считаться недоступной. Настоятельно рекомендуется избегать входа по протоколу SSH во время шифрования, чтобы избежать проблем с блокированием открытых файлов, к которым необходимо получить доступ во время процесса шифрования. Чтобы проверить ход выполнения, можно использовать команды [Get-азвмдискенкриптионстатус](/powershell/module/az.compute/get-azvmdiskencryptionstatus) или [шифрования ВМ](/cli/azure/vm/encryption#az-vm-encryption-show) . Этот процесс может занять несколько часов для тома операционной системы 30 ГБ, а также дополнительное время для шифрования томов данных. Если не используется параметр шифрования всех форматов, время шифрования тома данных будет соответствовать размеру и количеству томов данных. 
 > - Отключение шифрования на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

 

## <a name="bkmk_RunningLinux"> </a> Включение шифрования на виртуальной машине IaaS под управлением Linux, которая уже существует или работает

В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. 

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии командлет Set-Азвмдискенкриптионекстенсион можно использовать для шифрования управляемых дисков, указав параметр-skipVmBackup. Команда Set-Азвмдискенкриптионекстенсион завершится сбоем для виртуальных машин на основе управляемых дисков, пока не будет создана резервная копия и не задан этот параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью Azure CLI 
Можно включить шифрование дисков для зашифрованного виртуального жесткого диска, установив программу командной строки [Azure CLI 2.0](/cli/azure). Его можно использовать в браузере с [Azure Cloud Shell](../../cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell. Чтобы включить шифрование на виртуальных машинах IaaS под управлением Linux, которые уже существуют или работают в Azure, используйте следующие команды CLI.

Используйте команду [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure.

-  **Шифрование работающей виртуальной машины с использованием секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> </br>
Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Убедитесь, что диски зашифрованы:** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте команду [AZ VM Encryption демонстрация](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Отключение шифрования.** Чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью PowerShell
Используйте командлет [Set-азвмдискенкриптионекстенсион](/powershell/module/az.compute/set-azvmdiskencryptionextension) , чтобы включить шифрование на работающей виртуальной машине IaaS в Azure. Создайте [моментальный снимок](snapshot-copy-managed-disk.md) и (или) создайте резервную копию виртуальной машины с [Azure Backup](../../backup/backup-azure-vms-encryption.md) перед шифрованием дисков. Параметр-skipVmBackup уже указан в сценариях PowerShell для шифрования работающей виртуальной машины Linux.

- **Зашифруйте работающую виртуальную машину с помощью секрета клиента:** Приведенный ниже скрипт инициализирует переменные и запускает командлет Set-Азвмдискенкриптионекстенсион. Группа ресурсов, виртуальная машина, хранилище ключей, приложение AAD и секрет клиента должны быть уже созданы в качестве необходимых компонентов. Замените Мивиртуалмачинересаурцеграуп, Микэйваултресаурцеграуп, Мисекуревм, Мисекуреваулт, My-AAD-Client-ID, а мой-AAD-Client-Secret значениями. Измените параметр-параметра volumetype значение, чтобы указать диски, для которых выполняется шифрование.

    ```azurepowershell
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
    ```
- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента.** Шифрование дисков Azure позволяет указать существующий ключ из хранилища ключей для упаковки секретов шифрования диска, которые были сгенерированы при включении шифрования. Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault. Измените параметр-параметра volumetype значение, чтобы указать диски, для которых выполняется шифрование. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > Синтаксис значения параметра Disk-Encryption-keyvault — это полная строка идентификатора:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[Квресаурце-Group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 
    
- **Убедитесь, что диски зашифрованы:** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте командлет [Get-азвмдискенкриптионстатус](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Отключение шифрования дисков.** Для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Включение шифрования на существующей или работающей виртуальной машине с помощью шаблона

Включить шифрование дисков на существующих или работающих виртуальных машинах IaaS под управлением Linux в Azure можно с помощью [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите кнопку **Создать**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.

В следующей таблице перечислены параметры шаблона Resource Manager для существующих или работающих виртуальных машин, которые используют идентификатор клиента Azure AD.

| Параметр | Description (Описание) |
| --- | --- |
| AADClientID | Идентификатор клиента приложения Azure AD, которое имеет разрешения на запись секретов в хранилище ключей. |
| AADClientSecret | Секрет клиента приложения Azure AD, которое имеет разрешения на запись секретов в ваше хранилище ключей. |
| keyVaultName | Имя хранилища ключей, в которое будет передан ключ. Его можно получить с помощью команды интерфейса командной строки Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL-адрес ключа шифрования ключей, который используется для шифрования созданного ключа. Это необязательный параметр, если выбрать **nokek** из раскрывающегося списка UseExistingKek. Если из раскрывающегося списка UseExistingKek выбрано значение **kek**, то потребуется ввести значение _keyEncryptionKeyURL_. |
| volumeType | Тип тома, для которого будет выполняться шифрование. Допустимые поддерживаемые значения — _OS_ или _All_ (поддерживаемые дистрибутивы Linux и их версии для ОС и дисков данных можно просмотреть выше, в разделе предварительных требований). |
| sequenceVersion | Версия последовательности операций с BitLocker. Этот номер версии увеличивается каждый раз, когда шифрование диска выполняется на той же виртуальной машине. |
| vmName | Имя виртуальной машины, для которой будет выполняться шифрование. |
| парольная фраза | Введите надежную парольную фразу, которая будет служить ключом шифрования данных. |



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

### <a name="bkmk_EFATemplate"> </a> Использование параметра EncryptFormatAll с шаблоном
Чтобы использовать параметр EncryptFormatAll, в любом готовом шаблоне Azure Resource Manager, который шифрует виртуальную машину Linux, измените поле **EncryptionOperation** для ресурса AzureDiskEncryption.

1. Например, используйте [шаблон Resource Manager для шифрования работающей виртуальной машины IaaS под управлением Linux](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).
3. Измените значение параметра **EncryptionOperation** с **EnableEncryption** на **EnableEncryptionFormatAl**.
4. Выберите подписку, группу ресурсов, расположение группы ресурсов, другие параметры, условия использования и соглашение. Нажмите кнопку **Создать**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.


### <a name="bkmk_EFAPSH"> </a> Использование параметра EncryptFormatAll с командлетом PowerShell
Используйте командлет [Set-азвмдискенкриптионекстенсион](/powershell/module/az.compute/set-azvmdiskencryptionextension) с параметром `EncryptFormatAll`.

**Зашифруйте работающую виртуальную машину с помощью секрета клиента и енкриптформаталл:** Например, приведенный ниже скрипт инициализирует переменные и выполняет командлет Set-Азвмдискенкриптионекстенсион с параметром Енкриптформаталл. Группа ресурсов, виртуальная машина, хранилище ключей, приложение AAD и секрет клиента должны быть уже созданы в качестве необходимых компонентов. Замените Микэйваултресаурцеграуп, Мивиртуалмачинересаурцеграуп, Мисекуревм, Мисекуреваулт, My-AAD-Client-ID, а мой-AAD-Client-Secret значениями.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Использование параметра EncryptFormatAll с диспетчером логических томов (LVM) 
Рекомендуем установить LVM-on-crypt. Для всех следующих примеров замените путь к устройству и точки подключения в соответствии с вашим вариантом использования. Эту настройку можно выполнить следующим образом.

- Добавить диски данных, которые составляют виртуальную машину.
- Форматировать, подключить и добавить эти диски в FSTAB-файл.

    1. Отформатируйте добавленный диск. Здесь мы используем символические ссылки, созданные Azure. Использование символических ссылок позволяет избежать проблем, связанных с изменениями имен устройств. Дополнительные сведения см. в статье [Устранение неполадок при изменении имени устройства виртуальной машины Linux](troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Подключите диски.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Добавьте сведения в FSTAB-файл.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Выполните командлет PowerShell Set-Азвмдискенкриптионекстенсион с параметром-Енкриптформаталл, чтобы зашифровать эти диски.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Настройте диспетчер логических томов поверх новых дисков. Обратите внимание, что зашифрованные диски разблокируются после завершения загрузки виртуальной машины. Таким образом, подключение диспетчера логических томов также придется отложить.




## <a name="bkmk_VHDpre"> </a> Новые виртуальные машины IaaS, при создании которых используются зашифрованные виртуальные жесткие диски и ключи шифрования
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. В следующих разделах более подробно описаны шаблон Resource Manager и команды интерфейса командной строки. 

Следуйте инструкциям, приведенным в приложении к этой статье, чтобы подготовить предварительно зашифрованные образы, которые можно использовать в Azure. После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.

* [Подготовка предварительно зашифрованного виртуального жесткого диска Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии командлет Set-Азвмдискенкриптионекстенсион можно использовать для шифрования управляемых дисков, указав параметр-skipVmBackup. Команда Set-Азвмдискенкриптионекстенсион завершится сбоем для виртуальных машин на основе управляемых дисков, пока не будет создана резервная копия и не задан этот параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 



### <a name="bkmk_VHDprePSH"> </a> Использование Azure PowerShell для шифрования виртуальных машин IaaS с предварительно зашифрованными виртуальными жесткими дисками 
Вы можете включить шифрование дисков на зашифрованном виртуальном жестком диске с помощью командлета PowerShell [Set-азвмосдиск](/powershell/module/az.compute/set-azvmosdisk#examples). Приведенный ниже пример имеет некоторые общие параметры. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Включение шифрования на добавленном диске данных
Новый диск данных можно добавить с помощью команды [az vm disk attach](add-disk.md) или [через портал Azure](attach-disk-portal.md). Прежде чем запустить шифрование, необходимо подключить только что присоединенный диск данных. Нужно запросить шифрование диска данных, так как диск будет неработоспособным во время шифрования. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Включение шифрования на добавленном диске данных с помощью интерфейса командной строки Azure
 Если виртуальная машина ранее была зашифрована со значением "All", то в дальнейшем значение параметра volume-type должно оставаться "All". Значение "All" включает как диски ОС, так и диски данных. Если виртуальная машина была зашифрована с типом тома "OS", в дальнейшем параметр --volume-type следует заменить на "All", чтобы были включены диск ОС и новый диск данных. Если виртуальная машина была зашифрована с типом тома "Data", то в дальнейшем он может оставаться со значением "Data", как это показано ниже. Добавления и присоединения нового диска данных к виртуальной машине недостаточно для подготовки к шифрованию. Вновь подключенный диск также должен быть отформатирован и правильно подключен к виртуальной машине перед включением шифрования. В Linux диск монтируется в /etc/fstab с [постоянным именем блочного устройства](troubleshoot-device-names-problems.md).  

В отличие от синтаксиса Powershell, интерфейс командной строки (CLI) не требует предоставления уникальной версии последовательности при включении шифрования. CLI автоматически создает и использует свое собственное уникальное значение версии последовательности.

-  **Шифрование работающей виртуальной машины с использованием секрета клиента:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Включение шифрования на добавленном диске данных с помощью Azure PowerShell
 При использовании PowerShell для шифрования нового диска для Linux необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Приведенный ниже сценарий создает GUID для версии последовательности. 
 

-  **Зашифруйте работающую виртуальную машину с помощью секрета клиента:** Приведенный ниже скрипт инициализирует переменные и запускает командлет Set-Азвмдискенкриптионекстенсион. Группа ресурсов, виртуальная машина, хранилище ключей, приложение AAD и секрет клиента должны быть уже созданы в качестве необходимых компонентов. Замените Мивиртуалмачинересаурцеграуп, Микэйваултресаурцеграуп, Мисекуревм, Мисекуреваулт, My-AAD-Client-ID, а мой-AAD-Client-Secret значениями. Для параметра -VolumeType устанавливаются диски данных, а не диск операционной системы. Если виртуальная машина была зашифрована с типом тома "OS" или "All", в дальнейшем параметр -VolumeType следует заменить на "All", чтобы были включены диск ОС и новый диск данных.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента.** Шифрование дисков Azure позволяет указать существующий ключ из хранилища ключей для упаковки секретов шифрования диска, которые были сгенерированы при включении шифрования. Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault. Для параметра -VolumeType устанавливаются диски данных, а не диск операционной системы. Если виртуальная машина была зашифрована с типом тома "OS" или "All", в дальнейшем параметр -VolumeType следует заменить на "All", чтобы были включены диск ОС и новый диск данных.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей]. </br> </br>
Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

## <a name="disable-encryption-for-linux-vms"></a>Отключение шифрования для виртуальных машин Linux
Вы можете отключить шифрование с помощью Azure PowerShell, Azure CLI или шаблона Resource Manager. 

>[!IMPORTANT]
>Отключение шифрования, выполняемого с помощью службы шифрования Azure, на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

- **Отключение шифрования дисков с помощью Azure PowerShell.** Для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Отключение шифрования с помощью Azure CLI.** Для отключения шифрования используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Отключение шифрование с помощью шаблона Resource Manager.** Для отключения используйте шаблон [Отключение шифрования на работающей виртуальной машине Linux](https://aka.ms/decrypt-linuxvm).
     1. Нажмите кнопку **Развернуть в Azure**.
     2. Выберите подписку, группу ресурсов, расположение, виртуальную машину, условия использования и соглашение.
     3.  Нажмите кнопку **покупка**, чтобы отключить шифрование дисков на работающей виртуальной машине Windows. 


## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о шифровании дисков Azure для Linux](disk-encryption-overview-aad.md)
- [Создание и Настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий выпуск)](disk-encryption-key-vault-aad.md)
