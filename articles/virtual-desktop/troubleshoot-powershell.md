---
title: Windows Virtual Desktop PowerShell - Azure
description: Как устранить проблемы с PowerShell при настройке среды для арендаторов Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127464"
---
# <a name="windows-virtual-desktop-powershell"></a>Виртуальный рабочий стол Windows — PowerShell

Используйте эту статью для устранения ошибок и проблем при использовании PowerShell с помощью Виртуального рабочего стола Windows. Для получения дополнительной информации о удаленных настольных служб PowerShell, см [Windows Виртуальный настольный Powershell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Отзывы

Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Команды PowerShell, используемые при настройке виртуального рабочего стола Windows

В этом разделе перечислены команды PowerShell, которые обычно используются при настройке Виртуального рабочего стола Windows, и предоставляются способы решения проблем, которые могут возникнуть при их использовании.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Ошибка: Команда Add-RdsAppGroupUser -- Указанная группа приложений UserPrincipalName уже назначена группе приложений RemoteApp в указанном пуле хоста

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Причина:** Используемое имя пользователя уже назначено группе приложений другого типа. Пользователи не могут быть назначены как в группу удаленного рабочего стола, так и в группу удаленных приложений в одном и том же пуле хоста сеанса.

**Исправление:** Если пользователю нужны как удаленные приложения, так и удаленный рабочий стол, создайте различные пулы хостов или предоставьте пользователю доступ к удаленному рабочему столу, что позволит использовать любое приложение на хостеле сеанса VM.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Ошибка: Команда Add-RdsAppGroupUser -- Указанная пользовательская principalName не существует в active-каталоге Azure, связанном с арендатором удаленного рабочего стола

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Причина:** Пользователь, указанный в -UserPrincipalName, не может быть найден в active Directory Azure, привязанном к арендатору Windows Virtual Desktop.

**Исправление:** Подтвердите элементы в следующем списке.

- Пользователь синхронизирован с индикатором Active Azure.
- Пользователь не привязан к бизнесу с потребительской (B2C) или бизнес-бизнеса (B2B) коммерции.
- Арендатор Windows Virtual Desktop привязан к правильному каталогу Azure Active.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Get-RdsDiagnosticActivities - Пользователь не уполномочен задавать запрос службы управления

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Причина:** -TenantName параметр

**Исправление:** Выпуск Get-RdsDiagnosticActivities с \<-TenantName TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Get-RdsDiagnosticActivities - пользователь не уполномочен задавать запрос службы управления

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Причина:** Использование -Переключатель развертывания.

**Исправление:** -Переключатель развертывания может использоваться только администраторами развертывания. Эти администраторы, как правило, являются членами команды Remote Desktop Services/Windows Virtual Desktop. Замените переключатель -Развертывание \<с -TenantName TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: New-RdsRoleAssignment - пользователь не уполномочен задавать запрос службы управления

**Причина 1:** Используемая учетная запись не имеет разрешений владельца удаленного настольного компьютера на арендатора.

**Исправление 1:** Пользователь с разрешениями владельца удаленного рабочего стола должен выполнить назначение ролей.

**Причина 2:** Используемая учетная запись имеет разрешения владельца удаленного рабочего стола, но не входит в active-каталог клиента Azure Active или не имеет разрешений на запрос Active Directory Azure, где находится пользователь.

**Исправление 2:** Пользователь с разрешениями Active Directory должен выполнить назначение ролей.

>[!Note]
>New-RdsRoleAssignment не может давать разрешения пользователю, который не существует в Active Directory (AD).

## <a name="next-steps"></a>Дальнейшие действия

- Для обзора устранения неполадок Windows Virtual Desktop и треков эскалации [см.](troubleshoot-set-up-overview.md)
- Для устранения неполадок при создании пула арендатора и хоста в среде Windows Virtual Desktop [см.](troubleshoot-set-up-issues.md)
- Для устранения неполадок при настройке виртуальной машины (VM) в Windows Virtual Desktop [см.](troubleshoot-vm-configuration.md)
- Чтобы устранить неполадки с подключением клиентов Windows Virtual Desktop, [см.](troubleshoot-service-connection.md)
- Для устранения неполадок с клиентами Remote Desktop просмейте [о устранении проблем с клиентом Remote Desktop](troubleshoot-client.md)
- Чтобы узнать больше об услуге, [см.](environment-setup.md)
- Чтобы пройти через учебник по устранению неполадок, [см.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](../azure-resource-manager/templates/deployment-history.md).
