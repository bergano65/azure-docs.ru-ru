---
title: Сбой критически важной службы при загрузке виртуальной машины Azure | Документация Майкрософт
description: Узнайте, как устранить ошибку сбоя критически важной службы с кодом 0x0000005A, которая происходит при загрузке | Документация Майкрософт
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: e828a8fc4211a0f0c4b53a9e18fa1c2fb6f6916b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593225"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows отображает ошибку "Сбой критически важной службы" на синем экране при загрузке виртуальной машины Azure
В этой статье описывается ошибка "Сбой критически важной службы", которая может возникнуть при загрузке виртуальной машины Windows в Microsoft Azure. Здесь приведены действия по устранению неполадок. 

> [!NOTE] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель развертывания с помощью Resource Manager и классическая модель](../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для новых развертываний рекомендуется использовать эту модель вместо классической.

## <a name="symptom"></a>Симптом 

Виртуальная машина Windows не запускается. При проверке снимков экрана загрузки в окне [Диагностика загрузки](./boot-diagnostics.md) появляется одно из следующих сообщений об ошибке на синем экране:

- "На вашем ПК возникла проблема, и его необходимо перезагрузить. Вы можете выполнить перезапуск. Дополнительные сведения о данной проблеме и возможных исправлениях см. по адресу http://windows.com/stopcode. Если вы обращаетесь к специалисту службы поддержки, предоставьте следующие сведения. Код остановки: CRITICAL SERVICE FAILED" 
- "На вашем ПК возникла проблема, и его необходимо перезагрузить. Мы лишь собираем некоторые сведения об ошибке, а затем выполним перезагрузку. Если вы хотите узнать больше, можете позже поискать в Интернете сведения об этой ошибке: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Причина:

Существуют различные причины возникновения STOP-ошибок. Ниже перечислены наиболее распространенные из них.
- Проблема с драйвером.
- Поврежденный системный файл или память.
- Приложение обращается к запрещенному сектору памяти.

## <a name="solution"></a>Решение 

Чтобы устранить эту проблему, [обратитесь в службу поддержки и отправьте файл дампа](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), который поможет нам быстрее диагностировать проблему, или воспользуйтесь следующим решением для самостоятельного устранения.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. Сделайте снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).
2. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](./troubleshoot-recovery-disks-portal-windows.md). 
3. Установите подключение удаленного рабочего стола к виртуальной машине восстановления.

### <a name="enable-dump-logs-and-serial-console"></a>Включение журналов дампа и последовательной консоли

Журнал дампа и [последовательная консоль](./serial-console-windows.md) помогут нам в дальнейшем устранении неполадок.

Чтобы включить журналы дампа и последовательную консоль, выполните следующий сценарий.

1. Откройте сеанс командной строки с повышенными привилегиями (выполнив запуск от имени администратора).
2. Выполните следующий скрипт:

    В этом сценарии мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением на своей виртуальной машине.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Замена неподписанных драйверов

1. На виртуальной машине восстановления выполните следующую команду в командной строке с повышенными привилегиями. Эта команда настраивает запуск поврежденного диска ОС в безопасном режиме при следующей загрузке :

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Например, если присоединенный диск ОС является диском F, выполните следующую команду:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Отсоедините диск ОС и снова подключите его к необходимой виртуальной машине](troubleshoot-recovery-disks-portal-windows.md). Виртуальная машина загрузится в безопасном режиме. Если ошибка по-прежнему возникает, перейдите к дополнительному шагу.
3. Откройте поле **Выполнить** и запустите **средство проверки**, чтобы запустить диспетчер проверки драйверов.
4. Выберите **Автоматически выбирать неподписанные драйверы**, а затем нажмите кнопку **Далее**.
5. Вы получите список файлов драйверов без подписи. Запомните имена файлов.
6. Скопируйте те же версии этих файлов из рабочей виртуальной машины, а затем замените эти файлы без подписи. 

7. Удалите параметры безопасной загрузки:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Перезапустите виртуальную машину. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Необязательно: анализ журналов дампа в режиме аварийного завершения дампа

Чтобы проанализировать журналы дампа самостоятельно, выполните следующие действия:

1. Подключите диск ОС к виртуальной машине для восстановления.
2. В подключенном диске операционной системы перейдите в папку **\windows\system32\config**. Скопируйте все файлы как файл резервной копии. Это необходимо, если потребуется откат.
3. Запустите **редактор реестра** (файл regedit.exe).
4. Выберите раздел **HKEY_LOCAL_MACHINE**. Выберите в меню **Файл** > **Загрузка куста реестра**.
5. В подключенном диске операционной системы перейдите в системную папку **\windows\system32\config\SYSTEM**. Для имени куста введите **BROKENSYSTEM**. В разделе **HKEY_LOCAL_MACHINE** появится новый куст реестра.
6. Перейдите к **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** и внесите следующие изменения:

    Autoreboot = 0.

    CrashDumpEnabled = 2.
7.  Выберите **BROKENSYSTEM**. Выберите в меню **Файл** > **Выгрузить куст**.
8.  Измените установку BCD для загрузки в режиме отладки. Выполните следующие команды в командной строке с повышенными привилегиями.

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Отсоедините диск ОС и снова подключите его к необходимой виртуальной машине](troubleshoot-recovery-disks-portal-windows.md).
10. Загрузите виртуальную машину, чтобы увидеть, показывает ли она анализ дампа. Найдите файл, который не удается загрузить. Необходимо заменить его файлом рабочей виртуальной машины. 

    Ниже приведен пример анализа дампа. Можно заметить, что **FAILURE** относится к файлу filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Когда виртуальная машина будет работать и загружаться нормально, удалите параметры аварийного завершения дампа:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
