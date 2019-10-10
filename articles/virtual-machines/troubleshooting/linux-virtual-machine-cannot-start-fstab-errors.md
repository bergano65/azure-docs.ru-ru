---
title: Устранение проблем с запуском виртуальных машин Linux из-за ошибок fstab | Документация Майкрософт
description: В этой статье объясняется, почему не удается запустить виртуальную машину Linux и как решить проблему.
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
ms.openlocfilehash: 868a0238092786d0999a6a41de71d30011bbef7a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245348"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Устранение проблем с запуском виртуальных машин Linux из-за ошибок fstab

Вы не можете подключиться к виртуальной машине Azure Linux с помощью подключения Secure Shell (SSH). При запуске функции [диагностики загрузки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) на [портал Azure](https://portal.azure.com/)отображаются записи журнала, аналогичные следующим примерам.

## <a name="examples"></a>Примеры

Ниже приведены примеры возможных ошибок.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Пример 1 Диск монтируется с помощью идентификатора SCSI, а не универсального уникального идентификатора (UUID).

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type “journalctl -xb” to viewsystem logs, “systemctl reboot” to reboot, “systemctl default” to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Пример 2 В CentOS отсутствует неприсоединенное устройство

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device (“nofail” fstab option may be used to skip this device)
/dev/sdd1: nonexistent device (“nofail” fstab option may be used to skip this device)
/dev/sde1: nonexistent device (“nofail” fstab option may be used to skip this device)

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

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Пример 3 Не удается запустить виртуальную машину из-за неfstabй конфигурации или из-за того, что диск больше не подключен

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Пример 4. Последовательная запись журнала показывает Неверный UUID

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
*** Run ‘setenforce 1’ to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Эта проблема может возникать, если синтаксис таблицы файловых систем (fstab) неверен или если необходимый диск данных, сопоставленный с записью в файле "/etc/fstab", не подключен к виртуальной машине.

## <a name="resolution"></a>Способы устранения:

Чтобы устранить эту проблему, запустите виртуальную машину в аварийном режиме с помощью последовательной консоли для виртуальных машин Azure. Затем с помощью средства восстановите файловую систему. Если последовательная консоль не включена на виртуальной машине, перейдите к разделу [Восстановление виртуальной машины в автономном режиме](#repair-the-vm-offline) .

## <a name="use-the-serial-console"></a>Использование последовательной консоли

1. Подключитесь к [последовательной консоли](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Войдите в систему, используя локальный пользователь и пароль.

   > [!Note]
   > Вы не можете использовать ключ SSH для входа в систему в последовательной консоли.

3. Найдите ошибку, указывающую на то, что диск не был подключен. В следующем примере система представила попытку подключить диск, который больше не находился:

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

4. Подключитесь к виртуальной машине с помощью пароля root (виртуальных машин на основе Red Hat).

5. Откройте файл fstab с помощью любого текстового редактора. После подключения диска выполните следующую команду для Nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Проверьте перечисленные файловые системы. Каждая строка в файле fstab указывает на файловую систему, которая монтируется при запуске виртуальной машины. Чтобы получить дополнительные сведения о синтаксисе файла fstab, выполните команду man fstab. Чтобы устранить неполадки с запуском, проверьте каждую строку и убедитесь, что она правильная в структуре и содержимом.

   > [!Note]
   > * Поля в каждой строке разделяются символами табуляции или пробелами. Пустые строки игнорируются. Строки, имеющие знак решетки (#) в качестве первого символа, являются комментариями. Строки с комментариями могут остаться в файле fstab, но они не будут обработаны. Вместо удаления строк рекомендуется закомментировать fstab строки, о которых вы не уверены.
   > * Для восстановления и запуска виртуальной машины разделы файловой системы должны быть единственными необходимыми секциями. Виртуальная машина может столкнуться с ошибками приложений о дополнительных секциях с комментариями. Однако виртуальная машина должна запускаться без дополнительных секций. В дальнейшем можно раскомментировать все строки с комментариями.
   > * Мы рекомендуем подключать диски данных на виртуальных машинах Azure, используя UUID раздела файловой системы. Например, выполните следующую команду: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``.
   > * Чтобы определить UUID файловой системы, выполните команду blkid. Чтобы получить дополнительные сведения о синтаксисе, выполните команду man blkid.
   > * Параметр With позволяет гарантировать, что виртуальная машина запускается даже в том случае, если файловая система повреждена или файловая система не существует при запуске. Рекомендуется использовать параметр fstab в файле с целью включения запуска после ошибок в секциях, которые не требуются для запуска виртуальной машины.

7. Измените или закомментируйте любые неправильные или ненужные строки в файле fstab, чтобы обеспечить правильную загрузку виртуальной машины.

8. Сохраните изменения в файле fstab.

9. Перезапустите виртуальную машину.

10. Если комментарий или исправление записей были успешными, система должна связаться с запросом bash на портале. Проверьте, можно ли подключиться к виртуальной машине.

11. Проверьте точки подключения при тестировании любых изменений fstab, выполнив команду mount-a. Если ошибок нет, точки подключения должны быть приемлемыми.

## <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

1. Подключите системный диск виртуальной машины к ВИРТУАЛЬНОЙ машине восстановления (любой рабочей виртуальной машине Linux) в качестве диска данных. Для этого можно использовать [команды интерфейса командной строки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) или автоматизировать настройку виртуальной машины восстановления с помощью [команд восстановления виртуальной машины](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. После подключения системного диска в качестве диска данных на виртуальной машине восстановления создайте резервную копию файла fstab перед внесением изменений, а затем выполните следующие действия, чтобы исправить файл fstab.

3.  Найдите ошибку, указывающую на то, что диск не был подключен. В следующем примере система представила попытку подключить диск, который больше не находился:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Подключитесь к виртуальной машине с помощью пароля root (виртуальных машин на основе Red Hat).

5. Откройте файл fstab с помощью любого текстового редактора. После подключения диска выполните следующую команду для Nano. Убедитесь, что вы работаете с файлом fstab, расположенным на подключенном диске, а не с файлом fstab, который находится на виртуальной машине.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Проверьте перечисленные файловые системы. Каждая строка в файле fstab указывает на файловую систему, которая монтируется при запуске виртуальной машины. Чтобы получить дополнительные сведения о синтаксисе файла fstab, выполните команду man fstab. Чтобы устранить неполадки с запуском, проверьте каждую строку и убедитесь, что она правильная в структуре и содержимом.

   > [!Note]
   > * Поля в каждой строке разделяются символами табуляции или пробелами. Пустые строки игнорируются. Строки, имеющие знак решетки (#) в качестве первого символа, являются комментариями. Строки с комментариями могут остаться в файле fstab, но они не будут обработаны. Вместо удаления строк рекомендуется закомментировать fstab строки, о которых вы не уверены.
   > * Для восстановления и запуска виртуальной машины разделы файловой системы должны быть единственными необходимыми секциями. Виртуальная машина может столкнуться с ошибками приложений о дополнительных секциях с комментариями. Однако виртуальная машина должна запускаться без дополнительных секций. В дальнейшем можно раскомментировать все строки с комментариями.
   > * Мы рекомендуем подключать диски данных на виртуальных машинах Azure, используя UUID раздела файловой системы. Например, выполните следующую команду: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``.
   > * Чтобы определить UUID файловой системы, выполните команду blkid. Чтобы получить дополнительные сведения о синтаксисе, выполните команду man blkid. Обратите внимание, что диск, который вы хотите восстановить, теперь подключен к новой виртуальной машине. Хотя UUID должны быть одинаковыми, идентификаторы разделов устройств (например, "/dev/sda1") отличаются на этой виртуальной машине. Разделы файловой системы исходной виртуальной машины, на которой не установлен системный виртуальный жесткий диск, недоступны для виртуальной машины восстановления [с помощью команд интерфейса командной строки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux).
   > * Параметр With позволяет гарантировать, что виртуальная машина запускается даже в том случае, если файловая система повреждена или файловая система не существует при запуске. Рекомендуется использовать параметр fstab в файле с целью включения запуска после ошибок в секциях, которые не требуются для запуска виртуальной машины.

7. Измените или закомментируйте любые неправильные или ненужные строки в файле fstab, чтобы обеспечить правильную загрузку виртуальной машины.

8. Сохраните изменения в файле fstab.

9. Перезапустите виртуальную машину или перестройте исходную виртуальную машину.

10. Если комментарий или исправление записей были успешными, система должна связаться с запросом bash на портале. Проверьте, можно ли подключиться к виртуальной машине.

11. Проверьте точки подключения при тестировании любых изменений fstab, выполнив команду mount-a. Если ошибок нет, точки подключения должны быть приемлемыми.

12. Отключите и отсоедините исходный виртуальный жесткий диск, а затем создайте виртуальную машину на основе исходного системного диска. Для этого можно использовать [команды CLI](troubleshoot-recovery-disks-linux.md) или [команды восстановления виртуальной машины](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , если они ИСПОЛЬЗОВАЛИСЬ для создания виртуальной машины восстановления.

13. После повторного создания виртуальной машины и подключения к ней через SSH выполните следующие действия.
    * Проверьте все строки fstab, которые были изменены или добавлены в комментарий во время восстановления.
    * Убедитесь, что вы используете UUID и параметр "не пройден".
    * Протестируйте все изменения fstab, прежде чем перезапускать виртуальную машину. Для этого используйте следующую команду: ``$ sudo mount -a``.
    * Создайте дополнительную копию исправленного файла fstab для использования в будущих сценариях восстановления.

## <a name="next-steps"></a>Следующие шаги

* [Устранение неполадок виртуальной машины Linux путем подключения диска ОС к виртуальной машине восстановления с помощью Azure CLI 2,0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Устранение неполадок виртуальной машины Linux путем подключения диска ОС к виртуальной машине восстановления с помощью портал Azure](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

