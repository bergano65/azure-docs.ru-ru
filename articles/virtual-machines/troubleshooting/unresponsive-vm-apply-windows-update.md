---
title: Azure VM не реагирует на ошибку C01A001D при применении обновления Windows
description: В этой статье приводятся шаги по устранению проблем, в которых обновление Windows генерирует ошибку и не отвечает в VM Azure.
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
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633961"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>VM не реагирует на ошибку "C01A001D при применении обновления Windows

В этой статье приводятся шаги по устранению проблем, в которых обновление Windows (KB) генерирует ошибку и не реагирует в Azure VM.

## <a name="symptoms"></a>Симптомы

При [использовании диагностики Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра скриншота VM, обновление Windows (KB) в процессе отображается, но не с кодом ошибки: 'C01A001D'.

![безответное обновление Windows](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Причина

Основной файл не может быть создан в файловой системе. Операционная система не может записывать файлы на диск.

## <a name="resolution"></a>Решение

### <a name="process-overview"></a>Общие сведения о процессе

1. [Создайте и получите доступ к ремонту VM.](#create-and-access-a-repair-vm)
2. [Освободите место на жестком диске.](#free-up-space-on-the-hard-disk)
3. [Рекомендуется: Перед восстановлением VM, включите серийную консоль и коллекцию памяти.](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)
4. [Восстановить VM](#rebuild-the-vm).

> [!NOTE]
> При возникновении этой ошибки ОС для гостей не работает. Для решения этой проблемы необходимо устранить неполадки в автономном режиме.

### <a name="create-and-access-a-repair-vm"></a>Создание и доступ к ремонту VM

1. Выполните [шаги 1-3 команды по ремонту VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) для подготовки ремонта VM.
2. Подключайтесь к Ремонту VM с помощью удаленного соединения рабочего стола.

### <a name="free-up-space-on-the-hard-disk"></a>Освободите место на жестком диске

Если диск еще не 1 Tb, вы должны изменить его размер. После того, как диск 1 ТБ, выполнить очистку диска и дефрагментации диска.

1. Проверьте, заполнен ли диск. Если диск ниже 1 Тб, [расширьте его до максимум 1 Тб с помощью PowerShell.](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
2. После того, как диск 1 Tb, выполнить очистку диска.
    - [Отсоедините диск данных от сломанного VM.](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk)
    - [Прикрепите диск данных к функционирующему VM.](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)
    - Используйте [инструмент очистки диска,](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) чтобы освободить место.
3. После изобрачив размер и очистку, дефрагментируется диск:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    В зависимости от уровня фрагментации, это может занять несколько часов.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Рекомендуется: Перед восстановлением VM, включите серийную консоль и сбор памяти свалки

1. Откройте сеанс повышенной команды (запуск в качестве администратора).
2. Выполните следующие команды:

    Включить серийную консоль:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Убедитесь, что свободное пространство на диске ОС, по крайней мере, равно размеру памяти VM (RAM).

    Если на диске ОС не хватает места, измените место, где будет создан файл свалки памяти, и направь его на диск данных, прикрепленный к VM и имеюший достаточно свободного пространства. Чтобы изменить местоположение, `%SystemRoot%` замените дисковую букву (например, "F:") диска данных в нижеприведенных командах:

    **Включить дамп ОС предложил конфигурацию:**

    Нагрузка Сломанный диск ОС:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Включить ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Включить ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Выгрузите сломанный диск ОС:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Восстановить VM

Используйте [шаг 5 команды по ремонту VM,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) чтобы собрать VM.
