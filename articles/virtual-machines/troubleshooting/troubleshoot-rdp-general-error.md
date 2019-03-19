---
title: Устранение общей ошибки RDP на виртуальной машине Windows в Azure | Документация Майкрософт
description: Как устранить общую ошибку RDP на виртуальной машине Windows в Azure | Документация Майкрософт
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f290a7e16938c66d45fab9b78086f77bfdfe4839
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100417"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Устранение общей ошибки RDP на виртуальной машине Azure

В этой статье описано общую ошибку, которая возникает при подключении протокола удаленного рабочего стола (RDP) к виртуальной машине Windows в Azure.

## <a name="symptom"></a>Симптом

При подключении RDP к виртуальной машине Windows в Azure может появиться сообщение об общей ошибке.

**Удаленному рабочему столу не удается подключиться к удаленному компьютеру по одной из следующих причин.**

1. **Удаленный доступ к серверу запрещен**.

2. **Удаленный компьютер выключен**.

3. **К удаленному компьютеру нет доступа в сети**.

**Убедитесь, что удаленный компьютер включен и подключен к сети, и удаленный доступ включен.**

## <a name="cause"></a>Причина:

Эта проблема возникает из-за перечисленных ниже причин.

### <a name="cause-1"></a>Причина 1

Компонент RDP отключен следующим образом:

- на уровне компонента;
- на уровне прослушивателя;
- на сервере терминалов;
- на роле узла сеансов удаленных рабочих столов.

### <a name="cause-2"></a>Причина 2

Службы удаленных рабочих столов (TermService) не запущены.

### <a name="cause-3"></a>Причина 3

Прослушиватель RDP-TCP настроен неправильно.

## <a name="solution"></a>Решение

Чтобы устранить эту проблему, [сделайте резервное копирование диска операционной системы](../windows/snapshot-copy-managed-disk.md) и [вложите диск операционной системы к виртуальной машине, которая приходит на помощь](troubleshoot-recovery-disks-portal-windows.md), а затем следуйте инструкциям.

### <a name="serial-console"></a>Серийная консоль

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Шаг 1. Открыть экземпляр командной строки в последовательной консоли

1. Откройте [последовательную консоль](serial-console-windows.md), выбрав **Поддержка и устранение неисправностей** > **Последовательная консоль (предварительная версия)**. Если эта функция включена на виртуальной машине, вы сможете успешно подключиться к виртуальной машине.

2. Создайте канал для экземпляра командной строки. Введите **CMD**, чтобы запустить канал и получить имя канала.

3. Переключитесь на канал, в котором выполняется экземпляр командной строки, в этом случае следует использовать канал 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Шаг 2. Проверьте значения разделов реестра протокола удаленного рабочего СТОЛА:

1. Проверьте, не отключен ли протокол RDP политиками.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Если существует политика домена, установка локальной политики будет перезаписана.
      - Если политика домена указывает, что RDP отключен (1), с контроллера домена обновите политику домена приложения.
      - Если политика домена утверждается, что RDP включен (0), то обновление не нужно.
      - Если политика домена не существует, и локальная политика утверждает, что RDP отключен (1), включите протокол RDP, используя следующую команду. 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Проверьте текущую конфигурацию сервера терминалов.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Если команда возвращает 0, сервер терминалов отключен. Затем включите сервер терминалов, как показано ниже.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Модуль сервера терминалов устанавливается в режим стока, если сервер выполняется на кластере серверов терминалов (RDS или Citrix). Проверьте текущий режим модуля сервера терминалов.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Если команда возвращает значение 1, модуль сервера терминалов устанавливается в режим стока. Затем установите модуль в рабочий режим следующим образом.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Проверьте подключения к серверу терминалов.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Если команда возвращает значение 1, к серверу терминалов невозможно подключиться. Затем включите подключение.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Проверьте текущую конфигурацию прослушивателя RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Если команда возвращает 0, прослушиватель отключен. Затем включите прослушиватель, как показано ниже.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Проверьте подключения к прослушивателю RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Если команда возвращает значение 1, к прослушивателю RDP невозможно подключиться. Затем включите подключение.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Перезапустите виртуальную машину.

8. Выйдите из экземпляра CMD: введите `exit`, а затем два раза нажмите клавишу **ВВОД**.

9. Перезапустите виртуальную машину, введя `restart`, а затем подключитесь к ней.

Если эта проблема не исчезает, перейдите к шагу 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Шаг 2. Включить службы удаленных рабочих столов

Дополнительные сведения см. в статье [Службы удаленных рабочих столов не запускаются на виртуальной машине Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Шаг 3. Сбросьте RDP-прослушиватель

Дополнительные сведения см. в статье [Частый разрыв подключения к удаленному рабочему столу на виртуальной машине Azure](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Автономные реплики

#### <a name="step-1-turn-on-remote-desktop"></a>Шаг 1. Включение удаленного рабочего стола

1. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](../windows/troubleshoot-recovery-disks-portal.md).
2. Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления.
3. Убедитесь, что в консоли "Управление дисками" подключенный диск имеет состояние **Подключен**. Запишите или запомните букву диска, которая присвоена подключенному диску ОС.
4. Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления.
5. Откройте сеанс командной строки с повышенными привилегиями (**запуск от имени администратора**). Выполните следующие сценарии. В этом сценарии мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением для своей виртуальной машины.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Если виртуальная машина присоединена к домену, проверьте следующий раздел реестра, чтобы увидеть, имеется ли групповая политика, которая приведет к отключению RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Если это значение ключа имеет значение 1, это означает, что RDP отключено политикой. Чтобы включить удаленный рабочий стол с помощью политики GPO, измените следующую политику с контроллера домена.

   
      **Конфигурация компьютера\Политики\Административные шаблоны:**

      Определения политик\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения\Разрешить пользователям удаленное подключение с использованием служб удаленных рабочих столов
  
1. Отсоедините диск от виртуальной машины спасения.
1. [Создайте новую виртуальную машину, используя диск](../windows/create-vm-specialized.md).

Если эта проблема не исчезает, перейдите к шагу 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Шаг 2. Включить службы удаленных рабочих столов

Дополнительные сведения см. в статье [Службы удаленных рабочих столов не запускаются на виртуальной машине Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Шаг 3. Сбросьте RDP-прослушиватель

Дополнительные сведения см. в статье [Частый разрыв подключения к удаленному рабочему столу на виртуальной машине Azure](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
