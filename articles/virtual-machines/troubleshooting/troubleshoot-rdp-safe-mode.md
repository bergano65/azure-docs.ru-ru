---
title: Сбой удаленного подключения к Виртуальным машинам Azure по причине загрузки виртуальной машины в безопасном режиме | Документация Майкрософт
description: Сведения об устранении проблемы, из-за которой не удается подключиться к виртуальной машине по протоколу RDP, так как она загружается в безопасном режиме.| Документация Майкрософт
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
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 3ff1db9ee7dc34ce529702d61b3ac5970bb5d9df
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309879"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Сбой подключения к виртуальной машине по протоколу RDP по причине загрузки виртуальной машины в безопасном режиме

В этой статье описывается, как устранить сбой подключения к Виртуальным машинам Windows Azure из-за загрузки виртуальной машины в безопасном режиме.

> [!NOTE] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для новых развертываний рекомендуется использовать эту модель вместо классической. 

## <a name="symptoms"></a>Проблемы 

Вы не можете установить подключение по протоколу RDP или другие подключения (например, по протоколу HTTP) к виртуальной машине в Azure, потому что виртуальная машина настроена на загрузку в безопасном режиме. При проверке снимка экрана в разделе [Диагностика загрузки](../troubleshooting/boot-diagnostics.md) на портале Azure вы увидите, что виртуальная машина загружается нормально, однако сетевой интерфейс недоступен:

![Изображение, где видно, что сетевой интерфейс находится в безопасном режиме](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Причина:

Служба RDP недоступна в безопасном режиме. При загрузке виртуальной машины в безопасном режиме загружаются только основные системные программы и службы. Это применимо к двум различным версиям безопасного режима: "Минимальная безопасная загрузка" и "Безопасная загрузка с возможностью подключения".


## <a name="solution"></a>Решение 

Прежде чем выполнять какие-либо действия, сделайте моментальный снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).

Чтобы устранить эту проблему, в последовательной консоли настройте виртуальную машину на загрузку в обычном режиме или [выполните ее автономное восстановление](#repair-the-vm-offline) с помощью виртуальной машины восстановления.

### <a name="use-serial-control"></a>Использование последовательной консоли

1. Подключитесь к [последовательной консоли и откройте экземпляр командной строки](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
). Если на виртуальной машине последовательная консоль не включена, перейдите к разделу [Автономное восстановление виртуальной машины](#repair-the-vm-offline).
2. Проверьте данные конфигурации загрузки: 

        bcdedit /enum

    Если виртуальная машина настроена на загрузку в безопасном режиме, вы увидите в разделе **загрузчика Windows** дополнительный параметр, который называется **safeboot**. Если параметр **safeboot** не отображается, виртуальная машина не находится в безопасном режиме. В таком случае эта статья не применима к вашему сценарию.

    Параметр **safeboot** может отображаться со следующими значениями:
    - Небольшие
    - Сеть

    В любом из этих двух режимов протокол RDP не запускается. Поэтому исправление будет одинаковым для обоих режимов.

    ![Изображение с параметром безопасного режима](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Удалите параметр **безопасного режима загрузки**, чтобы виртуальная машина загружалась в обычном режиме:

        bcdedit /deletevalue {current} safeboot
        
4. Проверьте данные конфигурации загрузки, чтобы убедиться, что параметр **safeboot** удален:

        bcdedit /enum

5. Перезапустите виртуальную машину и проверьте, устранена ли проблема.

### <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](../windows/troubleshoot-recovery-disks-portal.md).
2. Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления. 
3. Убедитесь, что в консоли "Управление дисками" подключенный диск имеет состояние **Подключен**. Запишите или запомните букву диска, которая присвоена подключенному диску ОС.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Включение журнала дампа и последовательной консоли (необязательно)

Журнал дампа и последовательная консоль помогут нам продолжить устранение неисправностей, если проблему не удалось решить с помощью сведений в этой статье.

Чтобы включить журнал дампа и последовательную консоль, выполните следующий сценарий.

1. Откройте сеанс командной строки с повышенными привилегиями (**запуск от имени администратора**).
2. Выполните следующий скрипт:

    В этом сценарии мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением для своей виртуальной машины.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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

#### Configure the Windows to boot into normal mode

1. Open an elevated command prompt session (**Run as administrator**).
2. Check the boot configuration data. In the following commands, we assume that the drive letter that is assigned to the attached OS disk is F. Replace this drive letter with the appropriate value for your VM. 

        bcdedit /store F:\boot\bcd /enum
    Take note of the Identifier name of the partition that has the **\windows** folder. By default, the  Identifier name is "Default".  

    If the VM is configured to boot into Safe Mode, you will see an extra flag under the **Windows Boot Loader** section called **safeboot**. If you do not see the **safeboot** flag, this article does not apply to your scenario.

    ![The image about boot Identifier](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Remove the **safeboot** flag, so the VM will boot into normal mode:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Check the boot configuration data to make sure that the **safeboot** flag is removed:

        bcdedit /store F:\boot\bcd /enum
5. [Detach the OS disk and recreate the VM](../windows/troubleshoot-recovery-disks-portal.md). Then check whether the issue is resolved.
