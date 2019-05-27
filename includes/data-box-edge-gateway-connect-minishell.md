---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161157"
---
В зависимости от операционной системы клиента отличаются процедур удаленно подключиться к устройству.

### <a name="remotely-connect-from-a-windows-client"></a>Удаленное подключение из клиента Windows

Перед началом работы убедитесь, что клиент Windows работает с Windows PowerShell 5.0 или более поздней версии.

Выполните следующие действия для удаленного подключения от клиента Windows.

1. Запустите сеанс Windows PowerShell с правами администратора.
2. Убедитесь, что служба удаленного управления Windows работает на клиентском компьютере. В командной строке выполните следующую команду:

    `winrm quickconfig`

3. Назначьте переменную для IP-адрес устройства.

    $ip = "<device_ip>"

    Замените `<device_ip>` IP-адресом вашего устройства.

4. Чтобы добавить IP-адрес устройства к списку доверенных узлов клиента, введите следующую команду:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Запустите сеанс Windows PowerShell на устройстве:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Укажите пароль при появлении запроса. Используйте тот же пароль, который используется для входа в локальный пользовательский Интерфейс. Пароль по умолчанию локального web UI *Password1*. При подключении к устройству с помощью удаленного PowerShell успешно, отобразится следующий результат:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Удаленное подключение из клиента Linux

В клиенте Linux, который будет использоваться для подключения:

- [Установить последнюю PowerShell Core для Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) из GitHub, чтобы получить функции удаленного взаимодействия SSH. 
- [Установить только `gss-ntlmssp` пакета из модуля NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Для клиентов, Ubuntu используйте следующую команду:
    - `sudo apt-get install gss-ntlmssp`

Дополнительные сведения см. в статье [удаленное взаимодействие PowerShell через SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Выполните следующие действия для удаленного подключения от NFS-клиента.

1. Чтобы открыть сеанс PowerShell, введите следующую команду:

    `sudo pwsh`
 
2. Для подключения с помощью удаленного клиента, введите следующую команду:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    При запросе укажите пароль, используемый для входа в устройство.
 
> [!NOTE]
> Эта процедура не работает в Mac OS.
