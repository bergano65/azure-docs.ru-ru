---
title: Windows-ошибка завершения — 0x00000074 неверные сведения о конфигурации системы
description: В этой статье приводятся инструкции по устранению проблем, в которых Windows не может загрузиться и требуется перезапуститься из-за неправильной информации о конфигурации системы в виртуальной машине Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 071b5786127af31a2ad3266c128dbfb7cacad656
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942192"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows-ошибка завершения — 0x00000074 неверные сведения о конфигурации системы

В этой статье приводятся инструкции по устранению проблем, в которых Windows не может загрузиться и требуется перезапуститься из-за неправильной информации о конфигурации системы в виртуальной машине Azure.

## <a name="symptom"></a>Симптом

При использовании [диагностики загрузки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается **#0x00000074** или **BAD_SYSTEM_CONFIG_INFO**код завершения Windows.

*Ваш компьютер столкнулся с проблемой и должен перезапуститься. Вы можете перезапустить.* 
 Дополнительные *сведения об этой неполадке и возможных исправлениях см http://windows.com/stopcode . на странице* 
 *Если вы звоните в службу поддержки, предоставьте ей следующие сведения:* 
 *Код завершения: BAD_SYSTEM_CONFIG_INFO*

  ![Код завершения Windows 0x00000074, который также отображается как "BAD_SYSTEM_CONFIG_INFO". Windows информирует пользователя о том, что на компьютере возникла проблема, и его необходимо перезапустить.](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>Причина

**BAD_SYSTEM_CONFIG_INFO** код ошибки возникает, если куст **системного** реестра поврежден. Эта ошибка может быть вызвана любой из следующих причин:

- Куст реестра не был правильно закрыт.
- Куст реестра поврежден.
- Отсутствуют разделы или значения реестра.

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Общие сведения о процессе:

1. Создайте виртуальную машину восстановления и получите к ней доступ.
1. Проверьте наличие повреждений Hive.
1. Включите последовательную консоль и сбор дампов памяти.
1. Перестройте виртуальную машину.

> [!NOTE]
> При возникновении этой ошибки операционная система на виртуальной машине (ОС) не работает. Для устранения этой проблемы вы можете устранить неполадки в автономном режиме.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и доступ к ней

1. Выполните шаги 1–3 из списка [команд для восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands), чтобы подготовить виртуальную машину для восстановления.
1. Проверьте наличие повреждений Hive.
1. Используйте подключение к удаленному рабочему столу для подключения к виртуальной машине восстановления.
1. Скопируйте `\windows\system32\config` папку и сохраните ее в работоспособном разделе диска или в другом надежном расположении. Создайте резервную копию этой папки в качестве меры предосторожности, так как вы будете изменять критически важные файлы реестра.

> [!NOTE]
> Создайте копию `\windows\system32\config` папки в качестве резервной копии на случай, если необходимо выполнить откат всех изменений, внесенных в реестр.

### <a name="check-for-hive-corruption"></a>Проверка повреждения Hive

Приведенные ниже инструкции помогут определить, вызвана ли причина повреждениями Hive, или если куст не был правильно закрыт. Если куст не был правильно закрыт, вы сможете разблокировать файл и исправить виртуальную машину.

1. На виртуальной машине восстановления откройте приложение **редактора реестра** . В панели поиска Windows введите "regedit", чтобы найти его.
1. В редакторе реестра выберите **HKEY_LOCAL_MACHINE** , чтобы выделить его, а затем выберите **файл > загрузить Hive...**  в меню.
1. Перейдите к `\windows\system32\config\SYSTEM` и выберите **Открыть**.
1. При появлении запроса на ввод имени введите **BROKENSYSTEM**.

   1. Если не удается открыть куст или он пуст, то куст поврежден. Если куст поврежден, [откройте запрос в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

     ![Возникает ошибка, указывающая, что редактор реестра не может загрузить Hive.](./media/windows-stop-error-bad-system-config-info/2.png)

   1. Если куст открывается в обычном режиме, куст не был закрыт должным образом. Перейдите к шагу 5.

1. Чтобы устранить неправильное закрытие Hive, выделите **BROKENSYSTEM** , а затем выберите **файл > выгрузить Hive...** , чтобы разблокировать файл.

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

Чтобы перестроить виртуальную машину, [выполните шаг 5 из списка команд для восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example).
