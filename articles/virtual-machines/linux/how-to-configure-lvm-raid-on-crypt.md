---
title: Налажить LVM и RAID на зашифрованных устройствах - Шифрование дисков Azure
description: В этой статье содержатся инструкции по настройке LVM и RAID на зашифрованных устройствах для Linux VMs.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657447"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Настройка LVM и RAID на зашифрованных устройствах

Эта статья представляет собой поэтапный процесс выполнения логических управлений объемами (LVM) и RAID на зашифрованных устройствах. Процесс применяется к следующим средам:

- Дистрибутивы Linux
    - RHEL 7.6
    - Увунту 18,04"
    - SUSE 12"
- Расширение однопроходного шифрования дисков Azure
- Расширение двухпроходного шифрования Azure Disk


## <a name="scenarios"></a>Сценарии

Процедуры в этой статье поддерживают следующие сценарии:  

- Настройка LVM поверх зашифрованных устройств (LVM-на-crypt)
- Наконфигурните RAID поверх зашифрованных устройств (RAID-на-crypt)

После того, как базовое устройство или устройства будут зашифрованы, вы можете создать структуры LVM или RAID поверх этого зашифрованного слоя. 

Физические тома (PVs) создаются поверх зашифрованного слоя. Физические тома используются для создания группы объемов. Вы создаете тома и добавляете необходимые записи на /etc/fstab. 

