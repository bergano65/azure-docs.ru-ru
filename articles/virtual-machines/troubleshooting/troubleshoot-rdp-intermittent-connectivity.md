---
title: Частый разрыв подключения к удаленному рабочему столу на виртуальной машине Azure | Документация Майкрософт
description: Сведения об устранении проблем с частым разрывом подключений к удаленному рабочему столу на виртуальной машине Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: eec52fb2342ee85932a6e24aa4cb292496beccad
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087305"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Частый разрыв подключения к удаленному рабочему столу на виртуальной машине Azure

В этой статье объясняется, как устранять проблемы с частым разрывом подключений по протоколу удаленного рабочего стола на виртуальной машине Azure.


## <a name="symptom"></a>Симптом

Во время сеансов работы иногда возникают проблемы с подключением по протоколу удаленного рабочего стола (RDP). Сначала подключение к виртуальной машине выполняется нормально, но затем разрывается.

## <a name="cause"></a>Причина:

Это может происходить при неправильной настройке прослушивателя RDP. Чаще всего эта проблема возникает на виртуальных машинах, использующих пользовательские образы.

## <a name="solution"></a>Решение

Прежде чем выполнять эти действия, [Создайте моментальный снимок диска операционной системы](../windows/snapshot-copy-managed-disk.md) затронутой виртуальной машины в качестве резервной копии. 

Чтобы устранить эту проблему, воспользуйтесь последовательной консолью или [автономно восстановите виртуальную машину](#repair-the-vm-offline), присоединив диск ОС виртуальной машины к виртуальной машине восстановления.

### <a name="serial-control"></a>Использование последовательной консоли

1. Подключитесь к [последовательной консоли и откройте экземпляр командной строки](./serial-console-windows.md). Теперь выполните следующие команды, чтобы сбросить настройки удаленного рабочего стола. Если последовательная консоль на нужной виртуальной машине не включена, перейдите к следующему шагу.
2. Понизьте уровень защиты RDP до значения 0. На этом уровне для обмена данными между сервером и клиентом используется собственное шифрование RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

3. Понижение уровня шифрования до минимального позволит подключиться устаревшим клиентам RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

4. Также настройте для RDP загрузку конфигурации с клиентского компьютера.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

5. Включите параметр проверки активности для RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

6. Настройте параметр повторного подключения для RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

7. Настройте параметр времени сеанса для RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

8. Настройте параметр времени отключения для RDP. 

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f` `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

9. Настройте параметр времени подключения для RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

10. Настройте параметр отключения при неактивности для RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f`

11. Настройте параметр "Limit the maximum concurrent connections" (Ограничить максимальное число одновременных подключений):

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f`

12. Перезапустите виртуальную машину и снова попробуйте подключиться по протоколу удаленного рабочего стола.

### <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

1. [Подключите диск операционной системы к виртуальной машине восстановления](../windows/troubleshoot-recovery-disks-portal.md).
2. После подключения диска ОС к виртуальной машине восстановления убедитесь, что в консоли управления дисками он помечен как **В сети**. Запишите или запомните букву диска, которая присвоена подключенному диску ОС.
3. На подключенном диске операционной системы перейдите в папку **\windows\system32\config**. Создайте резервную копию всех файлов в этой папке на случай, если потребуется выполнить откат.
4. Запустите редактор реестра (файл regedit.exe).
5. Выберите раздел **HKEY_LOCAL_MACHINE**. В меню выберите **файл**  >  **Загрузить куст**:
6. Перейдите в папку **\windows\system32\config\SYSTEM** на подключенном диске ОС. Для имени куста введите **BROKENSYSTEM**. В разделе **HKEY_LOCAL_MACHINE** появится новый куст реестра. Затем загрузите куст программного обеспечения **\windows\system32\config\SOFTWARE** в раздел **HKEY_LOCAL_MACHINE**. Для имени программного обеспечения куста введите **BROKENSOFTWARE**. 
7. Откройте окно командной строки с повышенными привилегиями (**Запуск от имени администратора**) и выполните оставшиеся действия этой процедуры, чтобы сбросить конфигурации RDP. 
8. Понизьте уровень защиты RDP до значения 0, чтобы при обмене данными между сервером и клиентом использовалось собственное шифрование RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

9. Понижение уровня шифрования до минимального позволит подключиться устаревшим клиентам RDP.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

10. Также настройте для RDP загрузку конфигурации с клиентского компьютера.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

11. Включите параметр проверки активности для RDP.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

12. Настройте параметр повторного подключения для RDP.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

13. Настройте параметр времени сеанса для RDP.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

14. Настройте параметр времени отключения для RDP.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

15. Настройте параметр времени подключения для RDP.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

16. Настройте параметр отключения при неактивности для RDP.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

17. Настройте параметр "Limit the maximum concurrent connections" (Ограничить максимальное число одновременных подключений):

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

18. Перезапустите виртуальную машину и снова попробуйте подключиться по протоколу удаленного рабочего стола.

## <a name="need-help"></a>Требуется помощь? 
Обратитесь в службу поддержки. Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.





