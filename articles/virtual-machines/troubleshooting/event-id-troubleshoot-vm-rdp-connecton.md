---
title: Устранение неполадок с RDP-подключением к виртуальной машине по идентификатору события | Документация Майкрософт
description: Истолковые данные событий для устранения различных проблем, препятствующие подключению протокола удаленного рабочего стола (RDP) к виртуальной машине Azure (VM).
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 2073d5f91b26cd2ae53e3291a6d1dad4d711b66d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437065"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Устранение неполадок с RDP-подключением к виртуальной машине по идентификатору события 

В этой статье объясняется, как использовать идентификаторы событий для устранения проблем, которые препятствуют подключению к виртуальной машине Azure по протоколу удаленного рабочего стола.

## <a name="symptoms"></a>Симптомы

Вы пытаетесь использовать сеанс протокола удаленного рабочего стола (RDP) для подключения к виртуальной машине Azure. После того как вы вводите учетные данные, соединение завершается сбоем и появляется следующее сообщение об ошибке:

**Этот компьютер не может подключиться к удаленному компьютеру. Попробуйте подключиться снова, если проблема продолжается, свяжитесь с владельцем удаленного компьютера или администратором сети.**

Чтобы устранить эту проблему, просмотрите журналы событий на виртуальной машине, а затем обратитесь к следующим сценариям.

## <a name="before-you-troubleshoot"></a>Перед устранением неполадок

### <a name="create-a-backup-snapshot"></a>Создание моментального снимка резервной копии

Чтобы создать моментальный снимок резервной копии, выполните действия, описанные в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Удаленный вход на виртуальную машину

Чтобы удаленно подключиться к виртуальной машине, используйте один из методов, описанных в статье [Use remote tools to troubleshoot Azure VM issues](remote-tools-troubleshoot-azure-vm-issues.md) (Использование удаленных средств для устранения проблем с виртуальными машинами Azure).

## <a name="scenario-1"></a>Сценарий 1

### <a name="event-logs"></a>Журналы событий

В экземпляре CMD запустите следующие команды, чтобы проверить, зарегистрировано ли событие 1058 или 1057 в журнале системы в течение последних 24 часов:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Имя журнала:**      Системы <br />
**Источник:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Дата:**          *время* <br />
**Идентификатор события:** 1058 <br />
**Категория задач:** Ни один <br />
**Уровень:**         Ошибка <br />
**Ключевые слова:**      Классический <br />
**Пользователь:**          N/A <br />
**Компьютер:**      *имя компьютера* <br />
**Описание:** Сервер RD Session Host Server не смог заменить истекаерное самоподписанное сертификат, используемый для проверки подлинности RD Session Host Server на соединениях TLS. Соответствующий код состояния: "Отказ в доступе".

**Имя журнала:**      Системы <br />
**Источник:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Дата:**          *время* <br />
**Идентификатор события:** 1058 <br />
**Категория задач:** Ни один <br />
**Уровень:**         Ошибка <br />
**Ключевые слова:**      Классический <br />
**Пользователь:**          N/A <br />
**Компьютер:**      *имя компьютера* <br />
**Описание:** Сервер-хост RD Session не смог создать новый самоподписанный сертификат, который будет использоваться для проверки подлинности сервера хоста RD Session на соединениях TLS, соответствующий код состояния объекта уже существует.

**Имя журнала:**      Системы <br />
**Источник:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Дата:**          *время* <br />
**Идентификатор события:** 1057 <br />
**Категория задач:** Ни один <br />
**Уровень:**         Ошибка <br />
**Ключевые слова:**      Классический <br />
**Пользователь:**          N/A <br />
**Компьютер:**      *имя компьютера* <br />
**Описание:** Сервер RD Session Host Server не смог создать новый сертификат самостоятельного подписанного, который будет использоваться для проверки подлинности RD Session Host Server на соединениях TLS. Соответствующий код состояния: "Набор ключей не существует".

