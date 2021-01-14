---
title: Виртуальная машина Azure не отвечает с ошибкой C01A001D при применении Центр обновления Windows
description: В этой статье приводятся инструкции по устранению проблем, когда служба обновления Windows создает ошибку и не реагирует на них на виртуальной машине Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 55dcd65e901a5ce8665ef8d3a2a29d7943581df9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200725"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Виртуальная машина не отвечает на ошибку "C01A001D" при применении Центр обновления Windows

В этой статье приведены инструкции по устранению проблем, при которых Центр обновления Windows (KB) выдает ошибку и не реагирует на виртуальную машину Azure.

## <a name="symptoms"></a>Симптомы

При использовании [диагностики загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины отображается Центр обновления Windows (КБ), но завершается с кодом ошибки: "C01A001D".

![не отвечает Центр обновления Windows](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Причина

Невозможно создать основной файл в файловой системе. Операционной системе не удается записать файлы на диск.

## <a name="resolution"></a>Решение

### <a name="process-overview"></a>Общие сведения о процессе

1. [Создайте виртуальную машину восстановления и получите к ней доступ](#create-and-access-a-repair-vm).
2. [Освободите место на жестком диске](#free-up-space-on-the-hard-disk).
3. [Рекомендуется: перед перестроением виртуальной машины включите сбор последовательной консоли и дампа памяти](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Перестройте виртуальную машину](#rebuild-the-vm).

> [!NOTE]
> При возникновении этой ошибки гостевая ОС не работает. Чтобы устранить эту проблему, необходимо устранить неполадки в автономном режиме.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и вход на нее

1. Выполните [шаги 1-3 команды восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , чтобы подготовить виртуальную машину восстановления.
2. Подключитесь к виртуальной машине восстановления с помощью подключение к удаленному рабочему столу.

### <a name="free-up-space-on-the-hard-disk"></a>Освободите место на жестком диске

Если диск еще не 1 ТБ, его размер необходимо изменить. Если диск равен 1 ТБ, выполните очистку диска и дефрагментацию диска.

1. Проверьте, есть ли свободное место на диске. Если размер диска меньше 1 ТБ, [его можно развернуть до 1 ТБ с помощью PowerShell](../windows/expand-os-disk.md).
2. Когда диск будет равен 1 ТБ, выполните очистку диска.
    - [Отключите диск данных от поврежденной виртуальной машины](../windows/detach-disk.md).
    - [Подключите диск данных к работающей виртуальной машине](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
    - Освободите место с помощью [средства очистки диска](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup).
3. После изменения размера и очистки диска выполните дефрагментацию.

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    В зависимости от уровня фрагментации это может занять несколько часов.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Рекомендуется: перед перестроением виртуальной машины включите сбор последовательной консоли и дампа памяти.

1. Откройте сеанс командной строки с повышенными привилегиями (запуск от имени администратора).
2. Выполните следующие команды:

    Включите последовательную консоль.

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Убедитесь, что свободное пространство на диске операционной системы не меньше размера памяти виртуальной машины (ОЗУ).

    Если на диске ОС недостаточно места, измените расположение, в котором будет создан файл дампа памяти, а затем сошлите его на диск данных, подключенный к виртуальной машине, и достаточно свободного места. Чтобы изменить расположение, замените на `%SystemRoot%` букву диска (например, "F:") диска данных в следующих командах:

    **Включить рекомендуемую конфигурацию дампа ОС:**

    Загрузка неработающего диска ОС:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Включите сбор для ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Включите сбор для ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Выгрузите поврежденный диск ОС:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Перестроение виртуальной машины

Чтобы заново собрать виртуальную машину, используйте [Шаг 5 команд восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
