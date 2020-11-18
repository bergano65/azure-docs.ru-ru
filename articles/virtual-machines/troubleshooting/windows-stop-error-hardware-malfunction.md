---
title: Windows-ошибка завершения — оборудование неисправно
description: В этой статье приведены инструкции по устранению проблем, возникающих при сбое виртуальных машин Windows Server 2008 с сообщением об ошибке, сообщающим о неисправности оборудования.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: eb4e0a246d6a33c3fad5f44b99a37997e4462f05
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663862"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows-ошибка завершения — оборудование неисправно

В этой статье приведены инструкции по устранению проблем, возникающих при сбое виртуальных машин Windows Server 2008 с сообщением об ошибке, сообщающим о неисправности оборудования.

## <a name="symptoms"></a>Симптомы

При использовании [диагностики загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается синий экран с сообщением:

**\*\*\* Аппаратный сбой**

**Обратитесь к поставщику за поддержкой**

**\*\*\* Система остановлена \*\*\***

#### <a name="blue-screen"></a>Синий экран

![На снимке экрана показан сбой аппаратного сбоя на экране.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Последовательная консоль

![На снимке экрана отображается сообщение "сбой оборудования" в функции последовательной консоли, если включена последовательная консоль.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Причина

Этот экран появится, когда гостевая ОС была настроена неправильно и было отправлено немаскированное прерывание (NMI). Сообщение об ошибке указывает, что программа режима ядра создала исключение, которое обработчик не перехватывает. Чтобы узнать, какое исключение было создано, можно собрать дамп памяти.

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Обзор процесса 

1. Настройка раздела реестра немаскированного прерывания (NMI) 
2. Создание виртуальной машины для восстановления и получение доступа к ней 
3. Включение сбора последовательной консоли и дампа памяти 
4. Перестроение виртуальной машины 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>Настройка раздела реестра немаскированного прерывания (NMI)

1. Используя портал Azure, перезапустите виртуальную машину, чтобы гостевая ОС загрузится нормально. 
2. После восстановления какого-либо доступа к виртуальной машине откройте командную строку с повышенными привилегиями (Запуск от имени администратора). 
3. Настройте раздел реестра NMI с помощью следующей команды:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [Просмотр дополнительных сведений о команде REG ADD](https://docs.microsoft.com/windows-server/administration/windows-commands/reg-add)
4. *(Необязательно)* Настройка сбора дампа памяти:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Необязательно)* Настройка доступа к последовательной консоли:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [Просмотр дополнительных сведений о команде BCDEDIT](https://docs.microsoft.com/windows-server/administration/windows-commands/bcdedit)
6. Перезапустите виртуальную машину с помощью следующей команды:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [Просмотр дополнительных сведений о команде SHUTDOWN](https://docs.microsoft.com/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> Теперь проблема должна быть исправлена!

> [!NOTE]
> После перезагрузки протестируйте виртуальную машину, чтобы убедиться, что она работает как обычная. Если у вас по-прежнему возникают проблемы, можно перейти к следующему разделу для получения дальнейших инструкций.

> [!TIP]
> Рекомендуется настроить раздел реестра, не поддерживающий немаскируемое прерывание (NMI) в разделе выше, но если виртуальная машина не загрузилась нормально, то, возможно, в реестре гостевой ОС не будет выполнено никаких изменений. В этом случае вы можете вручную добавить параметры реестра, следуя приведенным ниже инструкциям.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и вход на нее

1. Выполните шаги 1–3 из списка [команд для восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md), чтобы подготовить виртуальную машину для восстановления.
2. Подключитесь к виртуальной машине восстановления с помощью подключение к удаленному рабочему столу.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Включение сбора последовательной консоли и дампа памяти

Перед перестроением виртуальной машины рекомендуется включить сбор дампов памяти и последовательную консоль. Для этого выполните следующий скрипт: 

1. Откройте сеанс командной строки с повышенными привилегиями (выполнив запуск от имени администратора). 
2. Перечислите данные из хранилища BCD и определите идентификатор загрузчика, который будет использоваться на следующем шаге. 
    1. Для виртуальной машины поколения 1 введите следующую команду и запишите идентификатор в списке: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        В команде замените на `<BOOT PARTITON>` букву раздела на подключенном диске, который содержит папку Boot. 

        ![На снимке экрана показан результат перечисления хранилища BCD в виртуальной машине поколения 1, которая отображается под загрузкой Windows. это идентификатор.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. Для виртуальной машины поколения 2 введите следующую команду и запишите идентификатор в списке:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * В команде замените на `<LETTER OF THE EFI SYSTEM PARTITION>` букву системного раздела EFI.
        * Может быть полезно запустить консоль управления дисками, чтобы указать соответствующий системный раздел, помеченный как *системный раздел EFI*.
        * Идентификатор может быть уникальным GUID или *диспетчером загрузки* по умолчанию.
3. Чтобы включить последовательную консоль, выполните следующие команды:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * В команде замените на `<VOLUME LETTER WHERE THE BCD FOLDER IS>` букву папки BCD.
    * В команде замените `<BOOT LOADER IDENTIFIER>` идентификатором, найденным на предыдущем шаге.
4. Убедитесь, что свободное пространство на диске операционной системы превышает объем памяти (ОЗУ) виртуальной машины. 
    1. Если на диске ОС недостаточно места, следует изменить расположение, в котором будет создан файл дампа памяти. Вместо того чтобы создавать файл на диске операционной системы, вы можете ссылаться на него на любой другой диск данных, подключенный к виртуальной машине, имеющей достаточно свободного места. Чтобы изменить расположение, замените **% systemroot%** буквой диска (например, **F:**) диска данных в командах, перечисленных ниже. 
    2. Введите приведенные ниже команды (рекомендуемая конфигурация дампа).

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
### <a name="rebuild-the-virtual-machine"></a>Перестроение виртуальной машины

* Чтобы перестроить виртуальную машину, [выполните шаг 5 из списка команд для восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Устранение ошибок загрузки виртуальных машин Azure](./boot-error-troubleshoot.md)