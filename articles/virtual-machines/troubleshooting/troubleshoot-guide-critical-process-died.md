---
title: Windows-ошибка завершения —
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80373366"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows-ошибка завершения — #0x000000EF "критический процесс умер"

В этой статье приведены инструкции по устранению проблем, при которых критический процесс выходит из строя во время загрузки виртуальной машины Azure.

## <a name="symptom"></a>Симптом

При использовании [диагностики загрузки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается сообщение об ошибке *#0x000000EF* с *критическим процессом умер*.

!["На вашем ПК возникла проблема, и его необходимо перезагрузить. Мы лишь собираем некоторые сведения об ошибке, а затем вы сможете выполнить перезагрузку". (# #% завершено) Если вы хотите узнать больше, можно выполнить поиск в Интернете позже для этой ошибки: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Причина

Как правило, это связано с тем, что во время загрузки происходит сбой критического системного процесса. Дополнительные сведения о критических проблемах процесса см. в статье "[Проверка ошибок 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Обзор процесса:

1. Создайте виртуальную машину восстановления и получите к ней доступ.
2. Устраните все повреждения ОС.
3. **Рекомендуется**. перед перестроением виртуальной машины включите сбор последовательной консоли и дампа памяти.
4. Перестройте виртуальную машину.

> [!NOTE]
> При возникновении этой ошибки загрузки гостевая ОС не работает. Для устранения этой проблемы вы будете устранять неполадки в автономном режиме.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и доступ к ней

1. Выполните [шаги 1-3 команды восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) , чтобы подготовить виртуальную машину восстановления.
2. С помощью подключение к удаленному рабочему столу подключитесь к виртуальной машине восстановления.

### <a name="fix-any-os-corruption"></a>Исправление повреждений ОС

1. Откройте командную строку с повышенными привилегиями.
2. Запустите следующую команду средства проверки системных файлов (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Где < ЗАГРУЗОЧный диск > является загрузочным томом восстанавливаемой виртуальной машины (обычно "C:"), а < НЕИСПРАВНЫй диск > будет буквой диска для подключенного диска из поврежденной виртуальной машины. Замените символы "больше" или "меньше", а также текст, содержащийся в них, например "< текст здесь >" с соответствующей буквой.

3. Затем используйте [Шаг 5 команд восстановления виртуальной](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) машины, чтобы заново собрать виртуальную машину и проверить, не загружается ли она.
4. Если виртуальная машина по-прежнему не загружается, продолжите получать файл дампа памяти.

### <a name="collect-the-memory-dump-file"></a>Получение файла дампа памяти

Если проблема сохраняется после запуска SFC, необходимо выполнить анализ файла дампа памяти, чтобы определить причину проблемы. Чтобы получить файл дампа памяти, выполните следующие действия.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Подключение диска ОС к новой виртуальной машине восстановления

1. Выполните [шаги 1-3 команды восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) , чтобы ПОДГОТОВИТЬ новую виртуальную машину для восстановления.
2. С помощью подключение к удаленному рабочему столу подключитесь к виртуальной машине восстановления.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Размещение файла дампа и отправка запроса в службу поддержки

3. На виртуальной машине восстановления перейдите в папку Windows на подключенном диске ОС. Если буква драйвера, назначенная подключенному диску ОС, — *F*, необходимо обратиться по адресу *ф:\виндовс*.
4. Найдите файл *Memory. dmp* и отправьте запрос в [службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом дампа памяти.

   > [!NOTE]
   > Если не удается найти файл дампа, выполните следующие действия, чтобы включить сбор дампов памяти и последовательную консоль, затем вернитесь к этому разделу и повторите шаги, описанные выше в задаче, чтобы получить файл дампа памяти.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Рекомендуется: перед перестроением виртуальной машины включите сбор последовательной консоли и дампа памяти.

Чтобы включить сбор дампов памяти и последовательную консоль, выполните следующий скрипт:

1. Откройте сеанс командной строки с повышенными привилегиями (Запуск от имени администратора).
2. Выполните следующие команды:

   Включить последовательную консоль

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Замените символы "больше" или "меньше", а также текст внутри них, например "< текст здесь >".

3. Убедитесь, что объем свободного места на диске ОС совпадает с объемом памяти (ОЗУ) виртуальной машины.

Если на диске операционной системы недостаточно места, следует изменить расположение файла дампа памяти и сослаться на любой диск данных, подключенный к виртуальной машине, имеющей достаточно свободного места. Чтобы изменить расположение, замените "% SystemRoot%" буквой диска (например, "F:") диска данных в приведенных ниже командах.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Рекомендуемая конфигурация для включения дампа ОС

**Загрузка неработающего диска ОС**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Включить в ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Включить в ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Выгрузка неработающего диска ОС:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Перестроение исходной виртуальной машины

Чтобы заново собрать виртуальную машину, используйте [Шаг 5 команд восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .
