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
ms.openlocfilehash: 4263afe33caa4d6471848c8e7dbf9bc1eeec4bee
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332522"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>При запуске виртуальная машина зависла на сообщении "Подготовка Windows. Не выключайте компьютер" в Azure

В этой статье описываются экраны "подготовка" и "Подготовка Windows", которые могут возникнуть при загрузке виртуальной машины Windows в Microsoft Azure. Представляем шаги, которые помогут вам при сборе данных для запроса в службу поддержки.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Симптомы

Виртуальная машина Windows не загружается. При использовании **диагностики загрузки** для получения снимка экрана виртуальной машины вы можете увидеть, что виртуальная машина отобразит сообщение "подготовка" или "Подготовка Windows".

![Пример сообщения для Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Пример сообщения](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Причина:

Обычно эта проблема возникает, когда сервер выполняет окончательную перезагрузку после изменения конфигурации. Изменение конфигурации может быть инициализировано обновлениями Windows либо изменением ролей или функций сервера. Если размер обновления, устанавливаемого в Центре обновления Windows, большой, операционной системе потребуется больше времени, чтобы перенастроить изменения.

## <a name="collect-an-os-memory-dump"></a>Сбор файла дампа памяти операционной системы

Если проблему не удается устранить после ожидания обработки изменений, необходимо будет получить файл дампа памяти и обратиться в службу поддержки. Чтобы собрать файл дампа, выполните следующие действия.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. Сделайте снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).
2. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](../windows/troubleshoot-recovery-disks-portal.md).
3. Подключитесь по протоколу удаленного рабочего стола к виртуальной машине восстановления. 
4. Если диск ОС зашифрован, необходимо отключить шифрование перед переходом к следующему шагу. Дополнительные сведения см. в разделе [расшифровка зашифрованного диска ОС в виртуальной машине, которая не может быть загружена](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Найдите файл дампа и отправьте запрос в службу поддержки

1. На виртуальной машине восстановления в подключенном диске ОС перейдите в папку Windows. Если подключенному диску ОС присвоена буква F, то необходимо перейти в F:\Windows.
2. Найдите файл Memory. dmp и отправьте запрос в [службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом дампа. 

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
4. Запустите виртуальную машину и получите доступ к последовательной консоли.
5. Выберите параметр **Отправить немаскируемое прерывание (NMI)** , чтобы активировать дамп памяти.
    @no__t образом 0the, куда отправить немаскируемое прерывание @ no__t-1
6. Снова подключите диск операционной системы к виртуальной машине восстановления и собирайте файл дампа.

## <a name="contact-microsoft-support"></a>Обратиться в службу поддержки Майкрософт

После сбора файла дампа обратитесь в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы проанализировать первопричину.