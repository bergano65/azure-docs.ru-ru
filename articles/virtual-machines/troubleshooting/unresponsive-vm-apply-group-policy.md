---
title: При применении политики виртуальная машина Azure не отвечает
description: В этой статье приведены инструкции по устранению проблем, когда при применении политики во время загрузки виртуальной машины Azure зависает экран загрузки.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748733"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Виртуальная машина перестает отвечать при применении политики "Групповая политика локальных пользователей и групп".

В этой статье приведены шаги по устранению проблемы, когда при применении политики во время загрузки виртуальной машины Azure зависает экран загрузки.

## <a name="symptoms"></a>Симптомы

При использовании [диагностики загрузки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра снимка экрана виртуальной машины экран зависает на загрузке с сообщением: *Применение политики "Групповая политика локальных пользователей и групп"* .

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Снимок экрана загрузки применения политики Групповая политика локальных пользователей и групп (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Снимок экрана загрузки применения политики Групповая политика локальных пользователей и групп (Windows Server 2012).":::

## <a name="cause"></a>Причина

Существуют конфликтующие блокировки, когда политика пытается очистить старые профили пользователей.

> [!NOTE]
> Это применимо только к Windows Server 2012 и Windows Server 2012 R2.

Ниже приведена проблемная политика:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Решение

### <a name="process-overview"></a>Общие сведения о процессе

1. [Создание виртуальной машины для восстановления и получение доступа к ней](#step-1-create-and-access-a-repair-vm)
2. [Отключение политики](#step-2-disable-the-policy)
3. [Включение последовательной консоли и сбор резервных копий в памяти](#step-3-enable-serial-console-and-memory-dump-collection)
4. [Перестроение виртуальной машины](#step-4-rebuild-the-vm)

> [!NOTE]
> Если вы сталкиваетесь с этой ошибкой загрузки, гостевая ОС не работает. Требуется устранить неполадки в автономном режиме.

### <a name="step-1-create-and-access-a-repair-vm"></a>Шаг 1. Создание виртуальной машины для восстановления и получение доступа к ней

1. Выполните [шаги 1–3 из списка команд для восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example), чтобы подготовить виртуальную машину для восстановления.
2. Используйте подключение к удаленному рабочему столу, чтобы связаться с виртуальной машиной для восстановления.

### <a name="step-2-disable-the-policy"></a>Шаг 2. Отключите политику

1. Откройте редактор реестра на виртуальной машине восстановления.
2. Разместите ключ **HKEY_LOCAL_MACHINE**, а затем выберите пункты меню **Файл** >  **"Загрузить куст..."** .

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="На снимке экрана показано выделенный HKEY_LOCAL_MACHINE и меню, содержащее пункт Загрузить куст.":::

    - Пункт "Загрузить куст" позволяет загружать разделы реестра из автономной системы, в таком случае неисправный диск подключен к виртуальной машине восстановления.
    - Параметры в рамках системы хранятся в `HKEY_LOCAL_MACHINE`, и их можно сократить как "HKLM".
3. На подключенном диске перейдите к файлу `\windows\system32\config\SOFTWARE` и откройте его.

    1. Вам будет предложено ввести имя. Введите BROKENSOFTWARE.<br/>
    2. Чтобы удостовериться, загружен ли BROKENSOFTWARE, разверните **HKEY_LOCAL_MACHINE** и найдите добавленный ключ BROKENSOFTWARE.
4. Перейдите к BROKENSOFTWARE и проверьте, существует ли ключ CleanupProfile в загруженном кусте.

    1. Если ключ существует, то политика CleanupProfile задана и ее значение представляет политику хранения в ближайшее время. Продолжайте удаление ключа.<br/>
    2. Если ключ не существует, политика CleanupProfile не установлена. [Отправьте запрос в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), включая файл memory.dmp, расположенный в каталоге Windows, подключенного диска ОС.

5. Удалите ключ CleanupProfiles с помощью следующей команды:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Выгрузите куст BROKENSOFTWARE с помощью следующей команды:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Шаг 3. Включение последовательной консоли и сбор резервных копий в памяти

Чтобы включить сбор резервных копий в памяти и последовательную консоль, выполните следующий скрипт:

1. Откройте сеанс командной строки с повышенными привилегиями (запуск от имени администратора).
2. Выполните следующие команды:

    **Включите последовательную консоль**. 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Убедитесь, что свободное место на диске ОС не меньше объема памяти виртуальной машины (ОЗУ).

    Если на диске ОС недостаточно места, измените расположение резервной копии памяти и переместите ее на подключенный диск данных, на котором есть достаточно свободного места. Чтобы изменить расположение, замените "% SystemRoot%" на букву диска (например, "F:") в приведенных ниже командах.

    **Рекомендуемая конфигурация для включения дампа ОС**.

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

### <a name="step-4-rebuild-the-vm"></a>Шаг 4. Перестроение виртуальной машины

Чтобы заново собрать виртуальную машину, выполните [шаг 5 из списка команд для восстановления ВМ](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example).

Если проблема устранена, политика отключена только локально. Для длительного решения не используйте политику CleanupProfiles на виртуальных машинах. Используйте другой метод для очисток профиля.

Не используйте эту политику:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Дальнейшие действия

Если при обращении к Центру обновления Windows возникли проблемы, см. статью [Виртуальная машина не отвечает и выводит ошибку "C01A001D" при обращении к Центру обновления Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).