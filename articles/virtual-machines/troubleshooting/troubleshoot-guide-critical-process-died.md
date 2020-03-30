---
title: Ошибка остановки Windows -
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373366"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Ошибка остановки Windows - #0x000000EF "Критический процесс умер"

В этой статье приводятся шаги по устранению проблем, когда критический процесс умирает во время загрузки в Azure VM.

## <a name="symptom"></a>Симптом

При [использовании диагностики Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра скриншота VM, вы увидите, что скриншот отображает ошибку *#0x000000EF* с сообщением *Критический процесс умер.*

!["На вашем ПК возникла проблема, и его необходимо перезагрузить. Мы лишь собираем некоторые сведения об ошибке, а затем вы сможете выполнить перезагрузку". (Полный) Если вы хотите узнать больше, вы можете искать в Интернете позже для этой ошибки: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Причина

Как правило, это происходит из-за сбоя критического системного процесса во время загрузки. Подробнее о критических проблемах процесса читайте в материале[«Проверка ошибок 0xEF: CRITICAL_PROCESS_DIED».](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Обзор процесса:

1. Создайте и получите доступ к ремонту VM.
2. Исправить любой OS Коррупция.
3. **Рекомендуется:** Прежде чем восстановить VM, включите серийную консоль и сбор дампа памяти.
4. Восстановите VM.

> [!NOTE]
> При возникновении этой ошибки загрузки ОС guest не работает. Вы будете устранения неполадок в автономном режиме, чтобы решить эту проблему.

### <a name="create-and-access-a-repair-vm"></a>Создание и доступ к ремонту VM

1. Используйте [шаги 1-3 команды по ремонту VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) для подготовки ремонтного VM.
2. Использование удаленного соединения рабочего стола подключается к Ремонту VM.

### <a name="fix-any-os-corruption"></a>Исправить любой OS Коррупция

1. Откройте командную строку с повышенными привилегиями.
2. Выполнить следующую команду System File Checker (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Где < BOOT DISK DRIVE > является объем загрузки Repair VM (обычно "C:") и < BROKEN DISK DRIVE > будет дисковой буквы для прикрепленного диска от сломанного VM. Замените больше, чем / меньше, чем символы, а также текст, содержащийся в них, например, "< текст здесь >", с соответствующим письмом.

3. Далее используйте [шаг 5 команды VM Repair Commands,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) чтобы собрать VM и посмотреть, если он сапоги.
4. Если VM все еще не загружается, продолжайте собирать файл свалки памяти.

### <a name="collect-the-memory-dump-file"></a>Сбор файла свалки памяти

Если проблема сохраняется после запуска SFC, для определения причины проблемы потребуется анализ файла свалки памяти. Чтобы собрать файл свалки памяти, выполните следующие действия:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Прикрепите диск ОС к новому Repair VM

1. Используйте [шаги 1-3 команды по ремонту VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) для подготовки нового Repair VM.
2. Использование удаленного соединения рабочего стола подключается к Ремонту VM.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Найдите файл свалки и отправьте билет поддержки

3. На ремонте VM, перейдите к папке окна в прикрепленном диске ОС. Если письмо драйвера, назначенное на прикрепленный диск *ОС,* f, вам нужно перейти к *F: Windows*.
4. Найдите файл *memory.dmp,* а затем [отправьте билет поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом свалки памяти.

   > [!NOTE]
   > Если вы не можете найти файл дампа, выполните ниже шаги, чтобы включить сбор дампа памяти и серийную консоль, затем вернитесь в этот раздел и повторите шаги в задаче выше, чтобы собрать файл свалки памяти.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Рекомендуем: Перед восстановлением VM включите серийную консоль и коллекцию дамп памяти

Для включения сбора пультов памяти и последовательной консоли запустите следующий сценарий:

1. Откройте сеанс повышенной команды (запуск в качестве администратора).
2. Выполните следующие команды:

   Включить серийную консоль

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Замените любые более или менее символы, а также текст в них, например, "< текст здесь >".

3. Убедитесь, что свободное пространство на диске ОС столько, сколько размер памяти (RAM) на VM.

Если на диске ОС недостаточно места, следует изменить место, где будет создан файл свалки памяти, и сослаться на то, что на любой диск данных, прикрепленный к VM, который имеет достаточно свободного пространства. Чтобы изменить местоположение, замените "%SystemRoot%" на дисковую букву (например, "F:") диска данных в нижеприведенных командах.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Предлагаемая конфигурация для включения Дампа ОС

**Нагрузка Сломанный диск ОС**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Включить ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Включить ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Разгрузить сломанный диск ОС:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Перестроить оригинальный VM

Используйте [шаг 5 команды по ремонту VM,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) чтобы собрать VM.
