---
title: Проверка состояния шифрования для Linux
description: В этой статье приведены инструкции по проверке состояния шифрования на уровне платформы и операционной системы.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123425"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Проверка состояния шифрования для Linux 

**Этот сценарий применяется для расширений ADE с двойным проходом и с одним проходом.**  
Эта область документа предназначена для проверки состояния шифрования виртуальной машины с помощью различных методов.

### <a name="environment"></a>Среда

- Дистрибутивах Linux

### <a name="procedure"></a>Процедура

Виртуальная машина была зашифрована с помощью двойного или единственного этапа.

Состояние шифрования можно проверить во время или после шифрования с помощью различных методов.

>[!NOTE] 
>Мы используем переменные по всему документу, заменяя соответствующие значения.

### <a name="verification"></a>Проверка

Проверку можно выполнить на портале, PowerShell, AZ CLI и на стороне ОС виртуальной машины. 

Эту проверку можно выполнить, проверив диски, подключенные к определенной виртуальной машине. 

Или, запросив параметры шифрования на каждом отдельном диске независимо от того, подключен диск или не подключен.

Ниже приведены различные методы проверки.

## <a name="using-the-portal"></a>Использование портала

Проверьте состояние шифрования, проверив раздел расширения на портал Azure.

В разделе **Extensions (расширения** ) вы увидите расширение ade, указанное в списке. 

Щелкните его и взгляните на **сообщение о состоянии**, оно будет указывать текущее состояние шифрования:

![Номер чека на портале 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

В списке расширений вы увидите соответствующую версию расширения ADE. Версия 0. x соответствует двойным проходам ADE и версии 1. x соответствует ADE с одним проходом.

Вы можете получить дополнительные сведения, щелкнув расширение, а затем — *просмотреть подробное состояние*.

Вы увидите более подробное состояние процесса шифрования в формате JSON:

![Номер чека на портале 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Номер чека на портале 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Другой способ проверки состояния шифрования — Просмотр раздела **диски** .

![Номер чека на портале 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Это состояние означает, что на дисках настроены параметры шифрования, но не все они были на самом деле зашифрованы на уровне операционной системы. При проектировании диски сначала отмечаться первыми и шифруются позже. В случае сбоя процесса шифрования диски могут оказаться помеченными, но не зашифрованы. Чтобы убедиться, что диски действительно зашифрованы, можно дважды проверить шифрование каждого диска на уровне операционной системы.

## <a name="using-powershell"></a>Использование PowerShell

Вы можете проверить **Общее** состояние шифрования ЗАШИФРОВАННОЙ виртуальной машины с помощью следующих команд PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Проверка PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Вы можете записать параметры шифрования для каждого отдельного диска с помощью следующих команд PowerShell:

### <a name="single-pass"></a>Один проход
Если один проход, параметры шифрования отправляются на каждый из дисков (ОС и данные), вы можете записать параметры шифрования диска ОС в один проход следующим образом:

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
![Проверка одного прохода ОС 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Если на диске не установлены параметры шифрования, выходные данные будут пустыми, как показано ниже.

![Параметры шифрования ОС 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Записать параметры шифрования дисков данных:

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
![Проверка данных Single PS 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Двойной проход
При двойном прохождении параметры шифрования отправляются в модель виртуальной машины, а не на каждый отдельный диск.

Чтобы убедиться, что параметры шифрования были помечены двойным проходом, можно использовать следующие команды:

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
![Проверка двойного прохода PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Неподключенные диски

Проверьте параметры шифрования для дисков, которые не подключены к виртуальной машине.

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
## <a name="using-az-cli"></a>Использование команды AZ CLI

Вы можете проверить **Общее** состояние шифрования ЗАШИФРОВАННОЙ виртуальной машины с помощью следующих команд AZ CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Проверка общего использования CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Один проход
Вы можете проверить параметры шифрования для каждого отдельного диска, выполнив следующие команды AZ CLI:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Параметры шифрования данных](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Если на диске не установлены параметры шифрования, он будет отображаться как "диск не зашифрован".

Подробные сведения о состоянии и параметрах шифрования:

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

![оссинглекли](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

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

![Одиночная CLI для данных ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Двойной проход

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Проверка общего числа двойных ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) ИНТЕРФЕЙСОВ командной строки. Вы также можете проверить параметры шифрования в профиле хранилища модели виртуальной машины на диске ОС:

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

![Проверка профиля виртуальной машины с двойным использованием интерфейса командной строки ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Неподключенные диски

Проверьте параметры шифрования для дисков, которые не подключены к виртуальной машине.

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

Чтобы получить сведения о конкретном диске, необходимо указать:

Идентификатор учетной записи хранения, содержащей диск.
Строка подключения для конкретной учетной записи хранения.
Имя контейнера, в котором хранится диск.
Имя диска.

Эта команда выводит список всех идентификаторов для всех учетных записей хранения.

```bash
az storage account list --query [].[id] -o tsv
```
Идентификаторы учетной записи хранения перечислены в следующей форме:

Идентификатор\<подписки/Subscriptions/>/resourcegroups/\<имя группы ресурсов>/провидерс/Микрософт.стораже/сторажеаккаунтс/\<имя учетной записи хранения>

Выберите соответствующий идентификатор и сохраните его в переменной:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Строка подключения.

Эта команда возвращает строку подключения для одной конкретной учетной записи хранения и сохраняет ее в переменной:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Имя контейнера.

Следующая команда выводит список всех контейнеров в учетной записи хранения:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Контейнер, используемый для дисков, обычно называется "VHD".

Сохранение имени контейнера в переменной 
```bash
ContainerName="name of the container"
```

Имя диска.

Используйте эту команду, чтобы вывести список всех больших двоичных объектов в определенном контейнере.
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Выберите диск, к которому нужно запросить, и сохраните его имя в переменной.
```bash
DiskName="diskname.vhd"
```
Запрос параметров шифрования диска
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Из операционной системы
Проверка шифрования разделов диска данных (и диска ОС)

При шифровании раздела или диска он отображается как тип **шифрования** , если он не зашифрован, он отображается как **часть или тип диска** .

``` bash
lsblk
```

![Уровень шифрования ОС ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Дополнительные сведения можно получить, используя следующий вариант "лсблк". 

Вы увидите слой типа **шифрования** , смонтированный расширением.

В следующем примере показаны логические тома и обычные диски с "**шифрованием\_LUKS фстипе**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Уровень шифрования ОС 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

В качестве дополнительного шага можно также проверить, загружен ли на диске данных ключи.

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

И какие устройства DM перечислены как зашифрованные

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Дальнейшие шаги

- [Устранение неполадок с шифрованием дисков Azure](disk-encryption-troubleshooting.md)
