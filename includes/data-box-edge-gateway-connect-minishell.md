---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67185749"
---
В зависимости от операционной системы клиента процедуры удаленного подключения к устройству различны.

### <a name="remotely-connect-from-a-windows-client"></a>Удаленное подключение от клиента Windows

Прежде чем начать, убедитесь, что ваш клиент Windows работает под управлением Windows PowerShell 5.0 или позже.

Выполните следующие действия, чтобы удаленно подключиться к клиенту Windows.

1. Запустите сеанс Windows PowerShell в качестве администратора.
2. Убедитесь, что служба удаленного управления Windows работает на вашем клиенте. В командной строке введите:

    `winrm quickconfig`

3. Назначить переменную на IP-адрес устройства.

    $ip " <device_ip>"

    Замените `<device_ip>` IP-адрес вашего устройства.

4. Чтобы добавить IP-адрес вашего устройства в список доверенных хостов клиента, введите следующую команду:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Запустите сеанс Windows PowerShell на устройстве:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Предоставьте пароль по запросу. Используйте тот же пароль, который используется для входного в локальном веб-ui. Локальный пароль веб-uI по умолчанию — *пароль 1.* При успешном подключении к устройству с помощью удаленного PowerShell вы увидите следующий выход образца:  

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

### <a name="remotely-connect-from-a-linux-client"></a>Удаленное подключение от клиента Linux

На клиенте Linux, который вы будете использовать для подключения:

- [Установите новейшее ядро PowerShell для Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) от GitHub, чтобы получить функцию удаления SSH. 
- [Установите `gss-ntlmssp` только пакет из модуля NTLM.](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md) Для клиентов Ubuntu используйте следующую команду:
    - `sudo apt-get install gss-ntlmssp`

Для получения дополнительной информации, перейдите на [PowerShell remoting над SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Выполните следующие действия, чтобы удаленно подключиться к клиенту NFS.

1. Чтобы открыть сеанс PowerShell, введите:

    `sudo pwsh`
 
2. Для подключения с помощью удаленного клиента введите:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    При запросе предоставьте пароль, используемый для ввоза в устройство.
 
> [!NOTE]
> Эта процедура не работает на Mac OS.
