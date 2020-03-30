---
title: Виртуальная машина Windows не может загрузиться из-за менеджера загрузки окон
description: В этой статье приводятся шаги по устранению проблем, в которых диспетчер Windows Boot Manager предотвращает загрузку виртуальной машины Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373353"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows VM не может загрузиться из-за менеджера загрузки Windows

В этой статье приводятся шаги по устранению проблем, в которых windows Boot Manager предотвращает загрузку виртуальной машины Azure (VM).

## <a name="symptom"></a>Симптом

VM застрял ждет запроса пользователя и не загружается, если вручную не проинструктировано.

При [использовании диагностики Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра скриншота VM, вы увидите, что скриншот отображает менеджер загрузки Windows с *сообщением Выберите операционную систему для запуска, или нажмите TAB, чтобы выбрать инструмент:*.

На рисунке 1
 
![Менеджер загрузки Windows заявляя: «Выберите операционную систему для запуска или нажмите TAB, чтобы выбрать инструмент:»](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Причина

Ошибка вызвана *дисплеем* флага BCD в менеджере загрузки Windows. Когда флаг включен, Windows Boot Manager предлагает пользователю в процессе загрузки выбрать, какой погрузчик они хотят запустить, что приводит к задержке загрузки. В Azure эта функция может добавить время, необходимое для загрузки VM.

## <a name="solution"></a>Решение

Обзор процесса:

1. Настройка для более быстрого времени загрузки с помощью серийной консоли.
2. Создайте и получите доступ к ремонту VM.
3. Наконфигурируйте для более быстрого времени загрузки на Ремонт VM.
4. **Рекомендуется:** Прежде чем восстановить VM, включите серийную консоль и сбор дампа памяти.
5. Восстановите VM.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Настройка для более быстрого времени загрузки с помощью серийной консоли

Если у вас есть доступ к серийной консоли, есть два способа, вы можете достичь более быстрого времени загрузки. Либо уменьшите время ожидания *displaybootmenu,* либо удалите флаг вообще.

1. Следуйте указаниям для доступа к [Серийной консоли Azure для Windows,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) чтобы получить доступ к текстовой консоли.

   > [!NOTE]
   > Если вы не можете получить доступ к серийной консоли, перейдите вперед, чтобы [создать и получить доступ к ремонту VM](#create-and-access-a-repair-vm).

2. **Вариант A**: Сокращение времени ожидания

   а. Время ожидания устанавливается на 30 секунд по умолчанию, но может быть изменено на более быстрое время (например, 5 секунд).

   b. Используйте следующую команду в серийной консоли для настройки значения тайм-аута:

      `bcdedit /set {bootmgr} timeout 5`

3. **Вариант B**: Удалить флаг BCD

   а. Чтобы предотвратить полное меню загрузки дисплея, введите следующую команду:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Если вы не смогли использовать серийную консоль для настройки более быстрого времени загрузки в следующих шагах, вы можете вместо этого продолжить следующие шаги. Теперь для решения этой проблемы в автономном режиме вы будете устрашать неполадки.

### <a name="create-and-access-a-repair-vm"></a>Создание и доступ к ремонту VM

1. Используйте [шаги 1-3 команды по ремонту VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) для подготовки ремонтного VM.
2. Используйте удаленное подключение к рабочему столу для ремонта VM.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Настройка для более быстрого времени загрузки на ремонт VM

1. Откройте командную строку с повышенными привилегиями.
2. Введите следующее, чтобы включить DisplayBootMenu:

   Используйте эту команду для **вдвизаний поколения 1:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Используйте эту команду для **вдвизаний поколения 2:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Замените любые более или менее символы, а также текст в них, например, "< текст здесь >".

3. Измените значение тайм-аута до 5 секунд:

   Используйте эту команду для **вдвизаний поколения 1:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Используйте эту команду для **вдвизаний поколения 2:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Замените любые более или менее символы, а также текст в них, например, "< текст здесь >".

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