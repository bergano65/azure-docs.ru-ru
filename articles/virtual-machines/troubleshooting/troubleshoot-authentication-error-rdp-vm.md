---
title: Устранение ошибок аутентификации при подключении к виртуальной машине Azure по протоколу RDP | Документация Майкрософт
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 5f9d933f0f294855719c8d187e759708ef8c9931
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669611"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Устранение ошибок аутентификации при подключении к виртуальной машине Azure по протоколу RDP

Эта статья поможет устранить ошибки аутентификации, возникающие при использовании подключения по протоколу удаленного рабочего стола (RDP) к виртуальной машине Azure.

## <a name="symptoms"></a>Проблемы

Вы создали снимок экрана виртуальной машины Azure, на котором виден экран приветствия и все указывает на то, что операционная система запущена. Тем не менее при попытке подключиться к удаленному рабочему столу виртуальной машины появляется одно из следующих сообщений об ошибке.

### <a name="error-message-1"></a>Сообщение об ошибке 1

**An authentication error has occurred. The Local Security Authority cannot be contacted** (Произошла ошибка аутентификации. Не удается связаться с локальным администратором безопасности).

### <a name="error-message-2"></a>Сообщение об ошибке 2

**The remote computer that you are trying to connect to requires Network Level Authentication (NLA), but your Windows domain controller cannot be contacted to perform NLA. If you are an administrator on the remote computer, you can disable NLA by using the options on the Remote tab of the System Properties dialog box** (Для подключения к удаленному компьютеру требуется проверка подлинности на уровне сети, однако не удалось подключиться к контроллеру домена Windows для ее выполнения. Если вы являетесь администратором удаленного компьютера, то можете отключить проверку подлинности на уровне сети, воспользовавшись параметрами на вкладке "Удаленные" диалогового окна "Свойства системы").

### <a name="error-message-3-generic-connection-error"></a>Сообщение об ошибке 3 (универсальная ошибка подключения)

**This computer can't connect to the remote computer. Try connecting again, if the problem continues, contact the owner of the remote computer or your network administrator** (Не удается подключиться к удаленному компьютеру. Попробуйте подключиться снова. Если проблема не исчезнет, ​​обратитесь к владельцу удаленного компьютера или к администратору сети).

## <a name="cause"></a>Причина:

Существует несколько причин, по которым проверка подлинности на уровне сети (NLA) может блокировать доступ по протоколу RDP к виртуальной машине.

### <a name="cause-1"></a>Причина 1

Виртуальная машина не может обмениваться данными с контроллером домена. Эта проблема может помешать сеансу RDP обращаться к виртуальной машине с помощью учетных данных домена. Тем не менее вы можете войти в систему, используя учетные данные локального администратора. Эта проблема может возникнуть в следующих ситуациях.

1. Нарушен защищенный канал Active Directory между этой виртуальной машиной и контроллером домена.

2. Виртуальная машина использует устаревшую копию пароля учетной записи, а контроллер домена использует новый пароль.

3. Контроллер домена, к которому подключается эта виртуальная машина, находится в неработоспособном состоянии.

### <a name="cause-2"></a>Причина 2

Уровень шифрования виртуальной машины выше уровня, который используется клиентским компьютером.

### <a name="cause-3"></a>Причина 3

На виртуальной машине отключены протоколы TLS 1.0, 1.1 или 1.2 (сервер).

### <a name="cause-4"></a>Причина 4

На виртуальной машине было отключено ведение журнала с помощью учетных данных домена, и локальная система безопасности настроена неправильно.

### <a name="cause-5"></a>Причина 5

Виртуальная машина была настроена для принятия только подключений, использующих алгоритмы, соответствующие Федеральному стандарту обработки информации (FIPS). Обычно для этого используются политика Active Directory. Эта конфигурация встречается редко, но FIPS можно настроить для использования только подключений к удаленному рабочему столу.

## <a name="before-you-troubleshoot"></a>Перед устранением неполадок

### <a name="create-a-backup-snapshot"></a>Создание моментального снимка резервной копии

