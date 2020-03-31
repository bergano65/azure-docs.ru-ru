---
title: При запуске виртуальная машина зависла на сообщении "Подготовка Windows. Не выключайте компьютер" в Azure | Документация Майкрософт
description: Здесь описаны шаги по решению проблемы, при которой во время запуска виртуальная машина зависает на сообщении "Подготовка Windows. Не выключайте компьютер".
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: da45e24898bc3b5aead250077af69a61bdb33bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749639"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>При запуске виртуальная машина зависла на сообщении "Подготовка Windows. Не выключайте компьютер" в Azure

В этой статье описаны экраны «Готовимся» и «Подготовка Windows», с которыми можно столкнуться при загрузке виртуальной машины Windows (VM) в Microsoft Azure. Представляем шаги, которые помогут вам при сборе данных для запроса в службу поддержки.

 

## <a name="symptoms"></a>Симптомы

Windows VM не загружается. При **использовании диагностики Boot** для получения скриншота VM, вы можете увидеть, что VM отображает сообщение "Готовимся" или "Подготовка Windows".

![Пример сообщения для Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Пример сообщения](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Причина

Обычно эта проблема возникает, когда сервер выполняет окончательную перезагрузку после изменения конфигурации. Изменение конфигурации может быть инициализировано обновлениями Windows либо изменением ролей или функций сервера. Если размер обновления, устанавливаемого в Центре обновления Windows, большой, операционной системе потребуется больше времени, чтобы перенастроить изменения.

## <a name="collect-an-os-memory-dump"></a>Сбор файла дампа памяти операционной системы

Если проблема не будет решена после ожидания изменений в процессе, необходимо собрать файл свалки памяти и связаться с поддержкой. Чтобы собрать файл дампа, выполните следующие действия.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. Сделайте снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).
2. [Прикрепите диск ОС к восстановлению VM.](../windows/troubleshoot-recovery-disks-portal.md)
3. Подключитесь по протоколу удаленного рабочего стола к виртуальной машине восстановления. 
4. Если диск ОС зашифрован, необходимо отключить шифрование перед переходом к следующему шагу. Для получения дополнительной информации, [см Расшифровать зашифрованный диск ОС в VM, которые не могут загрузиться.](troubleshoot-bitlocker-boot-error.md#solution)

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Найдите файл дампа и отправьте запрос в службу поддержки

1. На виртуальной машине восстановления в подключенном диске ОС перейдите в папку Windows. Если подключенному диску ОС присвоена буква F, то необходимо перейти в F:\Windows.
2. Найдите файл memory.dmp, а затем [отправьте билет поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом дампа. 

Если не удается найти файл дампа, перейдите на следующий шаг для включения журнала дампа и последовательной консоли.

### <a name="enable-dump-log-and-serial-console"></a>Включение журнала дампа и последовательной консоли

Чтобы включить журнал дампа и последовательную консоль, выполните следующий сценарий.

1. Откройте сеанс командной строки с повышенными привилегиями (выполнив запуск от имени администратора).
2. Выполните следующий скрипт:

    В этом сценарии мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением на своей виртуальной машине.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Убедитесь, что на диске достаточно места для выделения объема памяти, соответствующего объему ОЗУ, который зависит от выбираемого вами размера для виртуальной машины.
    2. Если места недостаточно или используется виртуальная машина большого размера (серии G, GS или E), то можно затем изменить расположение создания этого файла и указать любой другой диск данных, который присоединен к виртуальной машине. Для этого необходимо изменить следующий раздел.

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Отсоедините диск ОС и снова подключите его к необходимой виртуальной машине](../windows/troubleshoot-recovery-disks-portal.md).
4. Запустите VM и получите доступ к серийной консоли.
5. Выберите **Отправить немаскируемый прерывание (NMI),** чтобы вызвать дамп памяти.
    ![изображение о том, куда отправить немаскируемый прерывание](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Прикрепите диск ОС к восстановлению VM снова, соберите файл свалки.

## <a name="contact-microsoft-support"></a>Обратиться в службу поддержки Майкрософт

После сбора файла дампа обратитесь в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы проанализировать первопричину.