---
title: Ошибка загрузки - "это не загружаемый диск"
description: В этой статье приведены шаги для решения проблем, когда диск не загружается в виртуальную машину Azure
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
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300983"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Ошибка загрузки - Это не загружаемый диск

В этой статье приводятся шаги по устранению проблем, когда диск не загружается в виртуальную машину Azure (VM).

## <a name="symptoms"></a>Симптомы

При [использовании диагностики Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра скриншота VM, вы увидите, что скриншот отображает запрос с сообщением "Это не загружаемый диск. Пожалуйста, вставьте загрузочную дискету и нажмите любой ключ, чтобы попробовать еще раз ...'.

   На рисунке 1

   ![На рисунке 1 показано сообщение: «Это не загружаемый диск. Пожалуйста, вставьте загрузочную дискету и нажмите любой ключ, чтобы попробовать еще раз ..."](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Причина

Это сообщение об ошибке означает, что процесс загрузки ОС не может найти активный раздел системы. Эта ошибка может также означать, что в магазине Boot Configuration Data (BCD) отсутствует ссылка, которая не позволяет ей найти раздел Windows.

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Обзор процесса

1. Создайте и получите доступ к ремонту VM.
2. Установите статус раздела active.
3. Исправьте раздел диска.
4. **Рекомендуется:** Прежде чем восстановить VM, включите серийную консоль и сбор дампа памяти.
5. Перестроить оригинальный VM.

   > [!NOTE]
   > При возникновении этой ошибки загрузки ОС guest не работает. Вы будете устранения неполадок в автономном режиме, чтобы решить эту проблему.

### <a name="create-and-access-a-repair-vm"></a>Создание и доступ к ремонту VM

1. Используйте шаги 1-3 [команды по ремонту VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) для подготовки ремонтного VM.
2. Использование удаленного соединения рабочего стола подключается к Ремонту VM.

### <a name="set-partition-status-to-active"></a>Установка статуса раздела на активную

Поколение 1 VMs должны сначала проверить, что раздел ОС, который держит магазин BCD помечен как *активный.* Если у вас есть поколение 2 VM, пропустить вперед, чтобы [исправить раздел диска](#fix-the-disk-partition), как *флаг статуса* был унес в более позднем поколении.

1. Откройте поднятую командную подсказку *(cmd.exe).*
2. Введите *дискпарт* для запуска инструмента DISKPART.
3. Введите *диск списка,* чтобы перечислить диски в системе и определить прикрепленный OS VHD.
4. После того, как прилагается OS VHD находится, введите *s диска,* чтобы выбрать диск.  Смотрите рисунок 2, где диск 1 прилагается OS VHD.

   Рис. 2

   ![На рисунке 2 показано окно «DISKPART», показывающее вывод команды диска списка, диска 0 и диска 1, отображаемый в таблице.  Также отображает выход команды sel disk 1, Disk 1 является выбранным диском](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Как только диск выбран, введите *раздел списка,* чтобы перечислить разделы выбранного диска.
6. После того, как раздел загрузки определен, введите *раздел sel,* чтобы выбрать раздел.  Обычно раздел загрузки будет около 350 МБ в размере.  См. Рисунок 3, где раздел 1 является разделом загрузки.

   Рис. 3

   ![На рисунке 3 показано окно «DISKPART» с выводом команды «список раздела». Раздел 1 и раздел 2 отображаются в таблице. Он также показывает вывод команды «sel partition 1», когда раздел 1 является выбранным диском.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Введите раздел детали для проверки состояния раздела. См. Рисунок 4, где раздел *активен: Нет,* или Рисунок 5, где раздел "Активный: Да".

   Рис. 4

   ![На рисунке 4 показано окно «DISKPART» с выходом команды «детальная перегородка», когда раздел 1 установлен на «Активный: Нет»](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Рис. 5

   ![На рисунке 5 показано окно «DISKPART» с выходом команды «детальная перегородка», когда раздел 1 установлен на «Активный: Да».](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Если раздел не **активен,** *введите активный* для изменения *флага Active.*
9. Убедитесь, что изменение статуса было сделано должным образом, введя *детальный раздел.*

   Рис. 6

   ![На рисунке 6 показано окне диска с выходом команды «детальная перегородка», когда раздел 1 установлен на «Активный: Да»](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Введите *выход,* чтобы закрыть инструмент DISKPART и сохранить изменения конфигурации.

### <a name="fix-the-disk-partition"></a>Исправить раздел диска

1. Откройте поднятую команду (cmd.exe).
2. Используйте следующую команду для запуска *CHKDSK* на диске (ы) и исправления ошибок:

   `chkdsk <DRIVE LETTER>: /f`

   Добавление опции команды '/f' исправит любые ошибки на диске. Убедитесь в <DRIVE LETTER> том, чтобы заменить письмом прилагается OS VHD.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Рекомендуем: Перед восстановлением VM включите серийную консоль и коллекцию дамп памяти

Для включения сбора пультов памяти и последовательной консоли запустите следующий сценарий:

1. Откройте сеанс повышенной команды (запуск в качестве администратора).
2. Выполните следующие команды:

   Включить серийную консоль

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
