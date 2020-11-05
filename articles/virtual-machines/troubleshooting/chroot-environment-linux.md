---
title: Среда чрут на виртуальной машине Linux.
description: В этой статье описывается, как устранить неполадки среды чрут на виртуальной машине в Linux.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: 0d8a030061ef6aa848344152edaa3267ad916e2a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377944"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Среда чрут на виртуальной машине Linux

В этой статье описывается, как устранить неполадки среды чрут на виртуальной машине в Linux.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16. x && Ubuntu 18. x

1. Останавливает или отмените выделение затронутой виртуальной машины.
1. Создайте образ виртуальной машины для одной и той же версии ОС в той же группе ресурсов (RSG) и расположении, где используется управляемый диск.
1. Используйте портал Azure, чтобы создать моментальный снимок диска ОС затронутой виртуальной машины.
1. Создайте диск из моментального снимка диска ОС и подключите его к виртуальной машине "помощь".
1. После создания диска устраните неполадки среды чрут на виртуальной машине.

   1. Получите доступ к виртуальной машине от имени привилегированного пользователя с помощью следующей команды:

      `sudo su -`

   1. Найдите диск с помощью `dmesg` (метод, используемый для обнаружения нового диска, может отличаться). В следующем примере **дмесг** используется для фильтрации по дискам **SCSI** :

      `dmesg | grep SCSI`

      Выходные данные будут выглядеть так, как показано в следующем примере. В этом примере нам нужен диск **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Используйте следующие команды для доступа к среде чрут:

      ```
      mkdir /rescue
      mount /dev/sdc1 /rescue
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Устранение неполадок среды чрут.

   1. Чтобы выйти из среды чрут, используйте следующие команды:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue
      ```

      > [!NOTE]
      > Если появится сообщение об ошибке `unable to unmount /rescue` , добавьте параметр-l в команду umount.
      >
      > Например, `umount -l /rescue`.

1. Отключите диск от виртуальной машины и выполните переключение диска с исходной виртуальной машины.
1. Запустите исходную виртуальную машину и проверьте ее подключение.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/CentOS/Oracle 6. x && Oracle 8. x && RHEL/CentOS 7. x с необработанными секциями

1. Останавливает или отмените выделение затронутой виртуальной машины.
1. Создайте образ виртуальной машины для одной и той же версии ОС в той же группе ресурсов (RSG) и расположении, где используется управляемый диск.
1. Используйте портал Azure, чтобы создать моментальный снимок диска ОС затронутой виртуальной машины.
1. Создайте диск из моментального снимка диска ОС и подключите его к виртуальной машине "помощь".
1. После создания диска устраните неполадки среды чрут на виртуальной машине.

   1. Получите доступ к виртуальной машине от имени привилегированного пользователя с помощью следующей команды:

      `sudo su -`

   1. Найдите диск с помощью `dmesg` (метод, используемый для обнаружения нового диска, может отличаться). В следующем примере **дмесг** используется для фильтрации по дискам **SCSI** :

      `dmesg | grep SCSI`

      Выходные данные будут выглядеть так, как показано в следующем примере. В этом примере нам нужен диск **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Используйте следующие команды для доступа к среде чрут:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Устранение неполадок среды чрут.

   1. Чтобы выйти из среды чрут, используйте следующие команды:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/
      umount /rescue
      ```

      > [!NOTE]
      > Если появится сообщение об ошибке `unable to unmount /rescue` , добавьте параметр-l в команду umount.
      >
      > Например, `umount -l /rescue`.

1. Отключите диск от виртуальной машины и выполните переключение диска с исходной виртуальной машины.
1. Запустите исходную виртуальную машину и проверьте ее подключение.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/CentOS 7. x с LVM

   > [!NOTE]
   > Если исходная виртуальная машина содержит диспетчер логических томов (LVM) на диске операционной системы, создайте виртуальную машину для подготовки, используя образ с необработанными секциями на диске ОС.

1. Останавливает или отмените выделение затронутой виртуальной машины.
1. Создайте образ виртуальной машины для одной и той же версии ОС в той же группе ресурсов (RSG) и расположении, где используется управляемый диск.
1. Используйте портал Azure, чтобы создать моментальный снимок диска ОС затронутой виртуальной машины.
1. Создайте диск из моментального снимка диска ОС и подключите его к виртуальной машине "помощь".
1. После создания диска устраните неполадки среды чрут на виртуальной машине.

   1. Получите доступ к виртуальной машине от имени привилегированного пользователя с помощью следующей команды:

      `sudo su -`

   1. Найдите диск с помощью `dmesg` (метод, используемый для обнаружения нового диска, может отличаться). В следующем примере **дмесг** используется для фильтрации по дискам **SCSI** :

      `dmesg | grep SCSI`

      Выходные данные будут выглядеть так, как показано в следующем примере. В этом примере нам нужен диск **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Чтобы активировать группу логических томов, используйте следующую команду:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Используйте `lsblk` команду, чтобы получить имена LVM:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Используйте следующие команды для доступа к среде чрут:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/mapper/rootvg-optlv /rescue/opt
      mount /dev/sdc2 /rescue/boot/
      mount /dev/sdc1 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Устранение неполадок среды чрут.

   1. Чтобы выйти из среды чрут, используйте следующие команды:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue/opt
      umount /rescue
      ```

      > [!NOTE]
      > Если появится сообщение об ошибке `unable to unmount /rescue` , добавьте параметр-l в команду umount.
      >
      > Например, `umount -l /rescue`.