Вы также можете проверить события ошибок SCHANNEL 36872 и 36870, выполнив следующие команды:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Имя журнала:**      Системы <br />
**Источник:** Schannel <br />
**Дата:** — <br />
**Идентификатор события:** 36870 <br />
**Категория задач:** Ни один <br />
**Уровень:**         Ошибка <br />
**Ключевые слова:**       <br />
**Пользователь:** система <br />
**Компьютер:**      *имя компьютера* <br />
**Описание:** Смертельная ошибка произошла при попытке получить доступ к конфиденциальному ключу сервера TLS. Код ошибки, возвращаемый из криптографического модуля, — 0x8009030D.  <br />
Внутреннее состояние ошибки: 10001.

### <a name="cause"></a>Причина
Эта проблема возникает из-за того, что доступ к локальным ключам шифрования RSA в папке MachineKeys на виртуальной машине невозможен. Эта проблема может возникать по любой из следующих причин:

1. Неверная настройка разрешений в папке Machinekeys или в файлах RSA.

2. Ключ RSA отсутствует или поврежден.

### <a name="resolution"></a>Решение

Чтобы устранить эту проблему, необходимо настроить правильные разрешения для RDP-сертификата, используя следующие шаги.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Предоставление разрешений папке MachineKeys

1. Создайте сценарий, используя следующее содержимое:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Запустите этот сценарий, чтобы сбросить разрешения папки MachineKey и сбросить файлы RSA до значений по умолчанию.

3.  Попробуйте снова обратиться к виртуальной машине.

После выполнения сценария вы можете проверить следующие файлы, в которых наблюдаются проблемы с разрешениями:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Обновление самозаверяющего сертификата RDP

Если проблема не устранена, запустите следующий сценарий, чтобы убедиться, что самозаверяющий сертификат RDP обновлен:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Если вы не можете обновить сертификат, выполните следующие действия, чтобы попытаться удалить его:

1. На другой виртуальной машине в той же виртуальной сети откройте поле **Запуск**, введите **mmc**, а затем нажмите кнопку **ОК**. 

2. В меню **Файл** выберите **Добавить или удалить оснастку**.

3. В списке **Доступные оснастки** выберите **Сертификаты**, а затем нажмите кнопку **Добавить**.

4. Выберите **учетную запись компьютера** и нажмите кнопку **Далее**.

5. Выберите **Another computer** (Другой компьютер), а затем добавьте IP-адрес виртуальной машины, с которой возникли проблемы.
   >[!Note]
   >Используйте внутреннюю сеть, чтобы избежать использования виртуального IP-адреса.

