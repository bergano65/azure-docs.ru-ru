---
title: Устранение неполадок при ошибке загрузки "Произошла ошибка чтения диска"
description: В этой статье приведены инструкции по устранению проблем, из-за которых диск не доступен для чтения из виртуальной машины Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: 75d1cf8638f922bb0275322568eb1399db4f49e8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629731"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Устранение неполадок при ошибке загрузки "Произошла ошибка чтения диска"

В этой статье приведены инструкции по устранению проблем, из-за которых диск недоступен для чтения из виртуальной машины Azure.

## <a name="symptoms"></a>Симптомы

При использовании [диагностики загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины вы увидите, что отображается следующее сообщение: "A disk read error occurred. Press Ctrl+Alt+Del to restart" (Операционная система не найдена. Попробуйте отключить драйверы, которые не содержат операционную систему. Нажмите клавиши CTRL+ALT+DEL для перезапуска).

   ![Сообщение об ошибке: Произошла ошибка чтения диска. Нажмите клавиши CTRL+ALT+DEL для перезапуска.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Причина

Это сообщение об ошибке означает, что структура диска повреждена и недоступна для чтения. Если вы используете виртуальную машину 1-го поколения, возможно, раздел диска с данными конфигурации загрузки не находится в состоянии **Active** (Активно).

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Общие сведения о процессе

> [!TIP]
> Если у вас есть недавняя резервная копия виртуальной машины, можно попытаться [восстановить виртуальную машину из резервной копии](../../backup/backup-azure-arm-restore-vms.md) , чтобы устранить проблему загрузки.

1. Создайте виртуальную машину для восстановления и войдите на нее.
1. Выберите решение:
   - [перевод раздела в состояние "Active" (Активно);](#set-partition-status-to-active)
   - [исправление раздела диска.](#fix-the-disk-partition)
1. Включите последовательную консоль и сбор дампов памяти.
1. Перестройте виртуальную машину.

> [!NOTE]
> Если возникает эта ошибки загрузки, значит гостевая ОС не работает. Для устранения неполадок потребуется применить автономный режим.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и вход на нее

1. Выполните шаги 1–3 из списка [команд для восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md), чтобы подготовить виртуальную машину для восстановления.
1. Используйте подключение к удаленному рабочему столу, чтобы подключиться к виртуальной машине для восстановления.

### <a name="set-partition-status-to-active"></a>Перевод раздела в состояние "Active" (Активно)

Виртуальные машины 1-го поколения должны сначала проверить, что раздел ОС с хранилищем для данных конфигурации загрузки, имеет состояние **Active** (Активно). Если у вас виртуальная машина 2-го поколения, перейдите к разделу [Исправление раздела диска](#fix-the-disk-partition), так как флаг состояния в этом поколении был объявлен устаревшим.

1. Откройте командную строку с повышенными привилегиями (cmd.exe).
1. Введите команду **diskpart**, чтобы запустить средство **DISKPART**.
1. Введите **list disk**, чтобы получить список дисков в системе и найти среди них подключенный виртуальный жесткий диск (VHD) для операционной системы.
1. Когда вы найдете подключенный виртуальный жесткий диск ОС, введите команду **sel disk #** , чтобы выбрать диск. На следующем рисунке показан пример, где подключенным виртуальным жестким диском ОС является "Disk 1".

   ![Окно diskpart с выходными данными команды **list disk**, где в таблице отображаются Disk 0 и Disk 1. В окне также показаны выходные данные команды **sel disk 1**, где Disk 1 назначен выбранным диском.](./media/disk-read-error-occurred/2.png)

1. После выбора диска введите **list partition**, чтобы получить список разделов выбранного диска.
1. Выяснив номер загрузочного раздела, введите команду **sel partition #** , чтобы выбрать этот раздел. Размер загрузочного раздела обычно составляет около 350 МБ.  На следующем рисунке показан пример, в котором Partition 1 — это загрузочный раздел.

   ![Окно diskpart с выходными данными команды **list partition**, где в таблице отображаются разделы Partition 1 и Partition 2. В окне также показаны выходные данные команды **sel partition 1**, где Partition 1 назначен выбранным разделом.](./media/disk-read-error-occurred/3.png)

1. Введите **detail partition**, чтобы проверить состояние раздела. На следующих снимках экрана вы видите примеры разделов, для которых заданы значения **Active: No** или **Active: Yes**.

   **Active: No**

   ![Окно diskpart с выходными данными команды **detail partition**, где для раздела 1 задано значение **Active: No**.](./media/disk-read-error-occurred/4.png)

   **Active: Yes**

   ![Окно diskpart с выходными данными команды **detail partition**, где для раздела 1 задано значение **Active: Yes**.](./media/disk-read-error-occurred/5.png)

1. Если для раздела не задано значение **Active**, введите команду **active**, чтобы изменить значение флага Active.
1. Введите **detail partition**, чтобы проверить правильность изменения состояния, то есть убедитесь, что выходные данные содержат значение **Active: Yes**. 
1. Введите **exit**, чтобы закрыть средство diskpart и сохранить изменения конфигурации.

### <a name="fix-the-disk-partition"></a>Исправление раздела диска

1. Откройте командную строку с повышенными привилегиями (cmd.exe).
1. Выполните следующую команду, чтобы запустить **CHKDSK** для дисков и выполнить исправления ошибок:

   `chkdsk <DRIVE LETTER>: /f`

   Если вы добавите в команду параметр **/f**, на диске будут исправлены все ошибки. Не забудьте заменить значение **< DRIVE LETTER >** буквой подключенного виртуального жесткого диска ОС.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Включение последовательной консоли и сбор дампов памяти

**Рекомендуется**. Прежде чем перестраивать виртуальную машину, включите последовательную консоль и сбор дампов памяти. Для этого выполните следующий скрипт:

1. Откройте сеанс командной строки с повышенными привилегиями от имени администратора.
1. Выполните следующие команды:

   **Включите последовательную консоль.**
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Убедитесь, что размер свободного места на диске ОС превышает объем памяти (ОЗУ) этой виртуальной машины.

   Если места на диске ОС недостаточно, измените расположение для создания файла дампа памяти, указав подключенный к виртуальной машине диск данных, на котором достаточно свободного места. Чтобы изменить расположение, замените значение **%SystemRoot%** буквой нужного диска данных (например, **F:** ) в приведенных ниже командах.

   Рекомендуемая конфигурация для включения дампа ОС.

   **Загрузите куст реестра с поврежденного диска ОС.**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Включите сбор для ControlSet001.**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Включите сбор для ControlSet002.**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Выгрузите поврежденный диск ОС.**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Перестроение виртуальной машины

Чтобы перестроить виртуальную машину, [выполните шаг 5 из списка команд для восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example).
