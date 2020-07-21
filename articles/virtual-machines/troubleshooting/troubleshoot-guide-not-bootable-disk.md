---
title: Ошибка загрузки — "это не загрузочный диск"
description: В этой статье приведены инструкции по устранению проблем, когда диск не является загрузочным в виртуальной машине Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 16f6919577955bda5b04db26deb9fe78a467e364
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509041"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Ошибка загрузки — это не загрузочный диск

В этой статье приведены инструкции по устранению проблем, когда диск не является загрузочным для виртуальной машины Azure.

## <a name="symptoms"></a>Симптомы

При использовании [диагностики загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается сообщение "это не загрузочный диск. Вставьте загрузочный гибкий диск и нажмите любую клавишу, чтобы повторить попытку... ".

   На рисунке 1

   ![На рис. 1 показано сообщение * "это не загрузочный диск. Вставьте загрузочный гибкий диск и нажмите любую клавишу, чтобы повторить попытку... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Причина

Это сообщение об ошибке означает, что процессу загрузки ОС не удалось выполнить обнаружение активного системного раздела. Эта ошибка также может означать, что в хранилище данные конфигурации загрузки (BCD) отсутствует ссылка, и она не позволяет найти раздел Windows.

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Обзор процесса

1. Создайте виртуальную машину для восстановления и войдите на нее.
2. Задайте для секции состояние активно.
3. Исправьте раздел диска.
4. **Рекомендуется**. Перед перестроением виртуальной машины включите серийную консоль и сбор дампов памяти.
5. Перестройте исходную виртуальную машину.

   > [!NOTE]
   > При возникновении этой ошибки загрузки гостевая ОС не работает. Для устранения неполадок потребуется применить автономный режим.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и получение доступа к ней

1. Выполните шаги 1–3 из списка [команд для восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md), чтобы подготовить виртуальную машину для восстановления.
2. Используйте подключение к удаленному рабочему столу, чтобы подключиться к виртуальной машине для восстановления.

### <a name="set-partition-status-to-active"></a>Установить состояние секции "активный"

Виртуальные машины поколения 1 должны сначала проверить, что раздел ОС, в котором находится хранилище BCD, помечен как *Активный*. Если у вас есть виртуальная машина поколения 2, переходите к [исправлению раздела диска](#fix-the-disk-partition), так как флаг *состояния* устарел в последующем поколении.

1. Откройте командную строку с повышенными привилегиями *(cmd.exe)*.
2. Введите команду *diskpart*, чтобы запустить средство DISKPART.
3. Введите *List Disk* , чтобы вывести список дисков в системе и указать подключенный виртуальный жесткий диск ОС.
4. Когда вы найдете подключенный виртуальный жесткий диск ОС, введите команду *sel disk #* , чтобы выбрать диск.  См. рис. 2, где Disk 1 — подключенный виртуальный жесткий диск ОС.

   Рис. 2

   ![На рис. 2 показано окно "DISKPART *" с выводом команды "list disk", "Disk 0" и "Disk 1", отображаемых в таблице.  Также отображает выходные данные команды SEL Disk 1, диск 1 — выбранный диск.](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. После выбора диска введите *list partition*, чтобы получить список разделов выбранного диска.
6. Выяснив номер загрузочного раздела, введите команду *sel partition #* , чтобы выбрать этот раздел.  Обычно размер загрузочного раздела составляет около 350 МБ.  См. рис. 3, где Partition 1 — загрузочный раздел.

   Рис. 3

   ![На рис. 3 показано окно * DISKPART * с выходными данными команды * list partition *. Секции 1 и 2 отображаются в таблице. Также отображаются выходные данные команды * SEL Partition 1 *, если выбран диск Partition 1.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Введите "Detail Partition", чтобы проверить состояние секции. См. рис. 4, где Секция *активна: нет*или рис. 5, где раздел имеет значение "Active: Yes".

   Рис. 4

   ![На рис. 4 показано окно программы DISKPART * с выходными данными команды * Detail Partition *, если для секции 1 задано значение * Active: No *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Рис. 5

   ![На рис. 5 показано окно * DISKPART * с выходными данными команды * Detail Partition *, если для раздела 1 задано значение * Active: Yes *.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Если раздел **неактивен**, введите *Активный* , чтобы изменить флаг *активного* .
9. Убедитесь, что изменение состояния было выполнено правильно, введя *подробное секционирование*.

   Рис. 6

   ![На рис. 6 показано окно DiskPart с выходными данными команды * Detail Partition *, если для раздела 1 задано значение * Active: Да *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Введите *exit*, чтобы закрыть средство diskpart и сохранить изменения конфигурации.

### <a name="fix-the-disk-partition"></a>Исправление раздела диска

1. Откройте командную строку с повышенными привилегиями (cmd.exe).
2. Используйте следующую команду для запуска *программы chkdsk* на дисках и исправления ошибок:

   `chkdsk <DRIVE LETTER>: /f`

   При добавлении команды "/f" исправляются все ошибки на диске. Обязательно замените на <DRIVE LETTER> букву подключенного виртуального жесткого диска ОС.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Рекомендуется: перед перестроением виртуальной машины включите сбор последовательной консоли и дампа памяти.

Чтобы включить сбор дампов памяти и последовательную консоль, выполните следующий скрипт:

1. Откройте сеанс командной строки с повышенными привилегиями (запуск от имени администратора).
2. Выполните следующие команды:

   Включить последовательную консоль

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Убедитесь, что объем свободного места на диске ОС совпадает с объемом памяти (ОЗУ) виртуальной машины.

   Если на диске операционной системы недостаточно места, следует изменить расположение файла дампа памяти и сослаться на любой диск данных, подключенный к виртуальной машине, имеющей достаточно свободного места. Чтобы изменить расположение, замените "% SystemRoot%" буквой диска (например, "F:") диска данных в приведенных ниже командах.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Рекомендуемая конфигурация для включения дампа ОС

**Загрузка неработающего диска ОС**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Включите сбор для ControlSet001.**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Включите сбор для ControlSet002.**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Выгрузите поврежденный диск ОС.**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Перестроение исходной виртуальной машины

Чтобы заново собрать виртуальную машину, выполните [шаг 5 из списка команд для восстановления ВМ](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example).
