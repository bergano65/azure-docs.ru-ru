---
title: Подключения клиента удаленного рабочего стола в Windows виртуальный рабочий стол — Azure
description: Как устранить проблемы при настройке подключения клиентов в среде виртуального рабочего стола Windows клиента.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: f88dee579e44a01dc1a7404ef6a670de34063552
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833568"
---
# <a name="remote-desktop-client-connections"></a>Подключения клиента удаленного рабочего стола

Воспользуйтесь этой статьей, чтобы устранить проблемы с клиентские подключения виртуальный рабочий стол Windows.

## <a name="provide-feedback"></a>Отзывы

В период предоставления предварительной версии Виртуального рабочего стола Windows мы не принимаем запросы в службу поддержки. Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="you-cant-open-a-web-client"></a>Не удается открыть веб-клиента

Убедитесь, что имеется подключение к Интернету, открыв другой веб-сайт; например [www.Bing.com](https://www.bing.com).

Используйте **nslookup** для подтверждения DNS может разрешить FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Попробуйте подключиться с другой клиент, например, клиент удаленного рабочего стола для Windows 7 или Windows 10 и проверить увидеть, если его можно открыть веб-клиента.

### <a name="error-opening-another-site-fails"></a>Ошибка: Открытие другого сайта завершается сбоем

**Причина.** Проблемы с сетью и/или простоев.

**Исправление:** Обратитесь в службу поддержки сети.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Ошибка: Nslookup не удается разрешить имя

**Причина.** Проблемы с сетью и/или простоев.

**Исправление:** Обратитесь в службу поддержки сети

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Ошибка: Не удается подключиться, но другие клиенты могут подключаться

**Причина.** Браузер не работает как ожидаемый и остановлена работу.

**Исправление:** Следуйте этим инструкциям для устранения неполадок в браузере.

1. Перезапустите обозреватель.
2. Очистить файлы cookie. См. в разделе [по удалению файлов cookie в браузере Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Очистите кэш браузера. См. в разделе [очистить кэш браузера для веб-браузера](https://binged.it/2RKyfdU).
4. Открыть браузер в режиме защищенного просмотра.

## <a name="web-client-stops-responding-or-disconnects"></a>Отключение или перестает отвечать на запросы веб-клиента

Попробуйте подключиться, используя другой браузер или клиент.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Ошибка: Другие браузеры и клиенты также неисправности или не открываться

**Причина.** Сети и/или операции системы неполадок или простоев

**Исправление:** Обратитесь в службу поддержки команд.

## <a name="web-client-keeps-prompting-for-credentials"></a>Веб-клиент продолжает запрашивать учетные данные

Если веб-клиент продолжает запрашивать учетные данные, выполните следующие действия.

1. Убедитесь в правильности URL-адрес клиента.
2. Убедитесь, что учетные данные, в среде виртуального рабочего стола Windows, привязаны к URL-адрес.
3. Очистить файлы cookie. См. в разделе [по удалению файлов cookie в браузере Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Очистите кэш браузера. См. в разделе [очистить кэш браузера для веб-браузера](https://binged.it/2RKyfdU).
5. Открыть браузер в режиме защищенного просмотра.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Клиент удаленного рабочего стола для Windows 7 или Windows 10, перестает отвечать на запросы или не удается открыть

Используйте следующие командлеты PowerShell для очистки реестров на клиентских компьютерах (OOB)-каналу.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Перейдите к **%AppData%\RdClientRadc** и удалить все содержимое.

Удалите и переустановите клиент удаленного рабочего стола для Windows 7 и Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Устранение неполадок соединения конечных пользователей

Иногда пользователи могут получить доступ к их веб-канала и локальных ресурсов, но по-прежнему иметь конфигурацию, доступности или проблем с производительностью, не имели доступа к удаленным ресурсам. В этом случае пользователь получает сообщения следующего вида:

![Удаленный рабочий стол сообщение об ошибке.](media/eb76b666808bddb611448dfb621152ce.png)

![Не удается подключиться к сообщение об ошибке шлюза.](media/a8fbb9910d4672147335550affe58481.png)

Выполните эти общие инструкции по устранению неполадок для кодов ошибок подключения клиента.

1. Подтвердите имя пользователя и время, когда возникала проблема.
2. Откройте **PowerShell** и установить подключение к виртуальному рабочему столу Windows клиента, где была обнаружена проблема.
3. Убедитесь в правильный клиент с помощью соединения **Get RdsTenant.**
4. С помощью **Get-RdsHostPool** и **Get RdsSessionHost** командлетов, убедитесь, что устранение неполадок, проводится в пуле верный узел.
5. Выполните следующую команду, чтобы получить список всех неудачных действий тип подключения для указанного временного окна:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. С помощью **ActivityId** из предыдущих выходных данных командлета, выполните приведенную ниже команду:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. Команда выдает результат, аналогичный приведенному ниже. Используйте **ErrorCodeSymbolic** и **ErrorMessage** для устранения основной причины.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Ошибка: O_ADD_USER_TO_GROUP_FAILED / не удалось добавить пользователя = ≤username≥ группу = Пользователи удаленного рабочего стола. Причина: Win32.ERROR_NO_SUCH_MEMBER

**Причина.** Виртуальная машина не присоединена к домену, где находится объект пользователя.

**Исправление:** Добавьте виртуальную Машину к правильному домену. См. в разделе [присоединение виртуальной машины Windows Server к управляемому домену](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Ошибка: Nslookup не удается разрешить имя

**Причина.** Проблемы с сетью или простоев.

**Исправление:** Обратитесь в службу поддержки сети

### <a name="error-connectionfailedclientprotocolerror"></a>Ошибка: ConnectionFailedClientProtocolError

**Причина.** Виртуальные машины, пользователь пытается подключиться к не присоединен к домену.

**Исправление:** Присоедините все виртуальные машины, которые являются частью пула узла к контроллеру домена.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Пользователь подключается, но ничего не отображается (веб-канал не)

Пользователя можно запустить клиенты удаленного рабочего стола и возможность проверки подлинности, тем не менее пользователь не видит значки при обнаружении веб-канала.

Убедитесь, что пользователь, сообщающий о проблемах был назначен группы приложений с помощью этой командной строки:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Убедитесь, что пользователь входит в систему с правильными учетными данными.

Если используется веб-клиента, убедитесь, что проблем нет кэшированных учетных данных.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок виртуального рабочего стола Windows и отслеживает эскалации, см. в разделе [Устранение неполадок, Обзор, отзывы и поддержка](troubleshoot-set-up-overview.md).
- Чтобы устранить неполадки при создании клиента и узла пула в среде виртуального рабочего стола Windows, см. в разделе [клиента и узла пул создания](troubleshoot-set-up-issues.md).
- Чтобы устранить неполадки при настройке виртуальной машины (VM) в виртуальный рабочий стол Windows, см. в разделе [конфигурацию виртуальной машины узла сеансов](troubleshoot-vm-configuration.md).
- Чтобы устранить неполадки при использовании PowerShell с помощью виртуального рабочего стола Windows, см. в разделе [виртуального рабочего стола PowerShell Windows](troubleshoot-powershell.md).
- Дополнительные сведения о предварительной версии службы, см. в разделе [Windows Desktop предварительной среде](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Сведения об устранении неполадок см. в статье [Tutorial: Устраняйте неполадки развертывания шаблонов Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
