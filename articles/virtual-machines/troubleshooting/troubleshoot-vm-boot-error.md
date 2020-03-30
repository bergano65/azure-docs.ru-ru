---
title: Linux VM сапоги для Grub спасения
description: Виртуальная машина не смогла загрузиться из-за того, что виртуальная машина вошла в спасательную консоль
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561955"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM сапоги для Grub спасения

Мы установили, что ваша виртуальная машина (VM) вошла в спасательную консоль. Проблема возникает, когда в Linux VM недавно были применены изменения ядра, такие как обновление ядра, и больше не запускается должным образом из-за ошибок ядра во время процесса загрузки. Во время процесса загрузки, когда загрузочный погрузчик пытается найти ядро Linux и передать ему управление ботинком, VM входит в спасательную консоль, когда передача выходит из строя.

Если вы обнаружите, что в будущем вы не можете подключиться к VM, вы можете просмотреть скриншот вашего VM с помощью лезвия диагностики ботинок на портале Azure. Это может помочь диагностировать проблему и определить, является ли ее причиной такая же ошибка загрузки.

## <a name="recommended-steps"></a>Рекомендуемые действия

Следуйте ниже приведены меры по смягчению последствий в зависимости от получаемой ошибки:

### <a name="error---unknown-filesystem"></a>Ошибка - Неизвестная файловая система

* Если вы получаете ошибку **Unknown filesystem,** эта ошибка может возникнуть в результате повреждения файловой системы на разделе загрузки или неправильной конфигурации ядра.

   * Для проблем файловой системы выполните последующие шаги в статье [Linux Recovery: Cannot SSH к Linux VM из-за ошибок файловой системы (fsck, inodes).](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)
   * Для проблем с ядром выполните последующие шаги в статье [Linux Recovery: Manually fixing non-boot issues, связанных с проблемами ядра,](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)или Linux Recovery: Исправление проблем без [загрузки, связанных с проблемами ядра с использованием chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Ошибка - Файл не найден

* Если вы получаете ошибка **Ошибка 15: Файл не найден или первоначальный диск оперативной памяти** или **initrd / initramfs файл не найден,** следуйте ниже.

    * Для отсутствующих `/boot/grub2/grub.cfg` `initrd/initramfs` файлов или приступить к следующему процессу:

    1. Убедитесь, `/etc/default/grub` что существуют и имеют правильные/желаемые настройки. Если вы не знаете, какие параметры по умолчанию, вы можете проверить с рабочим VM.

    2. Затем запустите следующую команду для регенерации конфигурации:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Если `/boot/grub/menu.lst`недостающий файл, эта ошибка для старых версий ОС **(RHEL 6.x**, **Centos 6.x** и **Ubuntu 14.04),** так что команды могут отличаться. Вам придется спина до старого сервера и тест, чтобы обеспечить правильные команды предоставляются.

### <a name="error---no-such-partition"></a>Ошибка - Нет такой раздел

* Если вы получаете ошибку **Нет такой раздел,** обратитесь к [Сценарий случая : "нет такой раздел" ошибка при попытке запустить VM после попытки расширить диск ОС.](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)

### <a name="error---grubcfg-file-not-found"></a>Ошибка - файл grub.cfg не найден

* Если вы получаете ошибку **/ загрузки / GRUB2/grub.cfg файл не найден,** следуйте шагам ниже.

    * Для отсутствующих `/boot/grub2/grub.cfg` `initrd/initramfs` файлов или приступить к следующему процессу:

    1. Убедитесь, `/etc/default/grub` что существуют и имеют правильные/желаемые настройки. Если вы не знаете, какие параметры по умолчанию, вы можете проверить с рабочим VM.

    2. Затем запустите следующую команду для `grub2-mkconfig -o /boot/grub2/grub.cfg`регенерации конфигурации: .

   * Если `/boot/grub/menu.lst`недостающий файл, эта ошибка для старых версий ОС **(RHEL 6.x**, **Centos 6.x** и **Ubuntu 14.04),** так что команды могут отложить. Закрутите старый сервер и проверьте его, чтобы обеспечить правильные команды.

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор агента виртуальной машины Azure](../extensions/agent-windows.md)
* [Обзор расширений и компонентов виртуальной машины под управлением Windows](../extensions/features-windows.md)

