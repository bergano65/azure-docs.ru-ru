---
title: Общие сценарии в управлении правами - Azure AD
description: Изучите высококачественные шаги, которые следует выполнять для общих сценариев в управлении правами На Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190557"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Общие сценарии в управлении правами Azure AD

Существует несколько способов настройки управления правами для организации. Однако, если вы только начинаете работу, полезно понять общие сценарии для администраторов, владельцев каталогов, менеджеров пакетов доступа, одобрителей и запросов.

## <a name="delegate"></a>Делегат

### <a name="administrator-delegate-management-of-resources"></a>Администратор: Управление ресурсами делегатов

1. [Смотреть видео: Делегирование от ИТ до менеджера отдела](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Делегировать пользователей роли создателя каталога](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Создатель каталога: Управление ресурсами делегатов

- [Создать новый каталог](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Владелец каталога: Управление ресурсами делегатов

1. [Добавление совладельцев в каталог](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Добавление ресурсов в каталог](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Владелец каталога: Управление пакетами доступа

1. [Смотреть видео: Делегация от владельца каталога к менеджеру пакетов доступа](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Делегировать пользователей к роли менеджера пакетов](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Управление доступом для пользователей в вашей организации

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Менеджер пакетов доступа: Разрешить сотрудникам в вашей организации запрашивать доступ к ресурсам

1. [Создание пакета для доступа](entitlement-management-access-package-create.md#start-new-access-package)
1. [Добавление групп, команд, приложений или сайтов SharePoint для доступа к пакету](entitlement-management-access-package-create.md#resource-roles)
1. [Добавьте политику запроса, чтобы пользователи в каталоге запросили доступ](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Указать параметры истечения срока действия](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Запрос: Запрос доступа к ресурсам

1. [Войти на портал "Мой доступ"](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Найти пакет доступа
1. [Запрос доступа](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Утверждение: Утверждение запросов на ресурсы

1. [Открытый запрос на портале "Мой доступ"](entitlement-management-request-approve.md#open-request)
1. [Утвердить или отклонить запрос на доступ](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Запрос: Просмотр ресурсов, к которые у вас уже есть доступ

1. [Войти на портал "Мой доступ"](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Просмотр пакетов активного доступа

## <a name="govern-access-for-users-outside-your-organization"></a>Управление доступом для пользователей за пределами организации

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Администратор: Сотрудничество с внешней организацией-партнером

1. [Узнайте, как работает доступ для внешних пользователей](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Параметры просмотра для внешних пользователей](entitlement-management-external-users.md#settings-for-external-users)
1. [Добавление подключения к внешней организации](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Менеджер пакетов доступа: Сотрудничество с внешней организацией-партнером

1. [Создание пакета для доступа](entitlement-management-access-package-create.md#start-new-access-package)
1. [Добавление групп, команд, приложений или сайтов SharePoint для доступа к пакету](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Добавьте политику запроса, чтобы пользователи, не веланные в каталоге, запросили доступ](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Указать параметры истечения срока действия](entitlement-management-access-package-create.md#lifecycle)
1. [Копирование ссылки для запроса пакета доступа](entitlement-management-access-package-settings.md)
1. Отправить ссылку на вашего внешнего партнера контактного партнера, чтобы поделиться со своими пользователями

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Запрос: Запрос доступа к ресурсам в качестве внешнего пользователя

1. Найти ссылку пакета доступа, полученную от вашего контакта
1. [Войти на портал "Мой доступ"](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Запрос доступа](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Утверждение: Утверждение запросов на ресурсы

1. [Открытый запрос на портале "Мой доступ"](entitlement-management-request-approve.md#open-request)
1. [Утвердить или отклонить запрос на доступ](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Запрос: Просмотр ресурсов, к которые уже имеется доступ к вам

1. [Войти на портал "Мой доступ"](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Просмотр пакетов активного доступа

## <a name="day-to-day-management"></a>Повседневное управление

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Менеджер пакетов доступа: Обновление ресурсов для проекта

1. [Смотреть видео: Повседневное управление: все изменилось](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Открыть пакет доступа
1. [Добавление или удаление групп, групп, приложений или сайтов SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Менеджер пакетов доступа: Обновление продолжительности проекта

1. [Смотреть видео: Повседневное управление: все изменилось](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Открыть пакет доступа
1. [Откройте настройки жизненного цикла](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Обновление параметров истечения срока действия](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Менеджер пакетов доступа: Обновите способ утверждения доступа для проекта

1. [Смотреть видео: Повседневное управление: все изменилось](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Откройте существующую политику параметров запроса и утверждения](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Обновление настроек утверждения](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Менеджер пакетов доступа: Обновление людей для проекта

1. [Смотреть видео: Повседневное управление: все изменилось](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Удалить пользователей, которым больше не нужен доступ](entitlement-management-access-package-assignments.md)
1. [Откройте существующую политику параметров запроса и утверждения](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Добавление пользователей, которым необходим доступ](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Менеджер пакетов доступа: Непосредственно присваивать конкретных пользователей пакету доступа

1. [Если пользователям нужны различные параметры жизненного цикла, добавьте новую политику в пакет доступа](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Непосредственно присваивать конкретных пользователей пакету доступа](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Назначения и отчеты

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Администратор: Просмотр назначений в пакет доступа

1. Открыть пакет доступа
1. [Просмотр заданий](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Архивные отчеты и журналы](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Администратор: Просмотр ресурсов, назначенных пользователям

1. [Просмотр пакетов доступа для пользователя](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Просмотр заданий ресурсов для пользователя](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Программное администрирование

Вы также можете управлять пакетами доступа, каталогами, политиками, запросами и назначениями с помощью Microsoft Graph.  Пользователь в соответствующей роли с приложением `EntitlementManagement.ReadWrite.All` с делегированным разрешением может позвонить в [API управления правами.](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)

## <a name="next-steps"></a>Дальнейшие действия

- [Делегирование и роли](entitlement-management-delegate.md)
- [Запрос процесса и уведомления по электронной почте](entitlement-management-process.md)
