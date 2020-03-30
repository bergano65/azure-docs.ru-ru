---
title: Как настроить LVM и RAID на склепе на Linux VM
description: В этой статье содержатся инструкции по настройке LVM и RAID на склепе на Linux VMs.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284914"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>Как настроить LVM и RAID на склепе

Этот документ представляет собой поэтапный процесс о том, как выполнять LVM на крипто и рейд на конфигурации crypt.

### <a name="environment"></a>Среда

- Дистрибутивы Linux
    - RHEL 7.6
    - Увунту 18,04"
    - SUSE 12"
- ADE Единый пропуск
- Двойной пропуск ADE


## <a name="scenarios"></a>Сценарии

**Этот сценарий применим к расширениям aDE с двойным проходом и одним проходом.**  

- Настройка LVM поверх зашифрованных устройств (LVM-на-Crypt)
- Наконфигурните RAID поверх зашифрованных устройств (RAID-on-Crypt)

После шифрования базового устройства можно создать структуры LVM или RAID поверх этого зашифрованного слоя. Физические объемы (PV) создаются поверх зашифрованного слоя.
Физические объемы используются для создания группы объемов.
Вы создаете тома и добавляете необходимые записи на /etc/fstab. 

![Проверка дисков, прикрепленных PowerShell](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Аналогичным образом устройство RAID создается поверх зашифрованного слоя на дисках. Файловая система создается поверх устройства RAID и добавляется в /etc/fstab в качестве обычного устройства.

### <a name="considerations"></a>Рекомендации

Рекомендуемый метод для использования LVM-на-Crypt.

RAID учитывается, когда LVM не может быть использован из-за определенных ограничений приложения/среды.

Вы будете использовать вариант EncryptFormatAll, информация об https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmsэтой функции находится здесь: .

Хотя этот метод может быть сделано, когда также шифрование ОС, мы просто шифрования дисков данных.

Эта процедура предполагает, что вы уже рассмотрели https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux предварительные https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstartтребования, упомянутые здесь: и здесь .

Версия aDE с двойным пропуском находится на пути амортизации и больше не должна использоваться на новых шифрованиях ADE.

### <a name="procedure"></a>Процедура

При использовании конфигураций "на склепе" вы будете следить за процессом, изложенным ниже:

>[!NOTE] 
>Мы используем переменные по всему документу, заменяя значения соответствующим образом.
## <a name="general-steps"></a>основные шаги
### <a name="deploy-a-vm"></a>Развертывание виртуальной машины 
>[!NOTE] 
>Хотя это необязательно, мы рекомендуем вам применить это на недавно развернутом VM.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Интерфейс командной строки.
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
### <a name="attach-disks-to-the-vm"></a>Прикрепите диски к vm:
Повторите для $N количество новых дисков, которые вы хотите прикрепить к VM PowerShell
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
Интерфейс командной строки.
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Проверить диски прилагаются к VM:
PowerShell.
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Проверьте диски,](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) прикрепленные PowerShell CLI:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Проверьте диски прилагается](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) ![CLI Портал: Проверьте диски прилагается CLI](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) OS:
```bash
lsblk 
```
![Проверка прикрепленных дисков портала](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>Настройка дисков для шифрования
Эта конфигурация сделана, что уровень операционной системы, соответствующие диски настроены для традиционного шифрования ADE:

Файлсистемы создаются поверх дисков.

Для установки файлов создаются временные точки крепления.

Filesystems настроены на /etc/fstab для установки во время загрузки.

Проверьте письмо устройства, назначенное новым дискам, на этом примере мы используем четыре диска данных

```bash
lsblk 
```
![Проверка дисков прилагается os](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Создайте файловую систему поверх каждого диска.
Эта команда итерирует создание файловой системы ext4 на каждом диске, определенном на "в" части цикла "для".
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Проверьте диски](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) прилагается os Найти UUID файлов недавно созданных систем, создать временную папку, чтобы смонтировать его, добавить соответствующие записи на / и т.д. / fstab и установить все файловые системы.

Эта команда также итерирует на каждом диске, определенном на "в" части цикла "для":
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Проверить диски установлены должным образом:
```bash
lsblk
```
![Проверьте временные](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) файлы, установленные и настроенные:
```bash
cat /etc/fstab
```
![Проверьте fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>Шифрование дисков данных:
PowerShell с помощью KEK:
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
CLI с использованием KEK:
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
![Проверьте](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) шифрование ps CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Проверьте шифрование](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) ![CLI](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) Портал: Проверьте шифрование OS OS Уровень:
```bash
lsblk
```
![Проверить шифрование CLI](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Расширение добавит файловые системы в "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (старое шифрование) или добавлено в "/etc/crypttab" (новые шифрования).

Не изменяйте ни один из этих файлов.

Этот файл будет заботиться об активации этих дисков во время процесса загрузки, чтобы они могли быть позже использованы LVM или RAID. 

Не беспокойтесь о точках крепления на этом файле, так как ADE потеряет возможность получить диски, установленные в качестве обычной файловой системы после того, как мы создадим физический объем или устройство рейда поверх этих зашифрованных устройств (который избавится от формата файловой системы, который мы использовали во время процесс подготовки).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>Удалите папки темпа и временные записи fstab
Вы разгрузите файловые системы на дисках, которые будут использоваться как часть LVM
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
И удалить /etc / fstab записей:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Убедитесь, что диски не установлены и что записи на /etc/fstab были удалены
```bash
lsblk
```
![Проверьте системы](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) temp unmounted и настроены:
```bash
cat /etc/fstab
```
![Проверка записей temp fstab удаляются](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>Для LVM-на-склепе
Теперь, когда базовые диски зашифрованы, можно приступить к созданию структур LVM.

Вместо того, чтобы использовать имя устройства, используйте /dev/mapper пути для каждого из дисков, чтобы создать физический объем (на слое склепа в верхней части диска не на самом диске).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Настройка LVM поверх зашифрованных слоев
#### <a name="create-the-physical-volumes"></a>Создание физических объемов
Вы получите предупреждение с просьбой, если это нормально, чтобы уничтожить подпись файловой системы. 

Вы можете продолжить, введя 'y' или использовать эхо "y", как показано на рисунке:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>Имена /dev/mapper/device здесь должны быть заменены для ваших фактических значений на основе вывода lsblk.
#### <a name="verify-the-physical-volumes-information"></a>Проверка информации о физических объемах
```bash
pvs
```
![проверить физические объемы 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>Создание группы громкости
Создание VG с использованием тех же устройств, которые уже инициализированы
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>Проверка информации группы громкости
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![проверить физические объемы 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Создание логических томов
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>Проверка созданных логических объемов
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![проверить lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Создание файлов поверх структуры логического объема (ы) (ы)
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Создание точек крепления для новых файлов
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
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Проверить, что новые файловые системы установлены
```bash
lsblk -fs
df -h
```
![проверить логические](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) тома На этом варианте lsblk, мы перечисляем устройства, показывающие зависимости от обратного порядка, эта опция помогает определить устройства, сгруппированные по логическому объему, а не оригинальные /dev/sd'disk названия устройств.

Важно: убедитесь, что опция "nofail" добавляется к параметрам точек крепления объемов LVM, созданных поверх зашифрованного устройства ADE. Важно, чтобы избежать ОС от застрять во время процесса загрузки (или в режиме обслуживания). 

Зашифрованный диск разблокируются в конце процесса загрузки, объемы LVM и файловые системы будут автоматически установлены.

Если опция nofail не используется, ОС никогда не попадет на стадию запуска ADE, а диск данных (ы) разблокирован и установлен.

Вы можете протестировать перезагрузку VM и проверки файловых систем также автоматически смонтированы после загрузки времени. 

Примите во внимание, что этот процесс может занять несколько минут в зависимости от количества файловых систем и размеров
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Перезагрузка VM и проверить после перезагрузки
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>Для RAID-на-Crypt
Теперь основные диски зашифрованы вы можете продолжать создавать RAID структур, так же, как LVM, вместо того, чтобы использовать имя устройства, использовать / Dev / Mapper пути для каждого из дисков.

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
![mdadm создать](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>Имена /dev/mapper/device здесь должны быть заменены для ваших фактических значений на основе вывода lsblk.
#### <a name="checkmonitor-the-raid-creation"></a>Проверьте/проконтролировать создание RAID:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![проверить mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Создайте файловую систему поверх нового устройства Raid:
```bash
mkfs.ext4 /dev/md10
```
Создайте новую точку крепления для файловой системы, добавьте новую файловую систему в /etc/fstab, и установите ее
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Проверить, что новые файловые системы установлены
```bash
lsblk -fs
df -h
```
![проверить mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Важно: убедитесь, что опция "nofail" добавляется к параметрам точки крепления томов RAID, созданных поверх зашифрованного устройства ADE. 

Очень важно, чтобы избежать ОС от застрять во время процесса загрузки (или в режиме обслуживания). 

Зашифрованный диск будет разблокирован в конце процесса загрузки, а объемы RAID и файловые системы будут автоматически монтироваться до тех пор, пока они не будут разблокированы ADE, если опция nofail не будет использована.

ОС никогда не попадет в стадию запуска ADE, а диски данных разблокированы и установлены.

Вы можете протестировать перезагрузку VM и проверки файловых систем также автоматически смонтированы после загрузки времени. Примите во внимание, что этот процесс может занять несколько минут в зависимости от количества файловых систем и размеров
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

