---
title: Устранение неполадок при запуске ОС, связанных с нехваткой места для установки в Центре обновления Windows
description: Действия по устранению проблем, из-за которых Центр обновления Windows на виртуальной машине Azure возвращает ошибку и перестает отвечать.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: f83a1820eb931fa075681da7a9661b304059cd2a
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635711"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Устранение неполадок при запуске ОС, связанных с нехваткой места для установки в Центре обновления Windows

В этой статье приведены инструкции по устранению проблем на виртуальной машине Azure, из-за которых Центр обновления Windows возвращает ошибку и перестает отвечать.

## <a name="symptom"></a>Симптом

При использовании диагностики загрузки для просмотра снимка экрана виртуальной машины вы видите сообщения о том, что Центр обновления Windows выполняет работу, а затем завершается ошибкой со следующим кодом: **C01A001D**. На следующем изображении Центр обновления Windows уже перестал реагировать на запросы и отображает сообщение “Error C01A001D applying update operation ##### of ##### (######)” (Ошибка C01A001D при применении операции обновления):

![Центр обновления Windows не отвечает и отображает сообщение об ошибке C01A001D при применении операции обновления.](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Причина

В нашем примере операционная система (ОС) не может завершить установку Центра обновления Windows из-за того, что не удается создать файл дампа в файловой системе. Судя по коду этой ошибки, операционная система вообще не может записывать файлы на диск.

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Общие сведения о процессе.

1. Создайте виртуальную машину для восстановления и войдите на нее.
1. Освободите место на диске.
1. Включите последовательную консоль и сбор дампов памяти.
1. Перестройте виртуальную машину.

> [!NOTE]
> При возникновении такой ошибки гостевая ОС не работает. Для устранения этой проблемы воспользуйтесь автономным режимом.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и вход на нее

1. Выполните шаги 1–3 из списка [команд для восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md), чтобы подготовить виртуальную машину для восстановления.
1. Подключитесь к этой виртуальной машине для восстановления по протоколу удаленного рабочего стола.

### <a name="free-up-space-on-the-disk"></a>Освобождение места на диске

Чтобы устранить проблему, сделайте следующее:

- Измените размер диска до 1 ТБ, если его размер еще не достиг лимита в 1 ТБ.
- Очистите диск.
- Дефрагментируйте диск.

1. Проверьте, есть ли свободное место на диске. Если размер диска менее 1 ТБ, увеличьте его до максимального возможного размера 1 ТБ [с помощью PowerShell](../windows/expand-os-disk.md).
1. Если размер диска уже равен 1 ТБ, придется очистить диск.
   1. Освободите место с помощью [средства очистки диска](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup).
1. Когда изменение размера и (или) очистка завершатся, дефрагментируйте диск с помощью следующей команды:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
В зависимости от уровня фрагментации этот процесс может занять до нескольких часов.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Включение последовательной консоли и сбора дампов памяти

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

   Рекомендуемая конфигурация для включения дампа ОС:

    **Загрузка неработающего диска ОС** :

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Включение в ControlSet001** :

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Включение в ControlSet002** :

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Выгрузка неработающего диска ОС** :

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Перестроение виртуальной машины

Чтобы перестроить виртуальную машину, [выполните шаг 5 из списка команд для восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example).
