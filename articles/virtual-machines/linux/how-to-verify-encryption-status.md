---
title: Как проверить статус шифрования для Linux
description: В этой статье содержатся инструкции по проверке состояния шифрования с уровня платформы и ОС.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123425"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Как проверить статус шифрования для Linux 

**Этот сценарий применяется для aDE двойного прохода и однопроходных расширений.**  
Эта область документа должна проверить состояние шифрования виртуальной машины с помощью различных методов.

### <a name="environment"></a>Среда

- Дистрибутивы Linux

### <a name="procedure"></a>Процедура

Виртуальная машина была зашифрована с помощью двойного прохода или однопроходного.

Статус шифрования может быть проверен во время или после шифрования с помощью различных методов.

>[!NOTE] 
>Мы используем переменные по всему документу, заменяя значения соответствующим образом.

### <a name="verification"></a>Проверка

Проверка может быть выполнена с портала, PowerShell, Аз-CLI и, или со стороны VM OS. 

Эта проверка может быть сделана путем проверки дисков, прикрепленных к определенному VM. 

Или задавая вопрос о настройках шифрования на каждом отдельном диске независимо от того, прикреплен ли диск или не привязан.

Ниже различных методов проверки:

## <a name="using-the-portal"></a>Использование портала

Проверка статуса шифрования, проверяя раздел расширений на портале Azure.

В разделе **Расширения** вы увидите расширение ADE. 

Нажмите на него и посмотрите на **сообщение о состоянии,** оно будет указывать текущий статус шифрования:

![Проверка портала No1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

В списке расширений вы увидите соответствующую версию расширения ADE. Версия 0.x соответствует ADE Dual-Pass, а версия 1.x соответствует ADE Single-pass.

Вы можете получить более подробную информацию, нажав на расширение, а затем на *Просмотр подробный статус*.

Более подробное состояние процесса шифрования вы увидите в формате json:

![Проверка портала No2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Проверка портала No3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Другой способ проверки статуса шифрования — это взгляд на раздел **Диски.**

![Проверка портала No4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Этот статус означает, что диски имеют настройки шифрования штамп, но не то, что они были фактически зашифрованы на уровне ОС. По замыслу дисков диски сначала штампуются и затем зашифрованы. Если процесс шифрования завершается неудачей, диски могут в конечном итоге проштамповать, но не зашифровать. Чтобы подтвердить, действительно ли диски зашифрованы, можно дважды проверить шифрование каждого диска на уровне ОС.

## <a name="using-powershell"></a>Использование PowerShell

Вы можете проверить **общий** статус шифрования зашифрованного VM, используя следующие команды PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![проверить PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Настройки шифрования можно запечатлеть с каждого отдельного диска, используя следующие команды PowerShell:

### <a name="single-pass"></a>Одноместный пас
Если однопроходные настройки шифрования штампуют на каждом из дисков (ОС и данные), можно зафиксировать настройки шифрования диска ОС за один проход следующим образом:

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Проверка Единого прохода ОС 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Если на диске нет настроек шифрования, вывод будет пустым, как показано ниже:

![Настройки шифрования ОС 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Настройки шифрования диска данных::

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Проверка данных одного ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Двойной пас
В Dual Pass настройки шифрования штампованы в модели VM, а не на каждом отдельном диске.

Для проверки настроек шифрования, проштампованных в двойном проходе, можно использовать следующие команды:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Проверить двойной проход PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Неприкрепленные диски

Проверьте настройки шифрования для дисков, которые не подключены к VM.

### <a name="managed-disks"></a>Управляемые диски
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>Использование Аз-CLI

Вы можете проверить **общий** статус шифрования зашифрованного VM, используя следующие команды A CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Проверить общее использование CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Единый пропуск
Вы можете проверить настройки шифрования с каждого отдельного диска, используя следующие команды CLI:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Настройки шифрования данных](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> В случае, если на диске нет настроек шифрования, он будет отображаться как "Диск не зашифрован"

Подробные настройки состояния и шифрования:

Диск ОС:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Диски данных:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Данные одного CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Двойной пропуск

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Проверить общий двойной с помощью CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) Вы также можете проверить настройки шифрования на VM модель хранения профиля диска ОС:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Проверить vm профиль двойной с помощью CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Неприкрепленные диски

Проверьте настройки шифрования для дисков, которые не подключены к VM.

### <a name="managed-disks"></a>Управляемые диски

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Неуправляемые диски

Неуправляемые диски — это VHD-файлы, которые хранятся в учетных записях хранения Azure как страничные BLOB-объекты.

Чтобы получить подробную информацию о конкретном диске, необходимо предоставить:

Идентификатор учетной записи хранилища, содержащей диск.
Строка подключения для этой учетной записи хранилища.
Название контейнера, который хранит диск.
Имя диска.

Эта команда перечисляет все иные иудеи для всех учетных записей хранения:

```bash
az storage account list --query [].[id] -o tsv
```
Идоты учетной записи хранилища перечислены в следующей форме:

/подписка/\<идентификатор\<подписок>/ресурсныегруппы/имя группы\<ресурсов>/провайдеры/Microsoft.Storage/storageAccounts/имя учетной записи хранилища>

Выберите подходящий идентификатор и храните его на переменной:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Строка подключения.

Эта команда получает строку соединения для одной конкретной учетной записи хранилища и хранит ее на переменной:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Имя контейнера.

Следующая команда перечисляет все контейнеры под учетной записью хранения:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Контейнер, используемый для дисков, обычно называется "vhds"

Храните имя контейнера на переменной 
```bash
ContainerName="name of the container"
```

Имя диска.

Используйте эту команду, чтобы перечислить все капли на определенном контейнере
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Выберите диск, который вы хотите задать запрос и сохранить его имя на переменной.
```bash
DiskName="diskname.vhd"
```
Запрос настроек шифрования диска
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Из ОС
Проверка, если разделы диска данных зашифрованы (а диск ОС - нет)

Когда раздел/диск зашифрован, он отображается как тип **склепа,** когда он не зашифрован, он отображается как **часть/тип диска**

``` bash
lsblk
```

![Слой Ос Крипт ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Более подробную информацию можно получить, используя следующий вариант "lsblk". 

Вы увидите слой типа **склепа,** который устанавливается расширением.

Следующий пример показывает логические тома и нормальные диски, имеющие "**крипто\_LUKS FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Слой Ос Крипт 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

В качестве дополнительного шага можно также проверить, загружены ли дискданные данные

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

И какие dm устройства перечислены как склеп

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Next Steps

- [Устранение неполадок с шифрованием дисков Azure](disk-encryption-troubleshooting.md)
