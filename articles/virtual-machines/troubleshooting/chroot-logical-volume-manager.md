---
title: Восстановление Linux VMs с помощью chroot, где используется LVM (логический менеджер объемов) - Azure VMs
description: Восстановление Linux VMs с LVM.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684133"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Устранение проблем Linux VM, когда нет доступа к серийной консоли Azure и макет диска использует LVM (Логический менеджер громкости)

Это руководство по устранению неполадок имеет пользу для сценариев, где Linux VM не загружается, это невозможно, а базовый макет файловой системы настроен с LVM (Логический менеджер объемов).

## <a name="take-snapshot-of-the-failing-vm"></a>Сделать снимок неисправного VM

Сфотографать пострадавшего ВМ. 

Снимок будет присоединен к **спасательному** VM. Следуйте инструкциям [здесь](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) о том, как сделать **снимок**.

## <a name="create-a-rescue-vm"></a>Создание спасательного VM
Обычно рекомендуется спасательный VM той же или аналогичной версии операционной системы. Используйте один и тот же **регион** и **группу ресурсов** пострадавшего VM

## <a name="connect-to-the-rescue-vm"></a>Подключение к спасению VM
Подключитесь с помощью ssh в **спасательный** VM. Поднимите привилегии и станьте супер пользователем, используя

`sudo su -`

## <a name="attach-the-disk"></a>Прикрепите диск
Прикрепите диск к **спасательному** VM, сделанному из снимка, сделанного ранее.

Портал Azure - > выбрать **спасательный** VM - > **диски** 

