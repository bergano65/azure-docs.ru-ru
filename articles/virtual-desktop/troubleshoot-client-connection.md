---
title: Устранение неполадок удаленный рабочий стол виртуальных рабочих столов Windows в Azure
description: Устранение проблем при настройке клиентских подключений в среде клиента виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 9fcc65768db3029461a5823034336bc883379292
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227687"
---
# <a name="remote-desktop-client-connections"></a>Подключения клиента удаленного рабочего стола

Используйте эту статью для устранения проблем с подключением клиентов к виртуальным рабочим столам Windows.

## <a name="provide-feedback"></a>Отзывы

Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="you-cant-open-a-web-client"></a>Невозможно открыть веб-клиент

Убедитесь в наличии подключения к Интернету, открыв другой веб-сайт. Например, [www.Bing.com](https://www.bing.com).

Используйте **nslookup** , чтобы подтвердить, что DNS может разрешить полное доменное имя:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Попробуйте подключиться с помощью другого клиента, например удаленный рабочий стол клиента для Windows 7 или Windows 10, и проверьте, можно ли открыть веб-клиент.

### <a name="error-opening-another-site-fails"></a>Ошибка: сбой при открытии другого сайта

**Причина:** Проблемы с сетью и (или) простои.

**Исправление:** Обратитесь в службу поддержки сети.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Ошибка: nslookup не удается разрешить имя

**Причина:** Проблемы с сетью и (или) простои.

**Исправление:** Обратитесь в службу поддержки сети

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Ошибка: невозможно подключиться, но другие клиенты могут подключаться

**Причина:** Браузер работает не так, как ожидалось, и остановлен.

**Исправление:** Выполните эти инструкции для устранения неполадок в браузере.

1. Перезапустите браузер.
2. Очистите файлы cookie браузера. См. раздел [Удаление файлов cookie в Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Очистите кэш браузера. См. раздел [Очистка кэша браузера для браузера](https://binged.it/2RKyfdU).
4. Откройте браузер в частном режиме.

## <a name="web-client-stops-responding-or-disconnects"></a>Веб-клиент перестает отвечать или отключается

Попробуйте подключиться с помощью другого браузера или клиента.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Ошибка: другие браузеры и клиенты также неисправен или не удается открыть

**Причина:** Проблемы с сетью и (или) операционной системой или простои

**Исправление:** Обратитесь в службу поддержки.

## <a name="web-client-keeps-prompting-for-credentials"></a>Веб-клиент сохраняет запросы учетных данных

Если веб-клиент сохраняет запрос на ввод учетных данных, выполните следующие инструкции.

1. Убедитесь в правильности URL-адреса Web Client.
2. Убедитесь, что учетные данные предназначены для среды виртуальных рабочих столов Windows, привязанной к URL-адресу.
3. Очистите файлы cookie браузера. См. раздел [Удаление файлов cookie в Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Очистите кэш браузера. См. раздел [Очистка кэша браузера для браузера](https://binged.it/2RKyfdU).
5. Откройте браузер в частном режиме.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>удаленный рабочий стол клиент для Windows 7 или Windows 10 перестает отвечать на запросы или не может быть открыт

Используйте следующие командлеты PowerShell для очистки реестров клиента по внешнему каналу (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Перейдите по адресу **%аппдата%\рдклиентрадк** и удалите все содержимое.

Удалите и переустановите удаленный рабочий стол клиент для Windows 7 и Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Устранение неполадок подключения конечных пользователей

Иногда пользователи могут получить доступ к своим каналам и локальным ресурсам, но по-прежнему имеют проблемы конфигурации, доступности или производительности, которые не позволяют им получить доступ к удаленным ресурсам. В таких случаях пользователь получает сообщения, аналогичные следующим:

![Подключение к удаленному рабочему столу сообщение об ошибке.](media/eb76b666808bddb611448dfb621152ce.png)

![Не удается подключиться к сообщению об ошибке шлюза.](media/a8fbb9910d4672147335550affe58481.png)

Выполните следующие общие инструкции по устранению ошибок подключения клиентов.

1. Подтвердите имя пользователя и время, когда возникла ошибка.
2. Откройте **PowerShell** и установите подключение к клиенту виртуальных рабочих столов Windows, где была обнаружена эта ошибка.
3. Проверьте подключение к правильному клиенту с помощью **Get-рдстенант.**
4. С помощью командлетов **Get-рдшостпул** и **Get-рдссессионхост** убедитесь, что на правильном пуле узлов выполняется устранение неполадок.
5. Выполните следующую команду, чтобы получить список всех неудачных действий типа Connection для указанного временного интервала:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Используя идентификатор **ActivityId** из предыдущего выходных данных командлета, выполните следующую команду:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName <TenantName> -ActivityId <ActivityId> -Detailed).Errors
    ```

7. Команда выдает выходные данные, аналогичные приведенным ниже. Используйте **ерроркодесимболик** и **ErrorMessage** для устранения основной причины.

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

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Ошибка: O_ADD_USER_TO_GROUP_FAILED или не удалось добавить пользователя = ≤ username ≥ в Group = удаленный рабочий стол Users. Причина: Win32. ERROR_NO_SUCH_MEMBER

**Причина:** Виртуальная машина не присоединена к домену, в котором находится объект пользователя.

**Исправление:** Добавьте виртуальную машину в правильный домен. См. раздел [присоединение виртуальной машины Windows Server к управляемому домену](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Ошибка: nslookup не удается разрешить имя

**Причина:** Сетевые проблемы или простои.

**Исправление:** Обратитесь в службу поддержки сети

### <a name="error-connectionfailedclientprotocolerror"></a>Ошибка: Коннектионфаиледклиентпротоколеррор

**Причина:** Виртуальные машины, к которым пытается подключиться пользователь, не присоединены к домену.

**Исправление:** Присоедините все виртуальные машины, которые являются частью пула узлов, к контроллеру домена.

### <a name="error-connectionfailedusersidinformationmismatch"></a>Ошибка: Коннектионфаиледусерсидинформатионмисматч
**Причина:** Идентификатор безопасности из маркера Azure Active Directory (AD) пользователя не соответствует идентификатору безопасности, возвращенному контроллером домена при попытке включить удаленный вход в систему. Эта ошибка обычно возникает при попытке входа в среду Azure Active Directory доменных служб (Azure AD DS) с пользовательским исходным кодом из Windows Server AD.

**Исправление:** В настоящее время этот сценарий не поддерживается. Только пользователи, источником которых является Azure Active Directory, могут входить в виртуальные машины виртуальных рабочих столов Windows, подключенные к Azure AD DS.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Пользователь подключается, но ничего не отображается (канал отсутствует)

Пользователь может запускать удаленный рабочий стол клиентов и выполнять проверку подлинности, однако пользователь не увидит значки в веб-канале обнаружения.

Убедитесь, что пользователь, сообщающий о проблемах, назначен группам приложений с помощью следующей командной строки:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Убедитесь, что пользователь входит в систему с использованием правильных учетных данных.

Если используется веб-клиент, убедитесь в отсутствии проблем с кэшированными учетными данными.

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об устранении неполадок с виртуальным рабочим столом Windows и сведениями о эскалации см. в разделе [Обзор устранения неполадок, обратная связь и поддержка](troubleshoot-set-up-overview.md).
- Сведения об устранении неполадок при создании клиента и пула узлов в среде виртуальных рабочих столов Windows см. в статье [Создание пула клиентов и узлов](troubleshoot-set-up-issues.md).
- Сведения об устранении неполадок при настройке виртуальной машины в виртуальном рабочем столе Windows см. в разделе [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration.md).
- Сведения об устранении неполадок при использовании PowerShell с виртуальным рабочим столом Windows см. в статье [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Руководство по устранению неполадок см. в разделе [учебник. Устранение неполадок диспетчер ресурсов развертываний шаблонов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
