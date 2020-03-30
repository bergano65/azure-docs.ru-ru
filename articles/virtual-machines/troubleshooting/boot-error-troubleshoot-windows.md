---
title: Отключение виртуальных машин Azure застряло при перезагрузке, отключении или остановке служб (ru) Документы Майкрософт
description: Эта статья поможет вам устранить ошибки службы в Azure Windows Виртуальные машины.
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
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371352"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Отключение Azure Windows VM застряло на "Перезагрузке", "Выключение", или "Остановка услуг"

В этой статье приводятся шаги для решения проблем "Перезагрузка", "Закрытие" или "Остановка служб", с которыми можно столкнуться при перезагрузке виртуальной машины Windows (VM) в Microsoft Azure.

## <a name="symptoms"></a>Симптомы

При [использовании диагностики Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра скриншота VM можно увидеть, что на скриншоте отображается сообщение "Перезагрузка", "Закрытие" или "Остановка служб".

![Перезапуск, отключение и остановка экранов служб](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Причина

Windows использует процесс выключения для выполнения операций по техническому обслуживанию системы и обработки изменений, таких как обновления, роли и функции. Не рекомендуется прерывать этот критический процесс до тех пор, пока он не завершится. В зависимости от количества обновлений/изменений и размера VM, процесс может занять много времени. Если процесс остановлен, оС возможно стать коррумпированным. Только прервать процесс, если он занимает слишком много времени.

## <a name="solution"></a>Решение

### <a name="collect-a-process-memory-dump"></a>Сбор свалки памяти процесса

1. Загрузите [инструмент Procdump](http://download.sysinternals.com/files/Procdump.zip) в новый или существующий диск данных, который крепится к работающей ВМ из того же региона.

2. Отсоедините диск, содержащий файлы, необходимые от работающего VM, и прикрепите диск к сломанному VM. Мы называем этот диск **утилита диска**.

Используйте [серийную консоль](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) для выполнения следующих шагов:

1. Откройте административную Powershell и проверьте службу, которая висела при остановке.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. На административном CMD, получить PID висела службы.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Получить образец свалки памяти от висела процесса <STOPPING SERVICE>.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Теперь убейте зависший процесс, чтобы разблокировать процесс выключения.

   ``
   taskkill /PID <PID> /t /f
   ``

Как только OS начинает снова, если оно загружает нормально, то как раз обеспечивает согласованность OS одобрено. Если сообщение о коррупции сообщается, запустите следующую команду до тех пор, пока диск не будет свободным от повреждения:

``
dism /online /cleanup-image /restorehealth
``

Если вы не можете собрать дамп памяти процесса, или эта проблема является рекурсивной, и вам требуется анализ первопричины, приступить к сбору памяти ОС дамб ниже, приступить к открытию запроса поддержки.

### <a name="collect-an-os-memory-dump"></a>Сбор файла дампа памяти операционной системы

Если проблема не будет решена после ожидания изменений в процессе, необходимо собрать файл свалки памяти и связаться с поддержкой. Чтобы собрать файл дампа, выполните следующие действия.

**Прикрепите диск ОС к восстановлению VM**

1. Сделайте снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Прикрепите диск ОС к восстановлению VM.](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

3. Подключитесь по протоколу удаленного рабочего стола к виртуальной машине восстановления.

4. Если диск ОС зашифрован, необходимо отключить шифрование перед переходом к следующему шагу. Для получения дополнительной информации, [см Расшифровать зашифрованный диск ОС в VM, которые не могут загрузиться.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)

**Найдите файл дампа и отправьте запрос в службу поддержки**

1. На виртуальной машине восстановления в подключенном диске ОС перейдите в папку Windows. Если подключенному диску ОС присвоена буква F, то необходимо перейти в F:\Windows.

2. Найдите файл memory.dmp, а затем [отправьте билет поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом дампа.

Если не удается найти файл дампа, перейдите на следующий шаг для включения журнала дампа и последовательной консоли.

**Включение журнала дампа и последовательной консоли**

Чтобы включить журнал дампа и последовательную консоль, выполните следующий сценарий.

1. Откройте сеанс командной строки с повышенными привилегиями (выполнив запуск от имени администратора).

2. Выполните следующий скрипт:

   В этом скрипте мы предполагаем, что приводная буква, назначенная прикрепленному к диску ОС, f. Заменить ее соответствующим значением в вашем VM.

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

3. Убедитесь, что на диске достаточно места для выделения столько памяти, сколько оперативной памяти, что зависит от размера, который вы выбираете для этого VM.

4. Если не хватает места или VM большой (G, GS или E серии), вы можете изменить место, где этот файл будет создан и направить, что на любой другой диск данных, который прилагается к VM. Чтобы изменить местоположение, необходимо изменить следующий ключ:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Отсоедините диск ОС, а затем прикрепите диск ОС к пораженному VM.](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

6. Запустите VM и получите доступ к серийной консоли.

7. Выберите Отправить немаскируемый прерывание (NMI), чтобы вызвать дамп памяти.

   ![Отправить немаскируемый прерывание](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Прикрепите диск ОС к восстановлению VM снова, соберите файл свалки.

## <a name="contact-microsoft-support"></a>Обратиться в службу поддержки Майкрософт

После сбора файла дампа обратитесь в службу поддержки корпорации Майкрософт, чтобы определить причину.
