---
title: PowerShell для ролей Azure AD в PIM - Azure AD Документы Майкрософт
description: Управление ролями Azure AD с помощью cmdlets PowerShell в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa241a261b8dcb21dd39b5dacacac9aa4889304
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519647"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell для ad Azure в привилегированном управлении идентификацией

Эта статья содержит инструкции по использованию смлетов PowerShell PowerShell Для управления ролями Azure AD в привилегированном управлении идентификацией (PIM) для управления функциями Azure Active Directory (Azure AD) PowerShell. Кроме того, в ней содержатся сведения о том, как выполнить настройки модуля Azure AD PowerShell.

> [!Note]
> Наша официальная PowerShell поддерживается только в том случае, если вы находитесь в новой версии azure AD Privileged Identity Management. Пожалуйста, перейдите к Privileged Identity Management и убедитесь, что у вас есть следующий баннер на лезвии быстрого старта.
> [![проверить версию привилегированного управления идентификацией у вас есть](media/pim-how-to-add-role-to-user/pim-new-version.png "Выберите Azure AD > привилегированное управление идентификацией")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Если у вас нет этого баннера, пожалуйста, подождите, как мы в настоящее время в процессе развертывания этого обновленного опыта в течение ближайших нескольких недель.
> Смлеты Privileged Управления идентификацией PowerShell поддерживаются с помощью модуля Azure AD Preview. Если вы использовали другой модуль и этот модуль возвращает сообщение об ошибке, пожалуйста, начните использовать этот новый модуль. Если у вас есть производственные системы, построенные поверх другого модуля, пожалуйста,pim_preview@microsoft.com

## <a name="installation-and-setup"></a>Установка и настройка

1. Установка модуля Preview Azure AD

        Install-module AzureADPreview

1. Убедитесь, что перед началом работы у вас есть необходимые разрешения на ролевые роли. Если вы пытаетесь выполнять задачи управления, такие как присвоение назначения ролей или обновление настройки ролей, убедитесь, что у вас есть либо Глобальная администратор, либо привилегированная роль администратора. Если вы просто пытаетесь активировать собственное назначение, никаких разрешений, выходящих за рамки пользовательских разрешений по умолчанию, не требуется.

1. Подключитесь к Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Найдите идентификатор клиента, перейдя в**идентификатор** **каталога Active Directory.** > **Properties** >  В разделе cmdlets используйте этот идентификатор всякий раз, когда вам нужно предоставить ресурсId.

    ![Поиск идентификатора клиента в свойствах для организации Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Следующие разделы являются простыми примерами, которые могут помочь вам и работает. Вы можете найти более подробную документацию https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_managementотносительно следующих cmdlets на . Тем не менее, вам нужно будет заменить "azureResources" в параметре providerID на "aadRoles". Также необходимо помнить об использовании идентификатора клиента для организации Azure AD в качестве параметра resourceId.

## <a name="retrieving-role-definitions"></a>Получение определений ролей

Используйте следующие cmdlet, чтобы получить все встроенные и пользовательские роли Azure AD в вашей организации Azure AD (арендатор). Этот важный шаг дает вам отображение между ролевой именем и рольюDefinitionId. РольDefinitionId используется во всем этих cmdlets для того, чтобы ссылка на конкретную роль.

РольDefinitionId специфична для вашей организации Azure AD и отличается от ролиDefinitionId, возвращенной API управления ролью.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Результат:

![Получите все роли для организации Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Получение ролевых заданий

Используйте следующие cmdlet для получения всех назначений ролей в организации Azure AD.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Используйте следующие cmdlet для получения всех ролевых заданий для конкретного пользователя. Этот список также известен как "Мои роли" на портале Azure AD. Разница лишь в том, что вы добавили фильтр для идентификатора объекта. Идентификатором объекта в этом контексте является идентификатор пользователя или идентификатор группы.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Используйте следующие cmdlet для получения всех ролевых заданий для определенной роли. РольDefinitionId здесь является идентификатором, который возвращается предыдущим cmdlet.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

В результате cmdlets приведен список объектов назначения ролей, показанных ниже. Идентификатор объекта — это идентификатор пользователя пользователя, которому назначена роль. Состояние назначения может быть либо активным, либо подходящим. Если пользователь активен и в поле LinkedEligibleRoleAssignmentId есть идентификатор, это означает, что роль в настоящее время активирована.

Результат:

![Извлеките все назначения ролей для организации Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Назначение роли

Используйте следующий cmdlet для создания подходящего назначения.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

Расписание, определяющее время начала и окончания назначения, является объектом, который может быть создан как следующий пример:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> Если значение endDateTime сведено к нулю, это указывает на постоянное назначение.

## <a name="activate-a-role-assignment"></a>Активировать назначение ролей

Используйте следующий cmdlet для активации подходящего задания.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Этот cmdlet почти идентичен cmdlet для создания ролевой задания. Ключевое различие между cmdlets заключается в том, что для параметра типа «Тип» активация — это «userAdd», а не «adminAdd». Другое отличие заключается в том, что параметр «Назначениегосударства» является «активным» вместо «Приемлемого».

> [!Note]
> Существует два ограничивающих сценария активации ролей через PowerShell.
> 1. Если вам требуется билетная система / номер билета в настройке вашей роли, нет никакого способа, чтобы поставить их в качестве параметра. Таким образом, активировать роль за пределами портала Azure будет невозможно. Эта функция в настоящее время развернута в PowerShell в течение ближайших нескольких месяцев.
> 1. Если для активации роли требуется многофакторная аутентификация, PowerShell в настоящее время не может бросить вызов пользователю при активации их роли. Вместо этого, пользователи должны будут инициировать вызов MFA при подключении к Azure AD, следуя [этому сообщению](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) в блоге от одного из наших инженеров. Если вы разрабатываете приложение для PIM, одной из возможных реализаций является вызов пользователям и повторное подключение их к модулю после того, как они получат ошибку "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Извлечение и обновление параметров ролей

Используйте следующие cmdlet, чтобы получить все настройки ролей в вашей организации Azure AD.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

Есть четыре основных объекта в настройках. Только три из этих объектов в настоящее время используются PIM. UserMemberSettings — это настройки активации, AdminEligibleSettings — настройки назначений для подходящих назначений, а AdminmemberSettings — настройки назначения для активных назначений.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Для обновления настройки роли необходимо сначала определить объект настройки следующим образом:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

Затем можно применить настройку к одному из объектов для определенной роли, как показано ниже. Идентификатор — это идентификатор настройки роли, который можно извлечь из результата настроек ролей списка cmdlet.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Дальнейшие действия

- [Назначение настраиваемой роли Azure AD](azure-ad-custom-roles-assign.md)
- [Update or remove an assigned Azure AD custom role in Privileged Identity Management](azure-ad-custom-roles-update-remove.md) (Обновление или удаление назначенной настраиваемой роли AAD в Privileged Identity Management)
- [Настройка назначения настраиваемой роли Azure AD](azure-ad-custom-roles-configure.md)
- [Administrator role permissions in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) (Разрешения роли администратора в Azure Active Directory)
