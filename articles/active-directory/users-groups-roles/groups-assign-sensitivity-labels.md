---
title: Присваивай метки чувствительности группам - Azure AD Документы Майкрософт
description: Сведения о создании правил членства для автоматического заполнения групп и о ссылках на эти правила.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329738"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Присваивай метки чувствительности группам Office 365 в Active Directory (предварительный просмотр)

Активный каталог Azure (Azure AD) поддерживает применение меток чувствительности, опубликованных [центром соответствия Microsoft 365,](https://sip.protection.office.com/homepage) к группам Office 365. Метки чувствительности применяются к группам в различных службах, таких как Outlook, Microsoft Teams и SharePoint. Эта функция сейчас доступна в виде общедоступной предварительной версии. Для получения дополнительной информации о поддержке приложений Office 365 [см.](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)

> [!IMPORTANT]
> Для настройки этой функции в вашей организации Azure AD должна быть по крайней мере одна активная лицензия Azure Active Directory Premium P1.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Включить поддержку этикетки чувствительности в PowerShell

Чтобы применить опубликованные метки к группам, необходимо сначала включить функцию. Эти шаги позволяют включить функцию в Azure AD.

1. Откройте окно Windows PowerShell на компьютере. Его можно открыть без более высокого уровня привилегий.
1. Выполните следующие команды, чтобы подготовить среду к выполнению командлетов.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Во время **входа на страницу учетной записи** введите учетную запись и пароль для подключения к службе и выберите **вход в**систему.
1. Принесите текущие параметры группы для организации Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Если для этой организации Azure AD не были созданы настройки группы, необходимо сначала создать настройки. Выполните последующие действия в [cmdlets Active Directory Azure для настройки параметров группы](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) для создания параметров группы для этой организации Azure AD.

1. Затем отобразите текущие параметры группы.

    ```PowerShell
    $Setting.Values
    ```

1. Затем включите функцию:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Затем сохраните изменения и примените настройки:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Вот и все. Вы включили функцию и можете применять опубликованные метки к группам.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Назначить метку новой группе на портале Azure

1. Войти на [админ-центр Azure AD.](https://aad.portal.azure.com)
1. Выберите **группы,** а затем выберите **новую группу.**
1. На странице **New Group** выберите **Office 365,** а затем заполните необходимую информацию для новой группы и выберите метку чувствительности из списка.

   ![Назначить метку чувствительности на странице Новых групп](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Сохраните изменения и выберите **Создать**.

Создается группа, и затем автоматически применяются настройки сайта и группы, связанные с выбранной меткой.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Назначить метку существующей группе на портале Azure

1. Войти на [админ-центр Azure Ad](https://aad.portal.azure.com) с учетной записью группы админ или в качестве владельца группы.
1. Выберите **Группы**.
1. На странице **«Все группы»** выберите группу, которую вы хотите пометить.
1. На странице выбранной группы выберите **Свойства** и выберите метку чувствительности из списка.

   ![Назначить метку чувствительности на странице обзора для группы](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Выберите **Сохранить**, чтобы сохранить изменения.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Удалить метку из существующей группы на портале Azure

1. Войти на [админ-центр Azure Ad](https://aad.portal.azure.com) с учетной записью Глобального админа или группы или в качестве владельца группы.
1. Выберите **Группы**.
1. На странице **«Все группы»** выберите группу, из нее необходимо удалить метку.
1. На странице **группы** выберите **Свойства**.
1. Выберите **Удалить**.
1. Щелкните **Сохранить**, чтобы применить изменения.

## <a name="using-classic-azure-ad-classifications"></a>Использование классических классификаций Azure AD

После включения этой функции в "классические" классификации для групп появятся только существующие группы и сайты, и их следует использовать для новых групп только в том случае, если создаются группы в приложениях, не поддерживающих метки чувствительности. Ваш админ может преобразовать их в чувствительные метки позже, если это необходимо. Классические классификации – это старые классификации, `ClassificationList` настроенные путем определения значений для настройки в Azure AD PowerShell. Когда эта функция включена, эти классификации не будут применяться к группам.

## <a name="troubleshooting-issues"></a>Устранение неполадок

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Метки чувствительности недоступны для назначения в группе

Опция маркировки чувствительности отображается только для групп при соблюдении всех следующих условий:

1. Этикетки публикуются в Microsoft 365 Compliance Center для этого арендатора.
1. Функция включена, EnableMIPLabels установлен на True в PowerShell.
1. Группа представляет собой группу Office 365.
1. Арендатор имеет активную лицензию Azure Active Directory Premium P1.
1. Текущий пользователь, вписав в систему, имеет достаточные привилегии для присвоения меток. Пользователь должен быть либо Глобальным администратором, администратором группы, либо владельцем группы.

Пожалуйста, убедитесь, что все условия выполнены для того, чтобы назначить этикетки для группы.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Этикетки, который я хочу назначить, нет в списке

Если ярлыка, который вы ищете, не в списке, это может быть в случае одной из следующих причин:

- Метка может не быть опубликована в Microsoft 365 Compliance Center. Это может также применяться к этикеткам, которые больше не публикуются. Пожалуйста, свяжитесь с вашим администратором для получения дополнительной информации.
- Метка может быть опубликована, однако она недоступна пользователю, который вписан. Пожалуйста, свяжитесь с администратором для получения дополнительной информации о том, как получить доступ к этикетке.

### <a name="how-to-change-the-label-on-a-group"></a>Как изменить этикетку в группе

Этикетки могут быть заменены в любое время, используя те же шаги, как назначение метки для существующей группы, следующим образом:

1. Войти на [администратора Azure Ad](https://aad.portal.azure.com) с учетной записью администратора Global или Group или в качестве владельца группы.
1. Выберите **Группы**.
1. На странице **«Все группы»** выберите группу, которую вы хотите пометить.
1. На странице выбранной группы выберите **Свойства** и выберите новую метку чувствительности из списка.
1. Нажмите кнопку **Сохранить**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Изменения настройки группы к опубликованным метки не обновляются в группах

В качестве наилучшей практики мы не рекомендуем изменять настройки группы для метки после того, как метка будет применена к группам. При внесении изменений в параметры группы, связанные с опубликованными метками в [центре соответствия Microsoft 365,](https://sip.protection.office.com/homepage)эти изменения политики автоматически не применяются к затронутым группам.

Если необходимо внести изменения, используйте [скрипт Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) для ручного применения обновлений к затронутым группам. Этот метод гарантирует, что все существующие группы обеспечивают соблюдение новой настройки.

## <a name="next-steps"></a>Дальнейшие действия

- [Используйте метки чувствительности с группами Майкрософт, группами Office 365 и сайтами SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Обновление групп после изменения политики меток вручную с помощью сценария Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Изменение параметров группы](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Управление группами с помощью команд PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