6. Нажмите кнопку **Готово**, а затем **ОК**.

   ![Выбор компьютера](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Разверните сертификаты, перейдите в папку Remote Desktop\Certificates, щелкните правой кнопкой мыши сертификат и выберите **Удалить**.

8. Перезапустите службу настройки удаленного рабочего стола:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Если сейчас вы обновите хранилище с помощью команды mmc, сертификат снова появится. 

Попробуйте обратиться к виртуальной машине с помощью RDP.

#### <a name="update-tlsssl-certificate"></a>Обновление сертификата TLS/SSL

Если вы настроили VM для использования сертификата TLS/SSL, запустите следующую команду, чтобы получить отпечаток пальца. Затем проверьте, совпадает ли он с отпечатком сертификата:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Если это не так, измените отпечаток:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Вы также можете попытаться удалить ключ, чтобы для RDP использовался самозаверяющий сертификат:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Сценарий 2

### <a name="event-log"></a>Журнал событий

В экземпляре CMD запустите следующие команды, чтобы проверить, зарегистрировано ли событие ошибки SCHANNEL 36871 в системном журнале за последние 24 часа:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Имя журнала:**      Системы <br />
**Источник:** Schannel <br />
**Дата:** — <br />
**Идентификатор события:** 36871 <br />
**Категория задач:** Ни один <br />
**Уровень:**         Ошибка <br />
**Ключевые слова:**       <br />
**Пользователь:** система <br />
**Компьютер:**      *имя компьютера* <br />
**Описание:** произошла неустранимая ошибка при создании учетных данных сервера TLS. Внутреннее состояние ошибки: 10013.
 
### <a name="cause"></a>Причина

Эта проблема вызвана политиками безопасности. Когда более старые версии TLS (например, 1.0) отключены, RDP-доступ завершается ошибкой.

### <a name="resolution"></a>Решение

RDP использует TLS 1.0 в качестве протокола по умолчанию. Однако протокол может быть изменен на TLS 1.1, который является новым стандартом.

Чтобы устранить эту проблему, обратитесь к статье [Устранение ошибок аутентификации при подключении к виртуальной машине Azure по протоколу RDP](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Сценарий 3

Если на виртуальной машине установлена роль **посредника подключений к удаленному рабочему столу**, проверьте, зарегистрировано ли событие 2056 или 1296 в течение последних 24 часов. В экземпляре CMD выполните следующие команды: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Имя журнала:** Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Источник:** Microsoft-Windows-TerminalServices-SessionBroker <br />
**Дата:**          *время* <br />
**Идентификатор события:** 2056 <br />
**Категория задачи:** (109) <br />
**Уровень:**         Ошибка <br />
**Ключевые слова:**       <br />
**Пользователь:** сетевая служба <br />
**Компьютер:**      *полное доменное имя компьютера* <br />
**Описание:** не удается найти описание для события с кодом 2056 из источника Microsoft-Windows-TerminalServices-SessionBroker. Компонент, вызывающий это событие, не установлен на локальном компьютере или установка повреждена. Вы можете установить или исправить компонент на локальном компьютере. <br />
Если событие возникло на другом компьютере, отображаемые сведения должны быть сохранены вместе с событием. <br />
В событии содержалась такая информация: <br />
NULL <br />
NULL <br />
Не удалось выполнить вход в базу данных.

**Имя журнала:** Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Источник:** Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Дата:**          *время* <br />
**Идентификатор события:** 1296 <br />
**Категория задачи:** (104) <br />
**Уровень:**         Ошибка <br />
**Ключевые слова:**       <br />
**Пользователь:** сетевая служба <br />
**Компьютер:**      *полное доменное имя компьютера* <br />
**Описание:** не удается найти описание для события с кодом 1296 из источника Microsoft-Windows-TerminalServices-SessionBroker-Client. Компонент, вызывающий это событие, не установлен на локальном компьютере или установка повреждена. Вы можете установить или исправить компонент на локальном компьютере.
Если событие возникло на другом компьютере, отображаемые сведения должны быть сохранены вместе с событием.
В событии содержалась такая информация:  <br />
*text* <br />
*text* <br />
Посредник подключений к удаленному рабочему столу не готов для связи по RPC.

### <a name="cause"></a>Причина

Эта проблема возникает из-за изменения имени узла сервера посредника подключений к удаленному рабочему столу. Это неподдерживаемое изменение. 

Имя узла имеет записи во внутренней базе данных Windows, которая необходима ферме службы удаленных рабочих столов для работы, и зависимости от нее. Изменение имени узла после того, как ферма уже создана, вызывает множество ошибок и может привести к остановке работы сервера-посредника.

### <a name="resolution"></a>Решение 

Чтобы устранить эту проблему, необходимо переустановить роль посредника подключений к удаленному рабочему столу и внутреннюю базу данных Windows.

## <a name="next-steps"></a>Next Steps

[Сведения о событиях Schannel](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Технический обзор Schannel SSP](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Описание сбоя RDP с кодом события 1058 и 36870 с сертификатом узла сеансов удаленных рабочих столов и SSL-подключением](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Описание сбоя Schannel с кодом 36872 или 36870 на контроллере домена](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Описание события с кодом 1058 — аутентификация и шифрование служб удаленных рабочих столов](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

