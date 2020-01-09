---
title: Перезагрузка виртуальных машин Azure зависает при перезагрузке, завершении работы или остановке служб | Документация Майкрософт
description: Эта статья поможет вам устранить ошибки службы в Виртуальные машины Windows Azure.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: db7b26402170236843891799738088b9229e4693
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477412"
---
# <a name="azure-windows-vm-restart-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Перезапуск виртуальной машины Windows в Azure зависает при "перезапуске", "выключении" или "остановке служб"

В этой статье приводятся инструкции по устранению проблем с перезапуском, завершением работы или остановкой служб, которые могут возникнуть при перезагрузке виртуальной машины Windows (ВМ) в Microsoft Azure.

## <a name="symptoms"></a>Симптомы

При использовании [диагностики загрузки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра снимка экрана виртуальной машины может отобразиться, что на снимке экрана отображается сообщение "перезагрузка", "завершение работы" или "остановка служб".

![Перезапуск, завершение работы и остановка экранов служб](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Причина

Windows использует процесс завершения работы для выполнения операций обслуживания системы и обработки изменений, таких как обновления, роли и компоненты. Не рекомендуется прерывать этот критический процесс до его завершения. Процесс может занять длительное время в зависимости от количества обновлений, изменений и размера виртуальной машины. Если процесс остановлен, ОС может стать поврежденной. Прерывать процесс следует только в том случае, если он занимает слишком много времени.

## <a name="solution"></a>Решение

### <a name="collect-a-process-memory-dump"></a>Получение дампа памяти процесса

1. Скачайте [средство ProcDump](http://download.sysinternals.com/files/Procdump.zip) в новый или существующий диск данных, подключенный к рабочей виртуальной машине из того же региона.

2. Отключите диск, содержащий файлы, необходимые для рабочей виртуальной машины, и подключите диск к поврежденной виртуальной машине. Этот диск вызывается на **диске служебной программы**.

Используйте [последовательную консоль](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) для выполнения следующих действий.

1. Откройте административный PowerShell и проверьте службу, которая зависла при остановке.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. На административном CMD получите идентификатор зависла службы.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Получите образец дампа памяти из <STOPPING SERVICE>процесса зависания.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Теперь прервать зависание процесса, чтобы разблокировать процесс завершения работы.

   ``
   taskkill /PID <PID> /t /f
   ``

После того, как операционная система запустится снова, если она загружается нормально, просто обеспечьте согласованность операционной системы. В случае повреждения выводится следующая команда, пока диск не будет поврежден:

``
dism /online /cleanup-image /restorehealth
``

Если не удается собрать дамп памяти процесса или эта проблема является рекурсивной, и вам требуется анализ основных причин, продолжите сбор дампа памяти ОС ниже, перейдите к открытию запроса в службу поддержки.

### <a name="collect-an-os-memory-dump"></a>Сбор файла дампа памяти операционной системы

Если проблему не удается устранить после ожидания обработки изменений, необходимо будет получить файл дампа памяти и обратиться в службу поддержки. Чтобы собрать файл дампа, выполните следующие действия.

**Подключение диска ОС к виртуальной машине восстановления**

1. Сделайте снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Подключитесь по протоколу удаленного рабочего стола к виртуальной машине восстановления.

4. Если диск ОС зашифрован, необходимо отключить шифрование перед переходом к следующему шагу. Дополнительные сведения см. в разделе [расшифровка зашифрованного диска ОС в виртуальной машине, которая не может быть загружена](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution).

**Обнаружение файла дампа и отправка запроса в службу поддержки**

1. На виртуальной машине восстановления в подключенном диске ОС перейдите в папку Windows. Если подключенному диску ОС присвоена буква F, то необходимо перейти в F:\Windows.

2. Найдите файл Memory. dmp и отправьте запрос в [службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом дампа.

Если не удается найти файл дампа, перейдите на следующий шаг для включения журнала дампа и последовательной консоли.

**Включение журнала дампа и последовательной консоли**

Чтобы включить журнал дампа и последовательную консоль, выполните следующий сценарий.

1. Откройте сеанс командной строки с повышенными привилегиями (выполнив запуск от имени администратора).

2. Выполните следующий скрипт:

   В этом сценарии предполагается, что буква диска, назначенная подключенному диску ОС, — F. Замените ее на соответствующее значение в виртуальной машине.

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Убедитесь, что на диске достаточно места, чтобы выделить объем памяти, который зависит от размера, выбранного для этой виртуальной машины.

4. Если недостаточно места или виртуальная машина имеет большой размер (G, GS или E), можно изменить расположение, в котором будет создан этот файл, а также ссылаться на любой другой диск данных, подключенный к виртуальной машине. Чтобы изменить расположение, необходимо изменить следующий ключ:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Отключите диск операционной системы, а затем повторно подключите диск операционной системы к затронутой виртуальной машине](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Запустите виртуальную машину и получите доступ к последовательной консоли.

7. Выберите параметр Отправить немаскируемое прерывание (NMI), чтобы активировать дамп памяти.

   ![Отправить немаскируемое прерывание](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Снова подключите диск операционной системы к виртуальной машине восстановления и собирайте файл дампа.

## <a name="contact-microsoft-support"></a>Обратиться в службу поддержки Майкрософт

После того как файл дампа будет собран, обратитесь в службу поддержки Майкрософт, чтобы определить основную причину.
