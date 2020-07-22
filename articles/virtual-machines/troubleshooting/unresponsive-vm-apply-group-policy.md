---
title: При применении политики виртуальная машина Azure не отвечает
description: В этой статье приводятся инструкции по устранению проблем, в которых экран загрузки не отвечает при применении политики во время запуска на виртуальной машине Azure.
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
ms.openlocfilehash: cbf2fe491e1fe0b553eab04ca7190da0413a3ba6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526016"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>Виртуальная машина не отвечает, когда применяется групповая политика политики локальных пользователей и групп

В этой статье приводятся инструкции по устранению проблем, при которых экран загрузки не реагирует на то, что во время запуска виртуальная машина Azure применяет политику.

## <a name="symptoms"></a>Симптомы

Когда вы используете [диагностику загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины, экран зависает при загрузке с сообщением: "применение групповая политика политики" Локальные пользователи и группы ".

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
1. [Отключение политики](#step-2-disable-the-policy)
1. [Включение сбора последовательной консоли и дампа памяти](#step-3-enable-serial-console-and-memory-dump-collection)
1. [Перестроение виртуальной машины](#step-4-rebuild-the-vm)

> [!NOTE]
> При возникновении этой ошибки загрузки гостевая ОС не работает. Требуется устранить неполадки в автономном режиме.

### <a name="step-1-create-and-access-a-repair-vm"></a>Шаг 1. Создание виртуальной машины для восстановления и получение доступа к ней

1. Выполните [шаги 1–3 из списка команд для восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example), чтобы подготовить виртуальную машину для восстановления.
2. Используйте подключение к удаленному рабочему столу для подключения к виртуальной машине восстановления.

### <a name="step-2-disable-the-policy"></a>Шаг 2. Отключите политику

1. Откройте редактор реестра на виртуальной машине восстановления.
1. Перейдите к разделу **HKEY_LOCAL_MACHINE** и выберите **файл**  >  **Загрузить куст** в меню.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="На снимке экрана показано выделенный HKEY_LOCAL_MACHINE и меню, содержащее пункт Загрузить куст.":::

    - Можно использовать Load Hive для загрузки разделов реестра из автономной системы. В этом случае система является поврежденным диском, подключенным к виртуальной машине восстановления.
    - Параметры на уровне системы хранятся в `HKEY_LOCAL_MACHINE` и могут быть сокращены по "HKLM".
1. На подключенном диске перейдите к файлу `\windows\system32\config\SOFTWARE` и откройте его.

    1. Когда появится запрос на ввод имени, введите BROKENSOFTWARE.
    1. Чтобы удостовериться, загружен ли BROKENSOFTWARE, разверните **HKEY_LOCAL_MACHINE** и найдите добавленный ключ BROKENSOFTWARE.
1. Перейдите по адресу BROKENSOFTWARE и проверьте, существует ли ключ Клеануппрофиле в загруженном Hive.

    1. Если ключ существует, задается политика Клеануппрофиле. Его значение представляет политику хранения, измеряемую в днях. Продолжайте удаление ключа.
    1. Если ключ не существует, политика CleanupProfile не установлена. [Отправьте запрос в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), включая файл memory.dmp, расположенный в каталоге Windows, подключенного диска ОС.

1. Удалите ключ Клеануппрофилес с помощью следующей команды:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
1.  Выгрузите куст BROKENSOFTWARE с помощью следующей команды:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Шаг 3. Включение сборки последовательной консоли и дампа памяти

Чтобы включить сбор дампов памяти и последовательную консоль, выполните следующий скрипт:

1. Откройте сеанс командной строки с повышенными привилегиями. (Запуск от имени администратора.)
1. Выполните следующие команды, чтобы включить последовательную консоль:
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. Убедитесь, что свободное пространство на диске ОС не меньше объема памяти (ОЗУ) виртуальной машины.

    Если на диске ОС недостаточно места, измените расположение дампа памяти и сошлите его на подключенный диск данных с достаточным свободным местом. Чтобы изменить расположение, замените "% SystemRoot%" буквой диска (например, "F:") диска данных в следующих командах.

    **Рекомендуемая конфигурация для включения дампа ОС**

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

Чтобы заново собрать виртуальную машину, используйте [Шаг 5 команд восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

Если проблема устранена, политика теперь отключена локально. Для постоянного решения не используйте политику Клеануппрофилес на виртуальных машинах. Используйте другой метод для очисток профиля.

Не используйте эту политику:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Дальнейшие действия

Если при применении Центр обновления Windows возникли проблемы, см. раздел [VM не отвечает на ошибку "C01A001D" при применении центр обновления Windows](./unresponsive-vm-apply-windows-update.md).
