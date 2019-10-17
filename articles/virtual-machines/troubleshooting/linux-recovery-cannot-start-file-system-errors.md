---
title: Устранение неполадок, возникающих при запуске виртуальной машины Linux из-за ошибок файловой системы | Документация Майкрософт
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
ms.openlocfilehash: a47dc1032115f8bcae0c7bdc37c84ab3b68ec4a8
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432305"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Устранение неполадок, возникающих при запуске виртуальной машины Linux из-за ошибок файловой системы

Вы не можете подключиться к виртуальной машине Azure Linux с помощью Secure Shell (SSH). При запуске функции диагностики загрузки на [портал Azure](https://portal.azure.com/)отображаются записи журнала, аналогичные следующим примерам.

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

Этот пример вызван очисткой с помощью команды fsck. В этом случае к виртуальной машине также подключены дополнительные диски данных (/dev/sdc1 и/dev/SDE1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Эта проблема может возникать, если файловая система не завершила работу в чистом виде или проблемы с хранилищем. Проблемы включают в себя ошибки оборудования или программного обеспечения, проблемы с драйверами или программами, ошибки записи и т. д. Всегда важно иметь резервную копию критически важных данных. Средства, описанные в этой статье, могут помочь в восстановлении файловых систем, но при этом могут возникать потери данных.

В Linux доступно несколько проверок файловой системы. Наиболее распространенным для дистрибутивов в Azure являются: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)и [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Разрешение

Чтобы устранить эту проблему, загрузите виртуальную машину в аварийный режим с помощью [последовательной консоли](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) и используйте это средство для восстановления файловой системы. Если последовательная консоль не включена на виртуальной машине или не работает, см. раздел [Восстановление виртуальной машины в автономном режиме](#repair-the-vm-offline) этой статьи.

## <a name="use-the-serial-console"></a>Использование последовательной консоли

1. Подключение к последовательной консоли

   > [!Note]
   > Дополнительные сведения об использовании последовательной консоли для Linux см. в следующих статьях:
   > * [Использование последовательной консоли для доступа к GRUB и однопользовательским режимам](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Использование последовательной консоли для вызовов Сисрк и NMI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Нажмите кнопку с индикатором питания, а затем выберите перезапустить виртуальную машину. (Если последовательная консоль не включена или не подключена успешно, кнопка не отображается.)

   ![Эскиз](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Загрузите виртуальную машину в аварийном режиме.

4. Введите пароль для учетной записи root, чтобы войти в аварийный режим.

5. Используйте xfs_repair с параметром-n для обнаружения ошибок в файловой системе. В следующем примере предполагается, что системным разделом является/dev/sda1. Замените его соответствующим значением для виртуальной машины:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Выполните следующую команду, чтобы восстановить файловую систему:

   ```
   xfs_repair /dev/sda1
   ```

7. При появлении сообщения об ошибке "ошибка: файловая система содержит ценные изменения метаданных в журнале, который необходимо воспроизвести", создайте временный каталог и подключите файловую систему:

   ```
   mkdir /temp
   mount /dev/sda2 /temp
   ```

8. Если не удается подключить диск, выполните команду xfs_repair с параметром-L (Принудительное обнуление журнала):

   ```
   xfs_repair /dev/sda2 -L
   ```

9. Затем попытайтесь подключить файловую систему. Если диск подключен успешно, вы получите следующие выходные данные:
 
   ```
   XFS (sda2): Mounting V1 Filesystem
   XFS (sda2): Ending clean mount
   ```

10. Перезапустите виртуальную машину, а затем проверьте, устранена ли проблема.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

1. Подключите системный диск виртуальной машины к ВИРТУАЛЬНОЙ машине восстановления (любой рабочей виртуальной машине Linux) в качестве диска данных. Для этого можно использовать [команды интерфейса командной строки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) или автоматизировать настройку виртуальной машины восстановления с помощью [команд восстановления виртуальной машины](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Выделите метку диска подключенного системного диска. В этом случае предполагается, что метка системного диска, к которой вы присоединяетесь, —/dev/sdc1. Замените его соответствующим значением для виртуальной машины.

3. Используйте xfs_repair с параметром-n для обнаружения ошибок в файловой системе.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Выполните следующую команду, чтобы восстановить файловую систему:

   ```
   xfs_repair /dev/sdc1
   ```

5. При появлении сообщения об ошибке "ошибка: файловая система содержит ценные изменения метаданных в журнале, который необходимо воспроизвести", создайте временный каталог и подключите файловую систему:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Если не удается подключить диск, выполните команду xfs_repair с параметром-L (Принудительное обнуление журнала):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Затем попытайтесь подключить файловую систему. Если диск подключен успешно, вы получите следующие выходные данные:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Отключите и отсоедините исходный виртуальный жесткий диск, а затем создайте виртуальную машину на основе исходного системного диска. Для этого можно использовать [команды CLI](troubleshoot-recovery-disks-linux.md) или [команды восстановления виртуальной машины](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , если они ИСПОЛЬЗОВАЛИСЬ для создания виртуальной машины восстановления.

8. Проверьте, устранена ли проблема.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок виртуальной машины Linux путем подключения диска ОС к виртуальной машине восстановления с помощью Azure CLI 2,0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Подключение диска данных к виртуальной машине Linux с помощью портала](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

