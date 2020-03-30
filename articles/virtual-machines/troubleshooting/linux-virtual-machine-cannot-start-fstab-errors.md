---
title: Устранение проблем Linux VM, начиная проблемы из-за ошибок fstab Документы Майкрософт
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
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351152"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Устранение проблем Linux VM, начиная проблемы из-за ошибок fstab

Вы не можете подключиться к виртуальной машине Azure Linux (VM) с помощью подключения Secure Shell (SSH). При запуске [функции Boot Diagnostics](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) на [портале Azure](https://portal.azure.com/)вы видите записи журналов, напоминающие следующие примеры:

## <a name="examples"></a>Примеры

Ниже приведены примеры возможных ошибок.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Пример 1: Диск устанавливается SCSI ID вместо универсально уникального идентификатора (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Пример 2: На CentOS отсутствует устройство без прикрепляемого

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Пример 3: VM не может начаться из-за неправильной конфигурации fstab или из-за того, что диск больше не присоединен

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Пример 4: В серийном входе журнала отображается неправильный UUID

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Эта проблема может возникнуть, если синтаксис таблицы файловых систем (fstab) неверна или если необходимый диск данных, отображаемый на входе в файле "/etc/fstab", не присоединен к VM.

## <a name="resolution"></a>Решение

Чтобы решить эту проблему, запустите VM в аварийном режиме, используя серийную консоль для виртуальных машин Azure. Затем используйте инструмент для восстановления файловой системы. Если серийная консоль не включена на вашем VM, перейдите в [раздел Repair the VM в автономном режиме.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Использование последовательной консоли

### <a name="using-single-user-mode"></a>Использование единого пользовательского режима

1. Подключение к [серийной консоли](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Используйте серийную консоль для использования [единого пользовательского режима в едином пользовательском режиме](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
3. После того, как vm загрузился в единый пользовательский режим. Используйте свой любимый текстовый редактор, чтобы открыть файл fstab. 

   ```
   # nano /etc/fstab
   ```

4. Просмотрите перечисленные файловые системы. Каждая строка в файле fstab указывает на файловую систему, которая устанавливается при запуске VM. Для получения дополнительной информации о синтаксисе файла fstab запустите команду fstab. Чтобы устранить сбой запуска, просмотрите каждую строку, чтобы убедиться, что она верна как по структуре, так и по содержанию.

   > [!Note]
   > * Поля на каждой строке разделены вкладками или пробелами. Пустые строки не учитываются. Строки с знаком числа (яп. ) в качестве первого символа являются комментариями. Комментарии могут оставаться в файле fstab, но они не будут обработаны. Мы рекомендуем комментировать строки fstab, в которые вы не уверены, вместо того, чтобы удалять строки.
   > * Для восстановления и запуска VM разделы файловой системы должны быть единственными необходимыми разделами. VM может испытывать ошибки приложения о дополнительных прокомментированных разделах. Тем не менее, VM должен начаться без дополнительных перегородок. Вы можете позже uncomment любые комментарии строк.
   > * Мы рекомендуем устанавливать диски данных на vMs Azure с помощью UUID раздела файловой системы. Например, запустите следующую команду:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Чтобы определить UUID файловой системы, запустите команду blkid. Для получения дополнительной информации о синтаксисе, запустите команду человека blkid.
   > * Опция nofail помогает убедиться, что VM запускается, даже если файловая система повреждена или файловая система не существует при запуске. Мы рекомендуем использовать опцию nofail в файле fstab, чтобы запуск продолжался после ошибок в разделах, которые не требуются для запуска VM.

5. Измените или прокомментируйте любые неправильные или ненужные строки в файле fstab, чтобы VM мог правильно начать.

6. Сохраните изменения в файле fstab.

7. Перезагрузите vm с помощью нижей команды.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Вы также можете использовать команду "ctrl'x", которая также перезагрузит vm.


8. Если комментарий или исправление записей был успешным, система должна достичь баш запрос на портале. Проверьте, можно ли подключиться к VM.

### <a name="using-root-password"></a>Использование корневого пароля

1. Подключение к [серийной консоли](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Войти в систему с помощью локального пользователя и пароля.

   > [!Note]
   > Вы не можете использовать ключ SSH для вху-сивса в систему в серийной консоли.

3. Ищите ошибку, которая указывает на то, что диск не был установлен. В следующем примере система пытается прикрепить диск, которого больше нет:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Подключитесь к VM с помощью корневого пароля (Красные Чаты на основе VMs).

5. Используйте свой любимый текстовый редактор, чтобы открыть файл fstab. После установки диска запустите следующую команду для Nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Просмотрите перечисленные файловые системы. Каждая строка в файле fstab указывает на файловую систему, которая устанавливается при запуске VM. Для получения дополнительной информации о синтаксисе файла fstab запустите команду fstab. Чтобы устранить сбой запуска, просмотрите каждую строку, чтобы убедиться, что она верна как по структуре, так и по содержанию.

   > [!Note]
   > * Поля на каждой строке разделены вкладками или пробелами. Пустые строки не учитываются. Строки с знаком числа (яп. ) в качестве первого символа являются комментариями. Комментарии могут оставаться в файле fstab, но они не будут обработаны. Мы рекомендуем комментировать строки fstab, в которые вы не уверены, вместо того, чтобы удалять строки.
   > * Для восстановления и запуска VM разделы файловой системы должны быть единственными необходимыми разделами. VM может испытывать ошибки приложения о дополнительных прокомментированных разделах. Тем не менее, VM должен начаться без дополнительных перегородок. Вы можете позже uncomment любые комментарии строк.
   > * Мы рекомендуем устанавливать диски данных на vMs Azure с помощью UUID раздела файловой системы. Например, запустите следующую команду:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Чтобы определить UUID файловой системы, запустите команду blkid. Для получения дополнительной информации о синтаксисе, запустите команду человека blkid.
   > * Опция nofail помогает убедиться, что VM запускается, даже если файловая система повреждена или файловая система не существует при запуске. Мы рекомендуем использовать опцию nofail в файле fstab, чтобы запуск продолжался после ошибок в разделах, которые не требуются для запуска VM.

7. Измените или прокомментируйте любые неправильные или ненужные строки в файле fstab, чтобы VM мог правильно начать.

8. Сохраните изменения в файле fstab.

9. Перезапустите виртуальную машину.

10. Если комментарий или исправление записей был успешным, система должна достичь баш запрос на портале. Проверьте, можно ли подключиться к VM.

11. Проверьте баллы крепления, когда вы тестируете любое изменение fstab, запустив команду крепления- Если нет ошибок, то очки крепления должны быть хорошими.

## <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

1. Прикрепите системный диск VM в качестве диска данных к восстановлению VM (любой рабочий Linux VM). Для этого можно использовать [команды CLI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) или автоматизировать настройку VM-восстановления с помощью [команд по ремонту VM.](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

2. После установки системного диска в качестве диска данных на рекуперации VM, резервное копирование файла fstab, прежде чем вносить изменения, а затем затем выполнить следующие шаги, чтобы исправить fstab файл.

3.    Ищите ошибку, которая указывает на то, что диск не был установлен. В следующем примере система пытается прикрепить диск, которого больше нет:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Подключитесь к VM с помощью корневого пароля (Красные Чаты на основе VMs).

5. Используйте свой любимый текстовый редактор, чтобы открыть файл fstab. После установки диска запустите следующую команду для Nano. Убедитесь, что вы работаете над файлом fstab, который находится на установленном диске, а не файлом fstab, который находится на спасательном VM.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Просмотрите перечисленные файловые системы. Каждая строка в файле fstab указывает на файловую систему, которая устанавливается при запуске VM. Для получения дополнительной информации о синтаксисе файла fstab запустите команду fstab. Чтобы устранить сбой запуска, просмотрите каждую строку, чтобы убедиться, что она верна как по структуре, так и по содержанию.

   > [!Note]
   > * Поля на каждой строке разделены вкладками или пробелами. Пустые строки не учитываются. Строки с знаком числа (яп. ) в качестве первого символа являются комментариями. Комментарии могут оставаться в файле fstab, но они не будут обработаны. Мы рекомендуем комментировать строки fstab, в которые вы не уверены, вместо того, чтобы удалять строки.
   > * Для восстановления и запуска VM разделы файловой системы должны быть единственными необходимыми разделами. VM может испытывать ошибки приложения о дополнительных прокомментированных разделах. Тем не менее, VM должен начаться без дополнительных перегородок. Вы можете позже uncomment любые комментарии строк.
   > * Мы рекомендуем устанавливать диски данных на vMs Azure с помощью UUID раздела файловой системы. Например, запустите следующую команду:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Чтобы определить UUID файловой системы, запустите команду blkid. Для получения дополнительной информации о синтаксисе, запустите команду человека blkid. Обратите внимание, что диск, который вы хотите восстановить, теперь установлен на новом VM. Хотя UUIDs должны быть последовательными, ит-икс раздела устройства (например, "/dev/sda1") отличаются на этом VM. Разделы файловой системы исходного сбой VM, которые расположены на несистемной VHD не доступны для восстановления VM [с помощью команд CLI.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)
   > * Опция nofail помогает убедиться, что VM запускается, даже если файловая система повреждена или файловая система не существует при запуске. Мы рекомендуем использовать опцию nofail в файле fstab, чтобы запуск продолжался после ошибок в разделах, которые не требуются для запуска VM.

7. Измените или прокомментируйте любые неправильные или ненужные строки в файле fstab, чтобы VM мог правильно начать.

8. Сохраните изменения в файле fstab.

9. Перезапустите виртуальную машину или перестроить оригинальный VM.

10. Если комментарий или исправление записей был успешным, система должна достичь баш запрос на портале. Проверьте, можно ли подключиться к VM.

11. Проверьте баллы крепления, когда вы тестируете любое изменение fstab, запустив команду крепления- Если нет ошибок, то очки крепления должны быть хорошими.

12. Открепите и отсоедините оригинальный виртуальный жесткий диск, а затем создайте VM с исходного системного диска. Для этого можно использовать [команды CLI](troubleshoot-recovery-disks-linux.md) или [команды по ремонту VM,](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) если вы использовали их для создания восстановления VM.

13. После того как вы создадите VM снова, и вы можете подключиться к нему через SSH, принять следующие действия:
    * Просмотрите любую из линий fstab, которые были изменены или прокомментированы во время восстановления.
    * Убедитесь, что вы используете UUID и опцию nofail надлежащим образом.
    * Проверьте любые изменения fstab перед перезапуском VM. Для этого используйте следующую команду:``$ sudo mount -a``
    * Создайте дополнительную копию исправленного файла fstab для использования в будущих сценариях восстановления.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок виртуальной машины Linux путем подключения диска ОС к виртуальной машине восстановления с помощью Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Устранение неполадок с виртуальной машиной Linux при присоединении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

