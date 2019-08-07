---
title: Виртуальные рабочие столы Windows PowerShell — Azure
description: Устранение неполадок с PowerShell при настройке среды клиента виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 7c5a15790c3a88e9aa3921d6cc2626576aedaa64
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816373"
---
# <a name="windows-virtual-desktop-powershell"></a>Виртуальный рабочий стол Windows — PowerShell

Используйте эту статью для устранения ошибок и проблем при использовании PowerShell с виртуальным рабочим столом Windows. Дополнительные сведения о службы удаленных рабочих столов PowerShell см. в статье [Windows Virtual Desktop PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Оставить отзыв

В период предоставления предварительной версии Виртуального рабочего стола Windows мы не принимаем запросы в службу поддержки. Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Команды PowerShell, используемые при установке виртуальных рабочих столов Windows

В этом разделе перечислены команды PowerShell, которые обычно используются при настройке виртуальных рабочих столов Windows, а также способы решения проблем, которые могут возникнуть при их использовании.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Ошибка: Команда Add-Рдсаппграупусер — указанный UserPrincipalName уже назначен группе приложений RemoteApp в указанном пуле узлов.

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Причина.** Используемое имя пользователя уже назначено группе приложений другого типа. Пользователи не могут быть назначены как для удаленного рабочего стола, так и для группы удаленных приложений в одном пуле узлов сеансов.

**Исправно** Если пользователю нужны как удаленные приложения, так и удаленный рабочий стол, создайте разные пулы узлов или предоставьте пользователям доступ к удаленному рабочему столу, что позволит использовать любое приложение на виртуальной машине узла сеансов.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Ошибка: Команда Add-Рдсаппграупусер — указанное значение UserPrincipalName не существует в Azure Active Directory, связанном с клиентом удаленный рабочий стол

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Причина.** Пользователь, указанный параметром-UserPrincipalName, не может быть найден в Azure Active Directory, привязанном к клиенту виртуальных рабочих столов Windows.

**Исправно** Подтвердите элементы в следующем списке.

- Пользователь синхронизирован с Azure Active Directory.
- Пользователь не связан с бизнес-клиентами (B2C) или бизнес-предприятием (B2B).
- Клиент виртуальных рабочих столов Windows привязан к правильному Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Get-Рдсдиагностикактивитиес--пользователь не имеет права на выполнение запросов к службе управления

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Причина:** параметр-TenantName

**Исправно** Выполните инструкцию GET-рдсдиагностикактивитиес с \<параметром-TenantName TenantName >.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Get-Рдсдиагностикактивитиес--пользователь не имеет права на запрос к службе управления

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Причина.** Использование параметра-Deployment.

**Исправление.** параметр-Deployment может использоваться только администраторами развертывания. Эти администраторы обычно являются членами группы службы удаленных рабочих столов или Windows Virtual Desktop. Замените параметр-Deployment на-TenantName \<TenantName >.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: New-Рдсролеассигнмент — пользователь не имеет разрешения на выполнение запросов к службе управления

**Причина 1**. Используемая учетная запись не имеет службы удаленных рабочих столов разрешений владельца на клиенте.

**Исправление 1:** Пользователю с разрешениями службы удаленных рабочих столов Owner необходимо выполнить назначение ролей.

**Причина 2.** Используемая учетная запись имеет службы удаленных рабочих столов разрешений владельца, но не является частью Azure Active Directory клиента или не имеет разрешений на запрос Azure Active Directory, где находится пользователь.

**Исправление 2:** Пользователю с разрешениями Active Directory необходимо выполнить назначение ролей.

>[!Note]
>New-Рдсролеассигнмент не может предоставлять разрешения пользователю, который не существует в Azure Active Directory (AD).

## <a name="next-steps"></a>Следующие шаги

- Общие сведения об устранении неполадок с виртуальным рабочим столом Windows и сведениями о эскалации см. в разделе [Обзор устранения неполадок, обратная связь и поддержка](troubleshoot-set-up-overview.md).
- Сведения об устранении неполадок при создании клиента и пула узлов в среде виртуальных рабочих столов Windows см. в статье [Создание пула клиентов и узлов](troubleshoot-set-up-issues.md).
- Сведения об устранении неполадок при настройке виртуальной машины в виртуальном рабочем столе Windows см. в разделе [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration.md).
- Сведения об устранении неполадок клиентских подключений Windows к виртуальным рабочим столам см. в разделе [Удаленный рабочий стол клиентские подключения](troubleshoot-client-connection.md).
- Дополнительные сведения о службе предварительной версии см. в разделе [Среда Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Сведения об устранении неполадок см. в статье [Tutorial: Устранение неполадок при](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)развертывании шаблонов диспетчер ресурсов.
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