Чтобы создать моментальный снимок резервной копии, выполните действия, описанные в статье [Создание моментального снимка](..\windows\snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Удаленный вход на виртуальную машину

Чтобы удаленно подключиться к виртуальной машине, используйте один из методов, описанных в статье [Use remote tools to troubleshoot Azure VM issues](remote-tools-troubleshoot-azure-vm-issues.md) (Использование удаленных средств для устранения проблем с виртуальными машинами Azure).

### <a name="group-policy-client-service"></a>Службы клиента групповой политики

Если это виртуальная машина, присоединенная к домену, необходимо сначала остановить службу клиента групповой политики, чтобы предотвратить перезапись изменений какой-либо политикой Active Directory. Для этого выполните следующую команду:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

После устранения проблемы восстановите возможность виртуальной машины взаимодействовать с доменом, чтобы получить последний объект групповой политики из домена. Для этого выполните следующие команды.

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Если изменение отменено, это означает, что проблема вызвана политикой Active Directory. 

### <a name="workaround"></a>Возможное решение

Чтобы обойти эту проблему, выполните следующие команды в командном окне, чтобы отключить NLA.

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Затем перезапустите виртуальную машину.

Чтобы снова включить NLA, выполните приведенную ниже команду, а затем перезапустите виртуальную машину.

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="for-domain-joined-vms"></a>Для виртуальных машин, присоединенных к домену

Чтобы устранить эту проблему, сначала проверьте, может ли виртуальная машина подключиться к контроллеру домена и находится ли контроллер домена в работоспособном состоянии, позволяющем обрабатывать запросы от виртуальной машины.

>[!Note] 
>Чтобы проверить работоспособность контроллера домена, можно использовать другую виртуальную машину в той же одной виртуальной сети и подсети, которая использует тот же сервер входа в систему.

Подключитесь к виртуальной машине, на которой возникла проблема, с помощью Серийной консоли, удаленного сеанса командной строки или удаленного сеанса PowerShell. Следуйте инструкциям в разделе "Удаленный вход на виртуальную машину".

Чтобы определить, к которому контроллеру домена подключается виртуальная машина, выполните в консоли следующую команду. 

```cmd
set | find /i "LOGONSERVER"
```

Затем проверьте работоспособность защищенного канала между виртуальной машиной и контроллером домена. Для этого в экземпляре сеанса PowerShell с повышенными привилегиями выполните следующую команду. Эта команда возвращает логический флаг, который указывает, активен ли защищенный канал.

```powershell
Test-ComputerSecureChannel -verbose
```

Если канал не работает, выполните следующую команду для его восстановления.

```powershell
Test-ComputerSecureChannel -repair
```

Убедитесь, что пароль учетной записи компьютера в Active Directory обновлен в виртуальной машине и контроллере домена.

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Если контроллер домена и виртуальная машина успешно взаимодействуют, но контроллер домена недостаточно работоспособен, чтобы запустить сеанс RDP, можно попытаться перезапустить контроллер домена.

Если указанные выше команды не привели к устранению неполадки связи с доменом, можно повторно присоединить эту виртуальную машину к домену. Для этого выполните следующие действия.

1. Создайте сценарий Unjoin.ps1, используя приведенное ниже содержимое. Затем разверните этот сценарий как расширение пользовательских сценариев на портале Azure.

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Этот сценарий принудительно удаляет виртуальную машину из домена и перезапускает его через 10 секунд. Затем нужно очистить объект-компьютер на стороне домена.

2.  После завершения очистки вновь присоедините эту виртуальную машину к домену. Для этого создайте сценарий JoinDomain.ps1, используя приведенное ниже содержимое. Затем разверните этот сценарий как расширение пользовательских сценариев на портале Azure. 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Виртуальная машина присоединится к домену с помощью указанных учетных данных.

Если канал Active Directory находится в работоспособном состоянии, пароль компьютера обновлен, а контроллер домена работает правильно, выполните следующие действия.

Если проблема не исчезла, проверьте, не отключены ли учетные данные домена. Для этого откройте окно командной строки с повышенными привилегиями и выполните следующую команду, чтобы определить, отключены ли на виртуальной машине учетные записи домена для входа на эту виртуальную машину.

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Если задано значение раздела **1**, это означает, что на сервере запрещены учетные данные домена. Измените значение раздела на **0**.

### <a name="for-standalone-vms"></a>Для изолированных виртуальных машин

#### <a name="check-minencryptionlevel"></a>Проверка значения MinEncryptionLevel

В экземпляре командной строки выполните следующую команду, чтобы запросить значение реестра **MinEncryptionLevel**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Исходя из значения реестра, выполните следующие действия.

* 4 (FIPS): перейдите к разделу [Проверка подключений, использующих FIPS-совместимые алгоритмы](#fips-compliant).

* 3 (128-разрядное шифрование): задайте уровень серьезности **2**, выполнив приведенную ниже команду.

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (максимально возможный уровень шифрования в соответствии с клиентом): можно попытаться установить минимальный уровень шифрования **1**, выполнив приведенную ниже команду.

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Чтобы изменения реестра вступили в силу, перезапустите виртуальную машину.

#### <a name="tls-version"></a>Версия протокола TLS

В зависимости от системы протокол RDP использует протокол TLS 1.0, 1.1 или 1.2 (сервер). Чтобы запросить параметры этих протоколов, настроенные на виртуальной машине, откройте экземпляр командной строки и выполните следующие команды.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Если не все возвращенные значения равны **1**, это означает, что протокол отключен. Чтобы включить эти протоколы, выполните следующие команды.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Для других версий протокола можно выполнить следующие команды.

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Версию x.x протоколов SSH и TLS можно получить из журналов гостевой ОС для ошибок SCHANNEL.

#### <a name="fips-compliant"></a> Проверка подключений, использующих FIPS-совместимые алгоритмы

Удаленный рабочий стол можно настроить для использования только подключений, использующих FIPS-совместимые алгоритмы. Это можно сделать, задав соответствующий раздел реестра. Для этого откройте окно командной строки с повышенными привилегиями и запросите следующие разделы.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Если команда возвращает **1**, измените значение реестра на **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Проверьте текущее значение MinEncryptionLevel виртуальной машины.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Если команда возвращает **4**, измените значение реестра на **2**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Чтобы изменения реестра вступили в силу, перезапустите виртуальную машину.

## <a name="next-steps"></a>Дополнительная информация

[Метод SetEncryptionLevel класса Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Настройка проверки подлинности сервера и уровней шифрования](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Класс Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
