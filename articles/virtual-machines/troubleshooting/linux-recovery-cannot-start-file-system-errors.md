---
title: Устранение проблем Linux VM, начиная проблемы из-за ошибок файловой системы (ru) Документы Майкрософт
description: Объясняет, почему Linux VM не может начаться и как решить проблему.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842407"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Устранение проблем Linux VM, начиная проблемы из-за ошибок файловой системы

Вы не можете подключиться к виртуальной машине Azure Linux (VM) с помощью Secure Shell (SSH). При запуске функции Boot Diagnostics на [портале Azure](https://portal.azure.com/)вы видите записи журналов, напоминающие следующие примеры.

## <a name="examples"></a>Примеры

Ниже приведены примеры возможных ошибок.

### <a name="example-1"></a>Пример 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Пример 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Пример 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Пример 4 

Этот пример вызван чистым fsck. В этом случае к VM (/dev/sdc1 и /dev/sde1) также прикрепляются дополнительные диски данных.

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Эта проблема может возникнуть, если файловая система не была закрыта чисто или проблемы, связанные с хранением. Проблемы включают аппаратные или программные ошибки, проблемы с драйверами или программами, ошибки в написании и т.д. Всегда важно иметь резервную перепорку критически важных данных. Инструменты, описывающие в этой статье, могут помочь восстановить файловые системы, но это потеря данных все еще может произойти.

Linux имеет несколько проверки файловой системы доступны. Наиболее распространенными для дистрибутивов в Azure являются: [FSCK,](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific) [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)и [Xfs_repair.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair)

## <a name="resolution"></a>Решение

Чтобы решить эту проблему, загрузите VM в аварийный режим с помощью [серийной консоли](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) и используйте этот инструмент для ремонта файловой системы. Если серийная консоль не включена на вашем VM или не работает, [см.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Использование последовательной консоли

1. Подключение к последовательной консоли

   > [!Note]
   > Для получения дополнительной информации об использовании серийной консоли для Linux см.:
   > * [Используйте серийную консоль для доступа к GRUB и единому пользовательскому режиму](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Используйте серийную консоль для вызовов SysRq и NMI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Выберите кнопку значка питания, а затем выберите Перезапуск VM. (Если серийная консоль не включена или не подключена успешно, кнопку не увидится.)

   ![IMAGE](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Загрузите VM в режим ч. 1 ст.

4. Введите пароль вашей корневой учетной записи, чтобы войти в режим экстренной ситуации.

5. Используйте xfs_repair с опцией -n для обнаружения ошибок в файловой системе. В следующем примере мы предполагаем, что раздел системы /dev/sda1. Замените его соответствующим значением для вашего VM:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Выполнить следующую команду для ремонта файловой системы:

   ```
   xfs_repair /dev/sda1
   ```

7. Если вы получили сообщение об ошибке "ERROR: Файловая система имеет ценные изменения метаданных в журнале, который должен быть воспроизведен", создайте временный каталог и установите файловую систему:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Если диск не может смонтироваться, запустите xfs_repair команду с опцией -L (обнуление журнала силы):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Затем попробуйте смонтировать файловую систему. Если диск установлен успешно, вы получите следующий выход:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Перезапустите VM, а затем проверьте, не решена ли проблема.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

1. Прикрепите системный диск VM в качестве диска данных к восстановлению VM (любой рабочий Linux VM). Для этого можно использовать [команды CLI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) или автоматизировать настройку VM-восстановления с помощью [команд по ремонту VM.](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

2. Найдите дисковую метку системного диска, который вы прикрепили. В этом случае мы предполагаем, что метка системного диска, который вы прикрепили, /dev/sdc1. Замените его соответствующим значением для вашего VM.

3. Используйте xfs_repair с опцией -n для обнаружения ошибок в файловой системе.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Выполнить следующую команду для ремонта файловой системы:

   ```
   xfs_repair /dev/sdc1
   ```

5. Если вы получили сообщение об ошибке "ERROR: Файловая система имеет ценные изменения метаданных в журнале, который должен быть воспроизведен", создайте временный каталог и установите файловую систему:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Если диск не может смонтироваться, запустите xfs_repair команду с опцией -L (обнуление журнала силы):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Затем попробуйте смонтировать файловую систему. Если диск установлен успешно, вы получите следующий выход:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Открепите и отсоедините оригинальный виртуальный жесткий диск, а затем создайте VM с исходного системного диска. Для этого можно использовать [команды CLI](troubleshoot-recovery-disks-linux.md) или [команды по ремонту VM,](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) если вы использовали их для создания восстановления VM.

8. Проверьте, решена ли проблема.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок виртуальной машины Linux путем подключения диска ОС к виртуальной машине восстановления с помощью Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Подключение диска данных к виртуальной машине Linux с помощью портала](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