![Создание диска](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Заселять поля. Назначьте имя на новый диск, выберите ту же группу ресурсов, что и снимок, пострадавший VM и Rescue VM.

**Тип Исходного кода** — **снимок** .
**Моментальный снимок Исход** — это имя ранее созданного **снимка.**

![создать диск 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Создайте точку крепления для прикрепленного диска.

`mkdir /rescue`

Выполнить команду **fdisk -l,** чтобы проверить прикрепленный диск моментального снимка и перечислить все доступные устройства и перегородки

`fdisk -l`

Большинство сценариев, прилагается диск моментального снимка будет рассматриваться как **/dev/sdc** отображения двух разделов **/dev/sdc1** и **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Указывает **\*** на раздел загрузки, обе разделы должны быть установлены.

Выполнить команду **lsblk,** чтобы увидеть LVMs пострадавших VM

`lsblk`

![Запуск lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Проверьте, отображаются ли LVM с пострадавшего VM.
Если нет, используйте ниже команды, чтобы включить их и перезапустить **lsblk**.
Убедитесь, что LVMs от прилагаемого диска видны перед продолжением.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Найдите путь для установки Логического Тома, содержащего раздел /(корень). Он имеет файлы конфигурации, такие как /etc/default/grub

В этом примере вывод из предыдущей команды **lsblk** **rootvg-rootlv** является правильным **корнем** LV для установки и может быть использован в следующей команде.

Выход следующей команды покажет путь к **креплению** LV

`pvdisplay -m | grep -i rootlv`

![Рутлв](./media/chroot-logical-volume-manager/locate-rootlv.png)

Приступить к монтажу этого устройства на каталог / спасение

`mount /dev/rootvg/rootlv /rescue`

Смонтировать перегородку, которая имеет **флаг загрузки,** установленный на /rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Проверка файловых систем прикрепленного диска теперь правильно установлена с помощью команды **lsblk**

![Запуск lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

или **df-Th** команды

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Получение доступа к chroot

Получите доступ к **chroot,** который позволит вам выполнять различные исправления, небольшие вариации существуют для каждого дистрибутива Linux.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

При ошибке, например:

**chroot: не удалось запустить команду '/bin/bash': Нет такого файла или каталога**

попытка смонтировать **USr** Логический том

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> При выполнения команд в среде **chroot,** обратите внимание, что они работают против прикрепленного диска ОС, а не местного **спасательного** VM. 

Команды могут использоваться для установки, удаления и обновления программного обеспечения. Устранение проблем в:кв.


Выполните команду lsblk и /спасение теперь / и ![/спасение/загрузка /boot Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Выполните исправления

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Пример 1 - настроить VM для загрузки из другого ядра

Распространенным сценарием является принудительное загрузку VM из предыдущего ядра, поскольку текущее установленное ядро может быть повреждено или обновление не завершено правильно.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*Пошаговое руководство*

Команда **grep** перечисляет ядра, о которым известно **grub.cfg.**
![Ядра](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv список** отображает, какое ядро ![будет загружено при следующем по умолчанию загрузки ядра](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** используется для изменения на ![другой набор ядра Grub2](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** список отображает, какие ядра будут ![загружены на следующий загрузки Новое ядро](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** восстанавливает grub.cfg с ![использованием версий, необходимых Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Пример 2 - обновления пакетов

Неудачное обновление ядра может сделать VM незагружаемым.
Смонтировать все логические тома, чтобы позволить удалять или переустанавливать пакеты

Выполнить команду **lvs,** чтобы проверить, какие **lVs** доступны для монтажа, каждый VM, который был перенесен или исходит от другого поставщика облачных технологий, будет меняться в конфигурации.

Выйдите из среды **chroot** смонтировать необходимый **LV**

![Дополнительно](./media/chroot-logical-volume-manager/advanced.png)

Теперь снова получите доступ к среде **chroot,** запустив

`chroot /rescue`

Все LVs должны быть видны в виде установленных перегородок

![Дополнительно](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Запрос установленного **ядра**

![Дополнительно](./media/chroot-logical-volume-manager/rpm-kernel.png)

При необходимости удалить или обновить **ядро**
![Расширенный](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Пример 3 - включить серийную консоль
Если доступ к серийной консоли Azure невозможен, проверьте параметры конфигурации GRUB для ВМ Ивис и исправьте их. Подробную информацию можно найти [в этом документе](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Пример 4 - загрузка ядра с проблемным объемом свопа LVM

VM может не полностью загрузиться и капли в **драпировщик** подсказку.
Более подробная информация о сбое может быть размещена либо с серийной консоли Azure, либо с навигации на портал Azure - > диагностика загрузки - > Serial log


Ошибка, аналогичная этой, может присутствовать:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Grub.cfg настроен в этом примере для загрузки LV с именем **rd.lvm.lv'VG/SwapVol** и VM не может найти это. Эта строка показывает, как загружается ядро, ссылаясь на LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Удалить обидеть LV из /etc /etc/default/grub конфигурации и восстановить grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Выход chroot и поменять диск ОС

После ремонта проблемы, приступить к unmount и отделить диск от спасения VM позволяет ему быть заменены с пострадавшим диском VM OS.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Отсоедините диск от спасательного VM и выполните disk Swap.

Выберите VM с портала **Диски** и выберите **отсоединить**
![диск Detach](./media/chroot-logical-volume-manager/detach-disk.png) 

Сохранить изменения ![Сохранить отсоединить](./media/chroot-logical-volume-manager/save-detach.png) 

Диск теперь станет доступным, что позволит ему быть заменены с исходным диском ОС пострадавших VM.

Перейдите на портал Azure на неисправный VM и выберите **диски** -> **Swap OS Disk**
![Swap](./media/chroot-logical-volume-manager/swap-disk.png) 

Заполните поля **Выберите диск** моментальный диск только что отделены в предыдущем шаге. Имя VM пострадавшего VM также требуется, то выберите **OK**

![Новый диск os](./media/chroot-logical-volume-manager/new-osdisk.png) 

Если VM работает Диск Своп выключит его, перезагрузить VM после того, как операция дисксвопов завершена.


## <a name="next-steps"></a>Дальнейшие действия
Кроме того, вы можете узнать больше о:

 [Серийная консоль Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Режим единого пользователя](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
