---
title: Windows виртуального рабочего стола PowerShell — Azure
description: Устранение неполадок с помощью PowerShell, при настройке среды с клиентами виртуального рабочего стола Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 06b955365ffc7c0a1dff93db95932d8696293e9f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605249"
---
# <a name="windows-virtual-desktop-powershell"></a>Виртуальный рабочий стол Windows — PowerShell

Воспользуйтесь этой статьей, чтобы устранить ошибки и проблемы при использовании PowerShell с помощью виртуального рабочего стола Windows. Дополнительные сведения о PowerShell служб удаленного рабочего стола, см. в разделе [виртуального рабочего стола Powershell Windows](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Отзывы

В период предоставления предварительной версии Виртуального рабочего стола Windows мы не принимаем запросы в службу поддержки. Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Команды PowerShell, используемые во время установки виртуального рабочего стола Windows

В этом разделе перечислены команды PowerShell, которые обычно используются при настройке виртуального рабочего стола Windows и предоставляет способы устранения проблем, которые могут возникнуть при их использовании.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Ошибка: Добавление RdsAppGroupUser команда--указанного UserPrincipalName уже назначен группу приложения RemoteApp в указанном пуле узла

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Причина.** Имя пользователя, используемое уже назначены в группу другого типа приложения. Пользователи не могут быть назначены обе группу удаленного рабочего стола и удаленного приложения в один и тот же пул узлов сеанса.

**Исправление:** Если пользователю нужен удаленного рабочего стола и удаленные приложения, Создание пулов другой узел или предоставить пользователю доступ к удаленному рабочему столу, которая разрешает использование любого приложения на узле сеанс виртуальной Машины.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Ошибка: Добавление RdsAppGroupUser команда--указанного UserPrincipalName не существует в Azure Active Directory, связанные с клиентом удаленного рабочего стола

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Причина.** Не удается найти пользователя, указанного по - UserPrincipalName в Azure Active Directory, привязаны к виртуальному рабочему столу Windows клиента.

**Исправление:** Подтвердите элементы в списке ниже.

- Пользователь является синхронизированного с Azure Active Directory.
- Пользователю не привязано к бизнеса и потребитель (B2C) или commerce предприятие предприятие (B2B).
- Виртуальный рабочий стол Windows клиента привязывается к правильный Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Get-RdsDiagnosticActivities--Пользователь не авторизован для запроса к службе управления

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Причина:** - TenantName параметр

**Исправление:** Выдавать Get-RdsDiagnosticActivities с - TenantName <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Get-RdsDiagnosticActivities — пользователь не авторизован для запроса к службе управления

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Причина.** Использовать параметр - переключатель развертывания.

**FIX:** -переключатель развертывания можно использовать только для администраторов развертывания. Эти Администраторы обычно являются членами команды удаленного рабочего стола служб/Windows виртуальному рабочему столу. Замените развертывания параметр - с - TenantName <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Новый-RdsRoleAssignment--пользователь не авторизован для запроса к службе управления

**Причина 1**. Используемой учетной записи нет разрешений владельца службы удаленного рабочего стола на клиенте.

**Исправление 1:** Пользователь с разрешениями владельца службы удаленных рабочих столов необходимо выполнить назначение роли.

**Причина 2.** Используемой учетной записи есть разрешения владельца службы удаленных рабочих столов, но не входит в состав клиента Azure Active Directory или не имеет разрешений для запроса к Azure Active Directory, где находится пользователь.

**Исправление 2:** Пользователь с разрешениями Active Directory необходимо выполнить назначение роли.

>[!Note]
>Новый RdsRoleAssignment не может предоставить разрешения пользователю, не существует в Azure Active Directory (AD).

## <a name="next-steps"></a>Следующие шаги

- Общие сведения об устранении неполадок виртуального рабочего стола Windows и отслеживает эскалации, см. в разделе [Устранение неполадок, Обзор, отзывы и поддержка](troubleshoot-set-up-overview.md).
- Чтобы устранить неполадки при создании клиента и узла пула в среде виртуального рабочего стола Windows, см. в разделе [клиента и узла пул создания](troubleshoot-set-up-issues.md).
- Чтобы устранить неполадки при настройке виртуальной машины (VM) в виртуальный рабочий стол Windows, см. в разделе [конфигурацию виртуальной машины узла сеансов](troubleshoot-vm-configuration.md).
- Чтобы устранить неполадки с помощью клиентских подключений виртуального рабочего стола Windows, см. в разделе [клиента к удаленному рабочему столу](troubleshoot-client-connection.md).
- Дополнительные сведения о предварительной версии службы, см. в разделе [Windows Desktop предварительной среде](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Сведения об устранении неполадок см. в статье [Tutorial: Устраняйте неполадки развертывания шаблонов Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).