![Диаграмма слоев структур LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Аналогичным образом устройство RAID создается поверх зашифрованного слоя на дисках. Файловая система создается поверх устройства RAID и добавляется в /etc/fstab в качестве обычного устройства.

## <a name="considerations"></a>Рекомендации

Мы рекомендуем использовать LVM-на-crypt. RAID — это вариант, когда LVM не может быть использован из-за определенных ограничений приложения или среды.

Вы будете использовать опцию **EncryptFormatAll.** Для получения дополнительной информации об этой опции, [см.](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms)

Хотя вы можете использовать этот метод, когда вы также шифрования ОС, мы просто шифрования данных дисков здесь.

Процедуры предполагают, что вы уже рассмотрели предпосылки в [сценариях шифрования дисков Azure на Linux VMs](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) и в [квикстарте: Создание и шифрование Linux VM с помощью Azure CLI.](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart)

Двухпроходная версия Azure Disk Encryption находится на пути амортизации и больше не должна использоваться в новых шифрованиях.

## <a name="general-steps"></a>основные шаги

При использовании конфигураций "на склепе" используйте процесс, описанный в следующих процедурах.

>[!NOTE] 
>Мы используем переменные на протяжении всей статьи. Заменить значения соответственно.

### <a name="deploy-a-vm"></a>Развертывание виртуальной машины 
Следующие команды не являются обязательными, но мы рекомендуем применить их на недавно развернутой виртуальной машине (VM).

PowerShell.

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Прикрепите диски к VM
Повторите следующие `$N` команды для количества новых дисков, которые вы хотите прикрепить к VM.

PowerShell.

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Проверить, что диски прикреплены к VM
PowerShell.
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Список прикрепленных дисков в PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Список прикрепленных дисков в Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Портал:

![Список прикрепленных дисков на портале](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

ОС:

```bash
lsblk 
```
![Список прикрепленных дисков в ОС](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Настройка дисков для шифрования
Эта конфигурация выполняется на уровне операционной системы. Соответствующие диски настроены для традиционного шифрования с помощью шифрования Azure Disk:

- Файловые системы создаются поверх дисков.
- Для установки файловых систем создаются временные точки крепления.
- Файловые системы настроены на /etc/fstab для установки во время загрузки.

Проверьте письмо устройства, назначенное новым дискам. В этом примере мы используем четыре диска данных.

```bash
lsblk 
```
![Диски данных, прикрепленные к ОС](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Создание файловой системы поверх каждого диска
Эта команда итерирует создание файловой системы ext4 на каждом диске, определенной на "в" части цикла "для".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Создание файловой системы ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Найдите универсально уникальный идентификатор (UUID) файловых систем, которые вы недавно создали, создайте временную папку, добавьте соответствующие записи на /etc/fstab, и установите все файловые системы.

Эта команда также итерирует на каждом диске, определенном на "в" части цикла "для":

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Убедитесь, что диски установлены должным образом
```bash
lsblk
```
![Список установленных временных файловых систем](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Также убедитесь, что диски настроены:

```bash
cat /etc/fstab
```
![Информация о конфигурации через fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Шифрование дисков данных
PowerShell с помощью ключа шифрования (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI с помощью KEK:

```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>Проверка состояния шифрования

Продолжайте делать следующий шаг только тогда, когда все диски зашифрованы.

PowerShell.

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Статус шифрования в PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Статус шифрования в ClI Azure](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Портал:

![Статус шифрования на портале](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Уровень ОС:

```bash
lsblk
```
![Статус шифрования в ОС](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Расширение добавит файловые системы в /var/lib/azure_disk_encryption_config/azure_crypt_mount (старое шифрование) или в /etc/crypttab (новые шифрования).

>[!NOTE] 
>Не изменяйте ни один из этих файлов.

Этот файл будет заботиться об активации этих дисков во время процесса загрузки, так что LVM или RAID может использовать их позже. 

Не беспокойтесь о точках крепления в этом файле. Шифрование azure Disk потеряет возможность запускать диски в качестве обычной файловой системы после того, как мы создадим физический том или устройство RAID поверх этих зашифрованных устройств. (Это удалит формат файловой системы, который мы использовали во время процесса подготовки.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Удалить временные папки и временные записи fstab
Вы разгрузите файловые системы на дисках, которые будут использоваться как часть LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
И удалить /etc / fstab записей:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Убедитесь, что диски не установлены и что записи на /etc/fstab были удалены

```bash
lsblk
```
![Проверка того, что временные файловые системы не установлены](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

И убедитесь, что диски настроены:
```bash
cat /etc/fstab
```
![Проверка того, что временные записи fstab удаляются](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Шаги для LVM-на-склепе
Теперь, когда базовые диски зашифрованы, можно создать структуры LVM.

Вместо того, чтобы использовать имя устройства, используйте /dev/mapper пути для каждого из дисков, чтобы создать физический объем (на слое склепа в верхней части диска, а не на самом диске).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Настройка LVM поверх зашифрованных слоев
#### <a name="create-the-physical-volumes"></a>Создание физических объемов
Вы получите предупреждение, которое спрашивает, если это нормально, чтобы уничтожить подпись файловой системы. Продолжить, введя **у**, или использовать **эхо "y",** как показано на рисунке:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Проверка того, что физический том был создан](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Имена /dev/mapper/устройства здесь должны быть заменены для ваших фактических значений на основе вывода **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Проверка информации на физические объемы
```bash
pvs
```

![Информация для физических объемов](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Создание группы громкости
Создайте группу громкости, используя те же устройства, которые уже инициализированы:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Проверка информации для группы томов

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Информация для группы объемов](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Создание логических томов

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Проверьте созданные логические тома

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Информация для логических томов](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Создание файловых систем поверх структур для логических томов

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Создание точек крепления для новых файловых систем

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Добавьте новые файловые системы в /etc/fstab и смонтирите их

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Проверить, что новые файловые системы установлены

```bash
lsblk -fs
df -h
```
![Информация для установленных файловых систем](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

На этом варианте **lsblk**мы перечисляем устройства, показывающие зависимости в обратном порядке. Эта опция помогает идентифицировать устройства, сгруппированные по логическому объему, а не исходные имена /dev/sd'disk.

Важно убедиться, что опция **nofail** добавляется к параметрам точек крепления томов LVM, созданных поверх устройства, зашифрованного с помощью шифрования Azure Disk. Это предотвращает застрять ОС во время процесса загрузки (или в режиме обслуживания).

Если вы не используете опцию **nofail:**

- ОС никогда не попадет в стадию запуска лазурного диска и разблокируются и монтируются диски данных. 
- Зашифрованные диски будут разблокированы в конце процесса загрузки. Объемы LVM и файловые системы будут автоматически монтированы до тех пор, пока шифрование Azure Disk не разблокирует их. 

Вы можете протестировать перезагрузку VM и проверить, что файловые системы также автоматически устанавливаются после загрузки. Этот процесс может занять несколько минут, в зависимости от количества и размеров файловых систем.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Перезагрузка VM и проверить после перезагрузки

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Шаги для RAID-на-склепе
Теперь, когда базовые диски зашифрованы, можно продолжить создание структур RAID. Процесс такой же, как и для LVM, но вместо того, чтобы использовать имя устройства, используйте /dev/mapper пути для каждого диска.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Настройка RAID поверх зашифрованного слоя дисков
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Информация для настроенного RAID через команду mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Имена /dev/mapper/устройства здесь должны быть заменены на фактические значения, основанные на выходе **lsblk.**

### <a name="checkmonitor-raid-creation"></a>Проверка /монитор RAID создание
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Статус RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Создание файловой системы поверх нового устройства RAID
```bash
mkfs.ext4 /dev/md10
```

Создайте новую точку крепления для файловой системы, добавьте новую файловую систему в /etc/fstab, и установите ее:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Убедитесь, что новая файловая система установлена:

```bash
lsblk -fs
df -h
```
![Информация для установленных файловых систем](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Важно убедиться, что опция **nofail** добавляется в параметры точки крепления томов RAID, созданных поверх устройства, зашифрованного с помощью шифрования Azure Disk. Это предотвращает застрять ОС во время процесса загрузки (или в режиме обслуживания).

Если вы не используете опцию **nofail:**

- ОС никогда не попадет в стадию запуска лазурного диска и разблокируются и монтируются диски данных.
- Зашифрованные диски будут разблокированы в конце процесса загрузки. Объемы и файловые системы RAID будут автоматически монтированы до тех пор, пока шифрование Azure Disk не разблокирует их.

Вы можете протестировать перезагрузку VM и проверить, что файловые системы также автоматически устанавливаются после загрузки. Этот процесс может занять несколько минут, в зависимости от количества и размеров файловых систем.

```bash
shutdown -r now
```

И когда вы можете войти в систему:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Дальнейшие действия

- [Устранение неполадок с шифрованием дисков Azure](disk-encryption-troubleshooting.md)

