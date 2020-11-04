---
title: Изменение размера диска ОС с разделом GPT | Документация Майкрософт
description: В этой статье приведены инструкции по изменению размера диска ОС с разделом GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: baa260e911673ea99b292ab5dc9895840d0098ef
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340340"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Изменение размера диска ОС с разделом GPT

> [!NOTE]
> Этот сценарий применим только к дискам ОС с разделом GPT (таблица разделов GPT).

В этой статье описывается, как увеличить размер диска ОС с разделом GPT в Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Необходимо определить, содержит ли диск ОС раздел MBR или GPT

Используйте `parted` команду, чтобы указать, создан ли раздел диска с помощью раздела основной загрузочной записи (MBR) или раздела GPT.

### <a name="mbr-partition"></a>Раздел MBR

В следующих выходных данных **таблица разделов** отображает значение **msdos**. Это значение указывает на раздел MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Раздел GPT

В следующих выходных данных **таблица разделов** отображает значение **gpt**. Это значение указывает на наличие раздела GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Если виртуальная машина имеет раздел GPT на диске операционной системы, необходимо увеличить размер диска операционной системы.

## <a name="increase-the-size-of-the-os-disk"></a>Увеличение размера диска ОС

Приведенные ниже инструкции относятся к дистрибутивам, рекомендованным Linux.

> [!NOTE]
> Прежде чем продолжить работу, создайте резервную копию виртуальной машины или моментальный снимок диска операционной системы.

### <a name="ubuntu"></a>Ubuntu

Увеличение размера диска операционной системы в Ubuntu 16.x и 18.x:

1. Остановите виртуальную машину.
1. Увеличьте размер диска операционной системы на портале.
1. Перезапустите виртуальную машину, а затем войдите на виртуальную машину с учетными данными **корневого** пользователя.
1. Убедитесь в том, что размер файловой системы на диске ОС увеличился.

Как показано в следующем примере, размер диска ОС на портале теперь равен 100 ГБ. Файловая система **/dev/sda1** , смонтированная на **/** , теперь имеет размер 97 ГБ.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Увеличение размера диска ОС в SUSE 12 SP4, SUSE SLES 12 для SAP, SUSE SLES 15 и SUSE SLES 15 для SAP.

1. Остановите виртуальную машину.
1. Увеличьте размер диска операционной системы на портале.
1. Перезапустите виртуальную машину.

После перезапуска виртуальной машины выполните следующие действия.

1. Войдите на виртуальную машину с учетными данными **корневого** , используя следующую команду:

   ```
   # sudo -i
   ```

1. Используйте следующую команду, чтобы установить пакет **гровпарт** , который будет использоваться для изменения размера раздела:

   ```
   # zypper install growpart
   ```

1. Используйте `lsblk` команду, чтобы найти раздел, подключенный в корне файловой системы ("/"). В этом случае мы видим, что раздел 4 устройства SDA подключен к/:

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Измените размер требуемой секции с помощью `growpart` команды, используя номер раздела, указанный на предыдущем шаге.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Выполните `lsblk` команду еще раз, чтобы проверить, увеличилась ли секция.

   Следующие выходные данные показывают, что размер раздела **/dev/sda4** был изменен до 46,5 ГБ:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Укажите тип файловой системы на диске ОС с помощью `lsblk` команды с `-f` флагом:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Для изменения размера файловой системы используйте команды, соответствующие типу файловой системы.
   
   Для **xfs** используется следующая команда:
   
   ```
   #xfs_growfs /
   ```
   
   Выходные данные примера:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Для **ext4** используется следующая команда:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. С помощью следующей команды проверьте, изменился ли размер файловой системы для **df -Th** :
   
   ```
   #df -Thl
   ```
   
   Выходные данные примера:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   В предыдущем примере можно увидеть, что размер файловой системы для диска ОС увеличился.

### <a name="rhel-lvm"></a>RHEL LVM

Увеличение размера диска операционной системы в RHEL 7.x с LVM:

1. Остановите виртуальную машину.
1. Увеличьте размер диска операционной системы на портале.
1. Запустите виртуальную машину.

После перезапуска виртуальной машины выполните следующие действия.

1. Войдите на виртуальную машину с учетными данными **корневого** , используя следующую команду:
 
   ```
   #sudo su
   ```

1. Установите пакет **gptfdisk** , который необходим для увеличения размера диска ОС.

   ```
   #yum install gdisk -y
   ```

1. Чтобы просмотреть самый большой сектор, доступный на диске, выполните следующую команду:

   ```
   #sgdisk -e /dev/sda
   ```

1. Измените размер раздела, не удаляя его, с помощью следующей команды. Команда **parted** содержит параметр с именем **resizepart** , который позволяет изменить размер раздела, не удаляя его. Число 4 после **resizepart** указывает на изменение размера четвертой секции.

   ```
   #parted -s /dev/sda "resizepart 4 -1" quit
   ```
    
1. Выполните следующую команду, чтобы убедиться в том, что раздел был увеличен:

   ```
   #lsblk
   ```

   Следующие выходные данные показывают, что размер раздела **/dev/sda4** теперь составляет 99 ГБ.

   ```
   [user@myvm ~]# lsblk
   NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   fd0                 2:0    1    4K  0 disk
   sda                 8:0    0  100G  0 disk
   ├─sda1              8:1    0  500M  0 part /boot/efi
   ├─sda2              8:2    0  500M  0 part /boot
   ├─sda3              8:3    0    2M  0 part
   └─sda4              8:4    0   99G  0 part
   ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
   ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
   ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
   ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
   ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
   └─rootvg-rootlv   253:5    0    2G  0 lvm  /
   sdb                 8:16   0   50G  0 disk
   └─sdb1              8:17   0   50G  0 part /mnt/resource
   ```