1. Отключите диск от виртуальной машины и выполните переключение диска с исходной виртуальной машины.
1. Запустите исходную виртуальную машину и проверьте ее подключение.

## <a name="rhel-8x-with-lvm"></a>RHEL 8. x с LVM

   > [!NOTE]
   > Если исходная виртуальная машина содержит диспетчер логических томов (LVM) на диске операционной системы, создайте виртуальную машину для подготовки, используя образ с необработанными секциями на диске ОС.

1. Останавливает или отмените выделение затронутой виртуальной машины.
1. Создайте образ виртуальной машины для одной и той же версии ОС в той же группе ресурсов (RSG) и расположении, где используется управляемый диск.
1. Используйте портал Azure, чтобы создать моментальный снимок диска ОС затронутой виртуальной машины.
1. Создайте диск из моментального снимка диска ОС и подключите его к виртуальной машине "помощь".
1. После создания диска устраните неполадки среды чрут на виртуальной машине.

   1. Получите доступ к виртуальной машине от имени привилегированного пользователя с помощью следующей команды:

      `sudo su -`

   1. Найдите диск с помощью `dmesg` (метод, используемый для обнаружения нового диска, может отличаться). В следующем примере **дмесг** используется для фильтрации по дискам **SCSI** :

      `dmesg | grep SCSI`

      Выходные данные будут выглядеть так, как показано в следующем примере. В этом примере нам нужен диск **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Чтобы активировать группу логических томов, используйте следующую команду:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Используйте `lsblk` команду, чтобы получить имена LVM:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Используйте следующие команды для доступа к среде чрут:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Устранение неполадок среды чрут.

   1. Чтобы выйти из среды чрут, используйте следующие команды:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue
      ```

      > [!NOTE]
      > Если появится сообщение об ошибке `unable to unmount /rescue` , добавьте параметр-l в команду umount.
      >
      > Например, `umount -l /rescue`.

1. Отключите диск от виртуальной машины и выполните переключение диска с исходной виртуальной машины.
1. Запустите исходную виртуальную машину и проверьте ее подключение.

## <a name="oracle-7x"></a>Oracle 7. x

1. Останавливает или отмените выделение затронутой виртуальной машины.
1. Создайте образ виртуальной машины для одной и той же версии ОС в той же группе ресурсов (RSG) и расположении, где используется управляемый диск.
1. Используйте портал Azure, чтобы создать моментальный снимок диска ОС затронутой виртуальной машины.
1. Создайте диск из моментального снимка диска ОС и подключите его к виртуальной машине "помощь".
1. После создания диска устраните неполадки среды чрут на виртуальной машине.

   1. Получите доступ к виртуальной машине от имени привилегированного пользователя с помощью следующей команды:

      `sudo su -`

   1. Найдите диск с помощью `dmesg` (метод, используемый для обнаружения нового диска, может отличаться). В следующем примере **дмесг** используется для фильтрации по дискам **SCSI** :

      `dmesg | grep SCSI`

      Выходные данные будут выглядеть так, как показано в следующем примере. В этом примере нам нужен диск **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Используйте следующие команды для доступа к среде чрут:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Устранение неполадок среды чрут.

   1. Чтобы выйти из среды чрут, используйте следующие команды:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Если появится сообщение об ошибке `unable to unmount /rescue` , добавьте параметр-l в команду umount.
      >
      > Например, `umount -l /rescue`.

1. Отключите диск от виртуальной машины и выполните переключение диска с исходной виртуальной машины.
1. Запустите исходную виртуальную машину и проверьте ее подключение.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 для SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 с пакетом обновления 1 (SP1) для SAP

1. Останавливает или отмените выделение затронутой виртуальной машины.
1. Создайте образ виртуальной машины для одной и той же версии ОС в той же группе ресурсов (RSG) и расположении, где используется управляемый диск.
1. Используйте портал Azure, чтобы создать моментальный снимок диска ОС затронутой виртуальной машины.
1. Создайте диск из моментального снимка диска ОС и подключите его к виртуальной машине "помощь".
1. После создания диска устраните неполадки среды чрут на виртуальной машине.

   1. Получите доступ к виртуальной машине от имени привилегированного пользователя с помощью следующей команды:

      `sudo su -`

   1. Найдите диск с помощью `dmesg` (метод, используемый для обнаружения нового диска, может отличаться). В следующем примере **дмесг** используется для фильтрации по дискам **SCSI** :

      `dmesg | grep SCSI`

      Выходные данные будут выглядеть так, как показано в следующем примере. В этом примере нам нужен диск **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Используйте следующие команды для доступа к среде чрут:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc4 /rescue
      mount -o nouuid /dev/sdc3 /rescue/boot/
      mount /dev/sdc2 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Устранение неполадок среды чрут.

   1. Чтобы выйти из среды чрут, используйте следующие команды:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Если появится сообщение об ошибке `unable to unmount /rescue` , добавьте параметр-l в команду umount.
      >
      > Например, `umount -l /rescue`.

1. Отключите диск от виртуальной машины и выполните переключение диска с исходной виртуальной машины.
1. Запустите исходную виртуальную машину и проверьте ее подключение.

## <a name="next-steps"></a>Дальнейшие шаги

- [Устранение неполадок SSH-подключения](troubleshoot-ssh-connection.md)
