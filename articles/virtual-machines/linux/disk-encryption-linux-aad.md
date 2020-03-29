---
title: Шифрование дисков Azure с использованием виртуальных машин IaaS под управлением Linux для Azure AD App (предыдущий выпуск)
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин IaaS под управлением Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970605"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Включить шифрование azure disk с Azure AD на Linux VMs (предыдущий релиз)

Новый выпуск шифрования Azure Disk устраняет необходимость в предоставлении параметра приложения Azure Active Directory (Azure AD) для включения шифрования дисков VM. В новом выпуске больше не требуется вводить учетные данные Azure AD на этапе включения шифрования. Все новые VM должны быть зашифрованы без параметров приложения Azure AD с помощью нового релиза. Для получения инструкций о том, как включить шифрование [Azure Disk Encryption for Linux VMS](disk-encryption-linux.md)диска VM с помощью нового релиза, см. Виртуальные машины, которые уже были зашифрованы с помощью параметров приложения Azure AD, по-прежнему поддерживаются. Их следует и дальше обслуживать с использованием синтаксиса AAD.

Можно включить множество сценариев шифрования дисков, и шаги могут меняться в зависимости от сценария. Следующие разделы более подробно охватывают сценарии для инфраструктуры Linux как для vMs-услуг (IaaS). Вы можете применять шифрование диска только к виртуальным машинам [поддерживаемых размеров VM и операционным системам.](disk-encryption-overview.md#supported-vms-and-operating-systems) Вы также должны выполнить следующие предпосылки:

- [Дополнительные требования к ВМ](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Сетевая и групповая политика](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Требования к хранению ключей шифрования](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Сделайте [снимок,](snapshot-copy-managed-disk.md)сделайте резервную прибежку или оба перед шифровальным диском. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. После создания резервного копирования можно использовать set-AzVMDiskEncryptionEncryptionExtension cmdlet для шифрования управляемых дисков, указав параметр -skipVmBackup. Для получения дополнительной информации о том, как создать резервную связь и восстановить зашифрованные ВМ, [см.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
 > - Если ранее вы использовали [шифрование Azure Disk с помощью приложения Azure AD](disk-encryption-overview-aad.md) для шифрования этого VM, вы должны продолжать использовать эту опцию для шифрования вашего VM. Вы не можете использовать [шифрование Azure Disk](disk-encryption-overview.md) на этом зашифрованном VM, потому что это не поддерживается сценарий, что означает отключение от приложения Azure AD для этого зашифрованного VM еще не поддерживается.
 > - Чтобы секреты шифрования не пересекали региональные границы, Шифрованию диска Azure необходимо, чтобы хранилище ключей и вводы в чате были расположены в одном регионе. Создайте и используйте хранилище ключей, которое в том же регионе, что и VM, для шифрования.
 > - При шифровании объемов Linux OS процесс может занять несколько часов. Это нормально для linux OS объемы занять больше времени, чем объемы данных для шифрования.
> - При шифровании объемов Linux OS VM следует считать недоступным. Мы настоятельно рекомендуем вам избегать sSH логинов в то время как шифрование продолжается, чтобы избежать блокировки любых открытых файлов, которые должны быть доступны во время процесса шифрования. Чтобы проверить прогресс, используйте [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) или [Vm шифрования показать](/cli/azure/vm/encryption#az-vm-encryption-show) команды. Можно ожидать, что этот процесс займет несколько часов для объема 30 ГБ ОС, а также дополнительное время для шифрования объемов данных. Время шифрования объема данных пропорционально размеру и количеству объемов данных, если не используется **формат шифрования.** 
 > - Отключение шифрования на виртуальных машинах Linux поддерживается только для томов данных. Он не поддерживается на данных или объемах ОС, если объем ОС был зашифрован. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Включение шифрования на виртуальной машине IaaS под управлением Linux, которая уже существует или работает

В этом сценарии можно включить шифрование с помощью шаблона менеджера ресурсов Azure, cmdlets PowerShell или команд Azure CLI. 

>[!IMPORTANT]
 >Необходимо сделать снимок или создать резервную перегоняемую резервную связь с управляемым дисковым экземпляром VM за пределами и до включения шифрования azure Disk. Вы можете сделать снимок управляемого диска с портала Azure или использовать [резервное копирование Azure.](../../backup/backup-azure-vms-encryption.md) Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервного копирования используйте cmdlet Set-AzVMDiskEncryptionExtension для шифрования управляемых дисков, указав параметр -skipVmBackup. Команда Set-AzVMDiskEncryptionExtension выходит из строя против управляемых дисковых VMs до тех пор, пока не будет сделана резервная связь и не будет указан этот параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке VM. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Включить шифрование на существующем или запущенном Linux VM с помощью Azure CLI 
Вы можете включить шифрование диска на зашифрованном VHD, установив и используя инструмент командной строки [Azure CLI 2.0.](/cli/azure) Его можно использовать в браузере с [Azure Cloud Shell](../../cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell. Чтобы включить шифрование на виртуальных машинах IaaS под управлением Linux, которые уже существуют или работают в Azure, используйте следующие команды CLI.

Используйте команду [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure.

-  **Шифрование запущенного VM с помощью секрета клиента:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Шифрование запущенного VM с помощью KEK, чтобы обернуть секрет клиента:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > Синтаксис для значения параметра диск-шифрование-ключевой является полной строкой идентификатора: /подписка-id-guid/resourceGroups/'ресурс-группа-имя/провайдеры/Microsoft.KeyVault/vaults/.keyvaults/.keyvault-name».</br> </br> Синтаксис для значения параметра ключа-шифрования ключа является полным URI к KEK, как и в: https://'keyvault-name.vault.vault.azure.net/keys/.kekname/'kekname/'kek-unique-id).

- **Убедитесь, что диски зашифрованы:** Чтобы проверить состояние шифрования IaaS VM, используйте команду [шоу-шоу-шоу az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Отключение шифрования.** Чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Включить шифрование на существующем или запущенном Linux VM с помощью PowerShell
Используйте [cmdlet Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) чтобы включить шифрование на работающей виртуальной машине IaaS в Azure. Сделайте [снимок](snapshot-copy-managed-disk.md) или сделайте резервную резервную часть VM с помощью [резервного копирования Azure](../../backup/backup-azure-vms-encryption.md) до шифрования дисков. Параметр -skipVmBackup уже указан в сценариях PowerShell для шифрования подработаного Linux VM.

- **Шифрование запущенного VM с помощью секрета клиента:** Следующий сценарий инициализирует переменные и запускает Set-AzVMDiskEncryptionExtension cmdlet. Группа ресурсов, VM, хранилище ключей, приложение Azure AD и секрет клиента должны были быть уже созданы в качестве предварительных условий. Замените MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, MySecureVault, My-AAD-client-ID и My-AAD-client-secret с вашими ценностями. Измените параметр -VolumeType, чтобы указать, какие диски вы шифруете.

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
- **Шифрование запущенного VM с помощью KEK, чтобы обернуть секрет клиента:** Шифрование azure Disk позволяет указать существующий ключ в хранилище ключей для обертывания секретов шифрования дисков, которые были сгенерированы при включении шифрования. При указании ключа шифрования Azure Disk Encryption использует этот ключ для обертывания секретов шифрования перед записью в хранилище ключей. Измените параметр -VolumeType, чтобы указать, какие диски вы шифруете. 

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
  > Синтаксис для значения параметра диск-шифрование-ключевой является полная строка идентификатора: /подписка-id-guid/resourceGroups/"KVresource-group-name"/"Поставщики/Microsoft.KeyVault/vaults/'keyvault-name).</br> </br>
  Синтаксис для значения параметра ключа-шифрования ключа является полным URI к KEK, как и в: https://'keyvault-name.vault.vault.azure.net/keys/.kekname/'kekname/'kek-unique-id). 
    
- **Убедитесь, что диски зашифрованы:** Чтобы проверить состояние шифрования IaaS VM, используйте [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Отключение шифрования дисков.** Для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Включение шифрования на существующей или работающей виртуальной машине с помощью шаблона

Включить шифрование дисков на существующих или работающих виртуальных машинах IaaS под управлением Linux в Azure можно с помощью [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Выберите **Развертывание в Azure** на шаблоне быстрого запуска Azure.

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Выберите **Создать** для включения шифрования на существующих или запущенных IaaS VM.

В следующей таблице перечислены параметры шаблона Resource Manager для существующих или работающих виртуальных машин, которые используют идентификатор клиента Azure AD.

| Параметр | Описание |
| --- | --- |
| AADClientID | Идентификатор клиента приложения Azure AD, которое имеет разрешения на запись секретов в хранилище ключей. |
| AADClientSecret | Секрет клиента приложения Azure AD, которое имеет разрешения на запись секретов в ваше хранилище ключей. |
| keyVaultName | Имя хранилища ключей, в которое будет передан ключ. Его можно получить с помощью команды интерфейса командной строки Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL-адрес ключа шифрования ключей, который используется для шифрования созданного ключа. Этот параметр не является обязательным, если вы выберете **nokek** в **списке выпадающих в списке UseExistingKek.** Если вы выберете **кек** в списке выпадающих в **списке UseExistingKek,** необходимо ввести значение _keyEncryptionKeyURL._ |
| volumeType | Тип тома, для которого будет выполняться шифрование. Действительными поддерживаемыми значениями являются _ОС_ или _все_. (См. поддерживаемые дистрибутивы Linux и их версии для ОС и дисков данных в разделе предварительных условий ранее.) |
| sequenceVersion | Версия последовательности операций с BitLocker. Этот номер версии увеличивается каждый раз, когда шифрование диска выполняется на той же виртуальной машине. |
| vmName | Имя виртуальной машины, для которой будет выполняться шифрование. |
| парольная фраза | Введите надежную парольную фразу, которая будет служить ключом шифрования данных. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Используйте функцию EncryptFormatAll для дисков данных на Linux IaaS VMs
Параметр EncryptFormatAll сокращает время шифрования дисков данных Linux. Разделы, отвечающие определенным критериям, отформатированы (с их текущей файловой системой). Затем они возвращаются туда, где они были до выполнения команды. Если вы хотите исключить диск данных, отвечающий критериям, можно выполнить его перед запуском команды.

 После запуска этой команды все диски, которые были установлены ранее, отформатированы. Затем слой шифрования начинается поверх теперь пустого диска. При выборе этой опции также шифруется эфемерный ресурсный диск, прикрепленный к VM. Если эфемерный диск сдан, он переформатирован и повторно зашифрован для VM решением azure Disk Encryption при следующей возможности.

>[!WARNING]
> EncryptFormatAll не следует использовать при наличии необходимых данных о объемах данных VM. Вы можете исключить диски из шифрования, унижая их. Попробуйте параметр EncryptFormatAll на тесте VM, чтобы сначала понять параметр функции и его последствия, прежде чем попробовать его на производстве VM. Вариант EncryptFormatAll форматирует диск данных, поэтому все данные на нем будут потеряны. Прежде чем продолжить работу, убедитесь, что любые диски, которые вы хотите исключить, должным образом не установлены. </br></br>
 >Если вы установите этот параметр во время обновления настроек шифрования, это может привести к перезагрузке перед фактическим шифрованием. В этом случае также необходимо удалить диск, который не требуется отформатировать из файла fstab. Аналогичным образом следует добавить раздел, который требуется для шифрования, в файл fstab, прежде чем инициировать операцию шифрования. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> Критерии EncryptFormatAll
Параметр проходит через все разделы и шифрует их до тех пор, пока они отвечают *всем* следующим критериям: 
- это не корневой или загрузочный раздел, а также не раздел операционной системы;
- этот раздел еще не зашифрован;
- это не том BEK;
- это не том RAID;
- это не том LVM;
- он подключен.

Выполняйте шифрование дисков, которые составляют том RAID или LVM, а не самого тома RAID или LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Использование параметра EncryptFormatAll с шаблоном
Чтобы использовать опцию EncryptFormatAll, используйте любой уже существующий шаблон Azure Resource Manager, который шифрует Linux VM и изменит поле **encryptionOperation** для ресурса AzureDiskEncryption.

1. Например, используйте [шаблон Resource Manager для шифрования работающей виртуальной машины IaaS под управлением Linux](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Выберите **Развертывание в Azure** на шаблоне быстрого запуска Azure.
3. Измените поле **шифрования операции** с **EnableEncryption** на **EnableEncryptionFormatAl.**
4. Выберите подписку, группу ресурсов, расположение группы ресурсов, другие параметры, условия использования и соглашение. Выберите **Создать** для включения шифрования на существующих или запущенных IaaS VM.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> Использование параметра EncryptFormatAll с командлетом PowerShell
Используйте [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet с параметром EncryptFormatAll.

**Шифрование запущенного VM с помощью секрета клиента и EncryptFormatAll:** Например, следующий сценарий инициализирует переменные и запускает cmdlet Set-AzVMDiskEncryptionExtension с параметром EncryptFormatAll. Группа ресурсов, VM, хранилище ключей, приложение Azure AD и секрет клиента должны были быть уже созданы в качестве предварительных условий. Замените MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID и My-AAD-client-secret на ваши ценности.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> Использование параметра EncryptFormatAll с диспетчером логических томов (LVM) 
Рекомендуем установить LVM-on-crypt. Для всех следующих примеров замените устройство-путь и точки крепления тем, что подходит вашему делу использования. Эту настройку можно выполнить следующим образом.

- Добавить диски данных, которые составляют виртуальную машину.
- Форматировать, подключить и добавить эти диски в FSTAB-файл.

    1. Отформатируйте добавленный диск. Здесь мы используем символические ссылки, созданные Azure. Использование символических ссылок позволяет избежать проблем, связанных с изменениями имен устройств. Для получения дополнительной информации смотрите [проблемы с именами устройств Troubleshoot.](troubleshoot-device-names-problems.md)
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Подключите диски.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Добавьте сведения в FSTAB-файл.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Выполнить Set-AzVMDiskEncryptionExtension PowerShell cmdlet с -EncryptFormatAll для шифрования этих дисков.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Настройте диспетчер логических томов поверх новых дисков. Обратите внимание, что зашифрованные диски разблокируются после завершения загрузки виртуальной машины. Таким образом, подключение диспетчера логических томов также придется отложить.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Новые виртуальные машины IaaS, при создании которых используются зашифрованные виртуальные жесткие диски и ключи шифрования
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. В следующих разделах более подробно описаны шаблон Resource Manager и команды интерфейса командной строки. 

Следуйте инструкциям, приведенным в приложении к этой статье, чтобы подготовить предварительно зашифрованные образы, которые можно использовать в Azure. После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.

* [Подготовка предварительно зашифрованного виртуального жесткого диска Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Необходимо сделать снимок или создать резервную перегоняемую резервную связь с управляемым дисковым экземпляром VM за пределами и до включения шифрования azure Disk. Вы можете сделать снимок управляемого диска с портала или использовать [резервное копирование Azure.](../../backup/backup-azure-vms-encryption.md) Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервного копирования используйте cmdlet Set-AzVMDiskEncryptionExtension для шифрования управляемых дисков, указав параметр -skipVmBackup. Команда Set-AzVMDiskEncryptionExtension выходит из строя против управляемых дисковых VMs до тех пор, пока не будет сделана резервная связь и не будет указан этот параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке VM.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Использование Azure PowerShell для шифрования виртуальных машин IaaS с предварительно зашифрованными виртуальными жесткими дисками 
Вы можете включить шифрование диска на зашифрованном VHD с помощью Смдлета PowerShell [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) Следующий пример дает некоторые общие параметры. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Включение шифрования на добавленном диске данных
Вы можете добавить новый диск данных с помощью [az vm диска прикрепить](add-disk.md) или [через портал Azure](attach-disk-portal.md). Прежде чем запустить шифрование, необходимо подключить только что присоединенный диск данных. Вы должны запросить шифрование диска данных, поскольку диск будет непригодным для использования во время шифрования. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Включить шифрование на недавно добавленном диске с Azure CLI
 Если VM ранее был зашифрован с помощью "All", то параметр типа -тома должен оставаться все. Значение "All" включает как диски ОС, так и диски данных. Если VM ранее был зашифрован с типом громкости "OS", то параметр типа -тома должен быть изменен на All так, чтобы и ОС, и новый диск данных были включены. Если VM был зашифрован только с типом громкости "Данные", то он может оставаться данных, как показано здесь. Добавление и присоединение нового диска данных к VM не является достаточной подготовкой для шифрования. Недавно присоединенный диск также должен быть отформатирован и правильно установлен в VM, прежде чем включить шифрование. На Linux, диск должен быть установлен в /etc/fstab с [постоянным названием устройства блока.](troubleshoot-device-names-problems.md) 

В отличие от синтаксиса Powershell, CLI не требует, чтобы вы предоставили уникальную версию последовательности при вводе шифрования. CLI автоматически создает и использует свое собственное уникальное значение версии последовательности.

-  **Шифрование запущенного VM с помощью секрета клиента:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Шифрование запущенного VM с помощью KEK, чтобы обернуть секрет клиента:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Включение шифрования на добавленном диске данных с помощью Azure PowerShell
 Когда вы используете Powershell для шифрования нового диска для Linux, необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Следующий скрипт генерирует GUID для версии последовательности. 
 

-  **Шифрование запущенного VM с помощью секрета клиента:** Следующий сценарий инициализирует переменные и запускает Set-AzVMDiskEncryptionExtension cmdlet. Группа ресурсов, VM, хранилище ключей, приложение Azure AD и секрет клиента должны были быть уже созданы в качестве предварительных условий. Замените MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, MySecureVault, My-AAD-client-ID и My-AAD-client-secret с вашими ценностями. Для параметра -VolumeType устанавливаются диски данных, а не диск операционной системы. Если VM ранее был зашифрован с типом громкости "OS" или "All", то параметр -VolumeType должен быть изменен на Все, чтобы и ОС, и новый диск данных будут включены.

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
- **Шифрование запущенного VM с помощью KEK, чтобы обернуть секрет клиента:** Шифрование azure Disk позволяет указать существующий ключ в хранилище ключей для обертывания секретов шифрования дисков, которые были сгенерированы при включении шифрования. При указании ключа шифрования Azure Disk Encryption использует этот ключ для обертывания секретов шифрования перед записью в хранилище ключей. Для параметра -VolumeType устанавливаются диски данных, а не диск операционной системы. Если VM ранее был зашифрован с типом громкости "OS" или "All", то параметр -VolumeType должен быть изменен на Все, чтобы и ОС, и новый диск данных будут включены.

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
> Синтаксис для значения параметра диск-шифрование-ключевой является полной строкой идентификатора: /подписка-id-guid/resourceGroups/'ресурс-группа-имя/провайдеры/Microsoft.KeyVault/vaults/.keyvaults/.keyvault-name». </br> </br>
Синтаксис для значения параметра ключа-шифрования ключа является полным URI к KEK, как и в: https://'keyvault-name.vault.vault.azure.net/keys/.kekname/'kekname/'kek-unique-id).

## <a name="disable-encryption-for-linux-vms"></a>Отключение шифрования для виртуальных машин Linux
Отключить шифрование можно с помощью Azure PowerShell, CLI Azure или шаблона менеджера ресурсов. 

>[!IMPORTANT]
>Отключение шифрования, выполняемого с помощью службы шифрования Azure, на виртуальных машинах Linux поддерживается только для томов данных. Он не поддерживается на данных или объемах ОС, если объем ОС был зашифрован. 

- **Отключено шифрование диска с помощью Azure PowerShell:** Чтобы отключить шифрование, используйте [cmdlet Disable-AzureRmMMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Отключение шифрования с помощью Azure CLI.** Для отключения шифрования используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Отключить шифрование с помощью шаблона «Менеджер ресурсов»:** Чтобы отключить шифрование, используйте [шифрование Отключите на бегущий шаблон Linux VM.](https://aka.ms/decrypt-linuxvm)
     1. Выберите **Развертывание в Azure**.
     2. Выберите подписку, группу ресурсов, расположение, виртуальную машину, условия использования и соглашение.
     3. Выберите **Покупка,** чтобы отключить шифрование диска на подходящей Windows VM. 


## <a name="next-steps"></a>Дальнейшие действия

- [Лазурное шифрование дисков для обзора Linux](disk-encryption-overview-aad.md)
- [Создание и настройка хранилища ключей для шифрования дисков Azure с помощью Azure AD (предыдущий релиз)](disk-encryption-key-vault-aad.md)