1. Используйте следующую команду для изменения размера физического тома (ФТ):

   ```
   #pvresize /dev/sda4
   ```

   Следующие выходные данные показывают, что размер ФТ теперь составляет 99,02 ГБ.

   ```
   [user@myvm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   [user@myvm ~]# pvs
   PV         VG     Fmt  Attr PSize   PFree
   /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
   ```

1. В следующем примере размер **/dev/mapper/rootvg-rootlv** изменен с 2 ГБ на 12 ГБ (увеличен на 10 ГБ) с помощью следующей команды. Эта команда также позволяет изменить размер файловой системы.

   ```
   #lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Выходные данные примера:

   ```
   [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```
         
1. Проверьте, увеличился ли размер файловой системы **/dev/mapper/rootvg-rootlv** , с помощью следующей команды:

   ```
   #df -Th /
   ```

   Выходные данные примера:

   ```
   [user@myvm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [user@myvm ~]#
   ```

> [!NOTE]
> Чтобы использовать ту же процедуру для изменения размера другого логического тома, измените имя **lv** в шаге 7.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE]
>Прежде чем увеличивать размер диска ОС, всегда сделайте снимок виртуальной машины.

Чтобы увеличить размер диска операционной системы в RHEL с необработанным разделом:

Остановите виртуальную машину.
Увеличьте размер диска операционной системы на портале.
Запустите виртуальную машину.
После перезапуска виртуальной машины выполните следующие действия.

1. Войдите на виртуальную машину с учетными данными **корневого** , используя следующую команду:
 
   ```
   sudo su
   ```

1. Установите пакет **gptfdisk** , который необходим для увеличения размера диска ОС.

   ```
   yum install gdisk -y
   ```

1.  Чтобы просмотреть все доступные секторы на диске, выполните следующую команду:
    ```
    gdisk -l /dev/sda
    ```

1. Вы увидите сведения, уведомляющие о типе секции. Убедитесь, что это GPT. Определяет корневой раздел. Не изменяйте и не удаляйте загрузочный раздел (загрузочный раздел BIOS) и системный раздел ("системный раздел EFI").

1. Используйте следующую команду, чтобы начать секционирование в первый раз. 
    ```
    gdisk /dev/sda
    ```

1. Теперь вы увидите сообщение с запросом следующей команды (команда:? для справки). 

   ```
   w
   ```

1. Появится предупреждение "предупреждение! Дополнительный заголовок помещается на диске слишком рано. Вы хотите устранить эту проблему? (Y/N): ". Необходимо нажать "Y"

   ```
   Y
   ```

1. Должно отобразиться сообщение о завершении окончательных проверок и запросе подтверждения. Нажмите "Y"

   ```
   Y
   ```

1. Проверьте, правильно ли произошло все с помощью команды партпробе

   ```
   partprobe
   ```

1. Описанные выше шаги гарантируют, что дополнительный заголовок GPT помещается в конец. Следующим шагом является запуск процесса изменения размера с помощью средства гдиск. Используйте следующую команду.

   ```
   gdisk /dev/sda
   ```
1. Чтобы просмотреть список разделов, в меню Команда нажмите кнопку "p". Выявление корневого раздела (в шагах sda2 считается корневым разделом) и загрузочным разделом (в шагах sda3 считается загрузочным разделом). 

   ```
   p
   ```
    ![Корневой раздел и загрузочный раздел](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Нажмите клавишу "d", чтобы удалить секцию и выбрать номер секции, назначенный для загрузки (в этом примере это "3")
   ```
   d
   3
   ```
1. Нажмите клавишу "d", чтобы удалить секцию и выбрать номер секции, назначенный для загрузки (в этом примере — "2").
   ```
   d
   2
   ```
    ![Удалить корневой раздел и загрузочный раздел](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Чтобы повторно создать корневой раздел с увеличенным размером, нажмите "n", введите номер раздела, который вы ранее удалили для корневого ("2" в этом примере), и выберите первый сектор в качестве значения по умолчанию, последний сектор — "Последнее значение сектора — размер загрузочного сектора" ("4096" в этом случае) и шестнадцатеричный код как "8300".
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Чтобы повторно создать загрузочный раздел, нажмите "n", введите номер раздела, который вы ранее удалили для загрузки ("3" для этого примера), и выберите первый сектор в качестве значения по умолчанию, последний сектор — "значение по умолчанию" и шестнадцатеричный код как "EF02"
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Запишите изменения с помощью команды "w" и нажмите клавишу "Y" для подтверждения
   ```
   w
   Y
   ```
1. Выполните команду "партпробе", чтобы проверить стабильность диска
   ```
   partprobe
   ```
1. Перезагрузите виртуальную машину, и размер корневого раздела увеличится.
   ```
   reboot
   ```

   ![Новый корневой раздел и загрузочный раздел](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Выполните команду xfs_growfs в разделе, чтобы изменить ее размер.
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS расширение FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Изменение размера диска](expand-disks.md)
