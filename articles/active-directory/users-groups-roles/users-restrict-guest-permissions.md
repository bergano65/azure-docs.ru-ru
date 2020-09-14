---
title: Ограничить права доступа гостевых пользователей — Azure Active Directory | Документация Майкрософт
description: Ограничьте права доступа гостевых пользователей с помощью портал Azure, PowerShell или Microsoft Graph в Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 09/04/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418be35cb7996acaa7f11f37627d065451c9c7c6
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055220"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Ограничение разрешений гостевого доступа (Предварительная версия) в Azure Active Directory

Azure Active Directory (Azure AD) позволяет ограничить возможности внешних гостевых пользователей в Организации в Azure AD. Для гостевых пользователей по умолчанию задан ограниченный уровень разрешений в Azure AD, а по умолчанию для пользователей-членов — полный набор разрешений пользователя по умолчанию. Это предварительная версия нового уровня разрешений гостевого пользователя в настройках внешних параметров совместной работы Организации Azure AD для еще более ограниченного доступа, поэтому варианты гостевого доступа теперь:

Уровень разрешений         | Уровень доступа
----------------         | ------------
Как и пользователи членов     | Гости имеют тот же доступ к ресурсам Azure AD, что и пользователи, являющиеся членами
Ограниченный доступ (по умолчанию) | Гости могут видеть членство всех нескрытых групп
**Ограниченный доступ (новый)**  | **Гости не могут видеть членство в группах**

Если гостевой доступ ограничен, гости могут просматривать только свои профили пользователей. Разрешение на просмотр других пользователей запрещено, даже если гостевой Поиск выполняется по имени участника-пользователя или objectId. С помощью ограниченного доступа пользователи гостей могут видеть членство в группах, в которых они находятся. Дополнительные сведения об общих разрешениях пользователей по умолчанию, включая разрешения гостевых пользователей, см. [в разделе что такое разрешения пользователя по умолчанию в Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Разрешения и лицензии

Чтобы настроить параметры внешнего взаимодействия, необходимо быть в роли глобального администратора. Дополнительные требования к лицензированию для ограничения гостевого доступа отсутствуют.

## <a name="update-in-the-azure-portal"></a>Обновление в портал Azure

Мы внесли изменения в существующие элементы управления портал Azure для разрешений гостевого пользователя.

1. Войдите в [центр администрирования Azure AD](https://aad.portal.azure.com) с разрешениями глобального администратора.
1. На странице Обзор **Azure Active Directory** для своей организации выберите **Параметры пользователя**.
1. В разделе **внешние пользователи**выберите **Управление внешними параметрами совместной работы**.
1. На странице **внешние параметры совместной работы** выберите параметр **доступ к гостевым пользователям ограничен свойствами и членством собственных объектов каталога** .

    ![Страница параметров внешней совместной работы Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Выберите **Сохранить**. Изменения могут вступить в силу для гостевых пользователей до 15 минут.

## <a name="update-with-the-microsoft-graph-api"></a>Обновление с помощью Microsoft Graph API

Мы добавили новый API Microsoft Graph для настройки разрешений гостя в Организации Azure AD. Для назначения любого уровня разрешений можно выполнить следующие вызовы API. Значение для Гуестусерролеид, которое используется здесь, показывает наиболее ограниченный параметр гостевого пользователя. Дополнительные сведения об использовании Microsoft Graph для установки разрешений гостя см. в разделе [тип ресурса authorizationPolicy](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Настройка в первый раз

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Ответ должен быть успешным 204.

### <a name="updating-the-existing-value"></a>Обновление существующего значения

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Ответ должен быть успешным 204.

### <a name="view-the-current-value"></a>Просмотр текущего значения

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Пример ответа:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Обновление с помощью командлетов PowerShell

С помощью этой функции мы добавили возможность настройки ограниченных разрешений с помощью командлетов PowerShell версии 2. Командлеты PowerShell Get и Set опубликованы в версии 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get, команда: Get-Азуреадмсаусоризатионполици

Пример

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Команда Set: Set-Азуреадмсаусоризатионполици

Пример

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> При запросе необходимо ввести authorizationPolicy в качестве идентификатора.

## <a name="supported-microsoft-365-services"></a>Поддерживаемые службы Microsoft 365

### <a name="supported-services"></a>Поддерживаемые службы

В соответствии с поддерживаемыми возможностями мы имеем в виду, что они должны быть ожидаемыми. в частности, это то же самое, что и текущий гостевой интерфейс.

- Teams
- Outlook (OWA)
- SharePoint

### <a name="services-currently-not-supported"></a>Службы в настоящее время не поддерживаются

Служба без текущей поддержки может иметь проблемы совместимости с новым параметром ограничения гостевой системы.

- Формы
- Планировщик в командах
- Приложение планировщика
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Часто задаваемые вопросы

Вопрос | Ответ
-------- | ------
Где применяются эти разрешения? | Эти разрешения на уровне каталога применяются к службам и порталам Azure AD, включая Microsoft Graph, PowerShell v2, портал Azure и портале "Мои приложения". Также затрагиваются службы Microsoft 365, использующие группы Microsoft 365 для сценариев совместной работы, в частности Outlook, Microsoft Teams и SharePoint.
На какие части портала "Мои приложения" будет влиять эта функция? | Функциональные возможности групп на портале "Мои приложения" будут удовлетворять этим новым разрешениям. Сюда входят все пути для просмотра списка групп и членства в группах в "Мои приложения". В доступ к плитке группы не внесены изменения. Доступность плитки группы по-прежнему управляется существующим параметром группы на портале администрирования Azure.
Переопределяют ли эти разрешения гостевые параметры SharePoint или Microsoft Teams? | Нет. Эти существующие параметры по-прежнему управляют интерфейсом и доступом в этих приложениях. Например, если вы видите проблемы в SharePoint, проверьте параметры внешнего общего доступа.
Каковы известные проблемы совместимости в планировщике и в Yammer? | <li>Если для разрешений задано значение "ограничено", гости, вошедшие в Приложение планировщика или обращающиеся к планировщику в Microsoft Teams, не смогут получить доступ к своим планам и задачам.<li>Если для разрешений задано значение "ограничено", гости, вошедшие в Yammer, не смогут покинуть группу.
Будут ли мои существующие разрешения гостя изменены в моем клиенте? | В текущие параметры не внесены изменения. Мы поддерживаем обратную совместимость с существующими параметрами. Вы решите, когда нужно вносить изменения.
Будут ли эти разрешения установлены по умолчанию? | Нет. Существующие разрешения по умолчанию остаются без изменений. При необходимости можно задать более ограниченные разрешения.
Существуют ли требования к лицензиям для этой функции? | Нет, новые требования к лицензированию с этой функцией отсутствуют.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о существующих разрешениях гостя в Azure AD см. [в разделе что такое разрешения пользователя по умолчанию в Azure Active Directory?](../fundamentals/users-default-permissions.md).
- Дополнительные сведения о методах API Microsoft Graph, позволяющих ограничивать гостевой доступ, см. в разделе [тип ресурса authorizationPolicy](/graph/api/resources/authorizationpolicy).
- Сведения об отмене доступа для пользователя см. в статье [отзыв доступа пользователей в Azure AD](users-revoke-access.md).