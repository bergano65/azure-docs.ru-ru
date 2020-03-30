---
title: Делегация и роли в управлении правами - Azure AD
description: Узнайте, как делегировать управление доступом от ИТ-администраторов руководителям отделов и руководителям проектов, чтобы они могли управлять доступом самостоятельно.
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261844"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Делегация и роли в управлении правами Azure AD

По умолчанию глобальные администраторы и администраторы пользователей могут создавать и управлять всеми аспектами управления правами Azure AD. Однако пользователи в этих ролях могут не знать всех ситуаций, когда требуются пакеты доступа. Обычно пользователи в соответствующих отделах, группах или проектах знают, с кем они сотрудничают, используя какие ресурсы и как долго. Вместо предоставления неограниченных разрешений неадминистраторам можно предоставить пользователям наименьшие разрешения, необходимые для выполнения их работы, и избежать создания противоречащих друг другу или неуместных прав доступа.

Это видео содержит обзор того, как делегировать управление доступом от ИТ-администратора пользователям, которые не являются администраторами.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Пример делегирования

Чтобы понять, как можно делегировать управление доступом в управлении правами, это помогает рассмотреть пример. Предположим, что в вашей организации есть следующие администраторы и менеджеры.

![Делегирование от ИТ-администратора к менеджерам](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Как ИТ-администратор, Хана имеет контакты в каждом отделе - Mamta в маркетинге, Марк в области финансов, и Джо в юридическом, которые отвечают за ресурсы своего отдела и бизнес-критических содержание.

При управлении правами вы можете делегировать управление доступом этим неадминистраторам, поскольку именно они знают, к каким пользователям нужен доступ, как долго и к каким ресурсам. Это гарантирует, что нужные люди управляют доступом для своих отделов.

Вот один из способов, что Хана может делегировать управление доступом в маркетинг, финансы и юридические отделы.

1. Хана создает новую группу безопасности Azure AD и добавляет Мамта, Марка и Джо в состав группы.

1. Хана добавляет, что группа в роли создателей каталога.

    Теперь Mamta, Mark и Joe могут создавать каталоги для своих отделов, добавлять ресурсы, необходимые их отделам, и делать дальнейшие делегирования в каталоге.

    Обратите внимание, что Мамта, Марк и Джо не могут видеть каталоги друг друга.

1. Mamta создает **каталог маркетинга,** который является контейнером ресурсов.

1. Мамта добавляет ресурсы, которыми владеет ее отдел маркетинга, в этот каталог.

1. Mamta может добавить дополнительных людей из своего отдела в качестве владельцев каталогов для этого каталога. Это помогает разделить обязанности по управлению каталогом.

1. Mamta может также делегировать создание и управление пакетами доступа в каталоге маркетинга руководителям проектов в отделе маркетинга. Она может сделать это, назначив их на роль менеджера пакетов доступа. Менеджер пакетов доступа может создавать и управлять пакетами доступа. 

На следующей диаграмме показаны каталоги с ресурсами для отдела маркетинга, финансов и юридических факультетов. Используя эти каталоги, менеджеры проектов могут создавать пакеты доступа для своих групп или проектов.

![Пример делегирования прав](./media/entitlement-management-delegate/elm-delegate.png)

После делегирования отдел маркетинга может иметь роли, аналогичные следующей таблице.

| Пользователь | Роль задания | Роль Azure AD | Роль управления правами |
| --- | --- | --- | --- |
| Hana | ИТ-администратор | глобальный администратор или администратор пользователей. |  |
| Мамта | Менеджер по маркетингу | Пользователь | Создатель каталога и владелец каталога |
| Владимир | Маркетинг свинца | Пользователь | Владелец каталога |
| Джессика | Менеджер маркетинговых проектов | Пользователь | Менеджер пакетов доступа |

## <a name="entitlement-management-roles"></a>Роли управления правами

Управление правами имеет следующие роли, которые специфичны для управления правами.

| Роль управления правами | Описание |
| --- | --- |
| Создатель каталога | Создавайте и управляйте каталогами. Обычно ИТ-администратор, который не является глобальным администратором или владельцем ресурсов для сбора ресурсов. Человек, создающий каталог, автоматически становится первым владельцем каталога и может добавить дополнительных владельцев каталогов. Создатель каталога не может управлять каталогами, которые они не владеют, и не может добавлять ресурсы, которыми он не владеет, в каталог. Если создателю каталога необходимо управлять другим каталогом или добавлять ресурсы, которыми он не владеет, он может запросить быть совладельцем этого каталога или ресурса. |
| Владелец каталога | Отоденять и управлять существующими каталогами. Обычно ИТ-администратор или владельцы ресурсов или пользователь, которого определил владелец каталога. |
| Менеджер пакетов доступа | Отоденять и управлять всеми существующими пакетами доступа в каталоге. |

Кроме того, назначенный утверждатель и запрашивает пакет доступа также имеют права, хотя они не являются ролями.

| Right | Описание |
| --- | --- |
| Утверждающий | Уполномоченный политикой утверждать или отменять запросы на доступ к пакетам, хотя они не могут изменить определения пакетов доступа. |
| Requestor | Авторизованной политикой пакета доступа для запроса этого пакета доступа. |

В следующей таблице перечислены задачи, которые могут выполнять роли управления правами.

| Задача | Административный | Создатель каталога | Владелец каталога | Менеджер пакетов доступа |
| --- | :---: | :---: | :---: | :---: |
| [Делегат создателю каталога](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Добавление подключенной организации](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Создать новый каталог](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Добавление ресурса в каталог](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Добавить владельца каталога](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Отодвить каталог](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Удалить каталог](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Делегат менеджеру пакетов доступа](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Удалить менеджер пакета доступа](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Создание нового пакета доступа в каталоге](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Изменение ролей ресурсов в пакете доступа](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Создание и отработка политик](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Непосредственно назначить пользователю пакет доступа](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Просмотр назначения пакета доступа](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Просмотр запросов пакета доступа](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Просмотр ошибок доставки запроса](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Переработать запрос](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Отмена ожидающего запроса](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Скрыть пакет доступа](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Удалить пакет доступа](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Необходимые роли для добавления ресурсов в каталог

Глобальный администратор может добавить или удалить любую группу (созданные облаком группы безопасности или созданные облаком Office 365 Groups), приложение или сайт SharePoint Online в каталоге. Администратор пользователя может добавить или удалить любую группу или приложение в каталоге.

Для пользователя, который не является глобальным администратором или администратором пользователя, чтобы добавить группы, приложения или сайты SharePoint Online в каталог, этот пользователь должен иметь *как* необходимую роль каталога Azure AD, так и роль управления правами владельца каталога. В следующей таблице перечислены комбинации ролей, необходимые для добавления ресурсов в каталог. Чтобы удалить ресурсы из каталога, необходимо иметь те же роли.

| Роль каталога Azure AD | Роль управления правами | Можно добавить группу безопасности | Можно добавить Office 365 Group | Можно добавить приложение | Можно добавить сайт SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Глобальный администратор](../users-groups-roles/directory-assign-admin-roles.md) | Недоступно |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Администратор пользователя](../users-groups-roles/directory-assign-admin-roles.md) | Недоступно |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Администратор intune](../users-groups-roles/directory-assign-admin-roles.md) | Владелец каталога | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Администратор Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Владелец каталога |  | :heavy_check_mark: |  |  |
| [Администратор службы команд](../users-groups-roles/directory-assign-admin-roles.md) | Владелец каталога |  | :heavy_check_mark: |  |  |
| [Администратор SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Владелец каталога |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Администратор приложения](../users-groups-roles/directory-assign-admin-roles.md) | Владелец каталога |  |  | :heavy_check_mark: |  |
| [Администратор облачных приложений](../users-groups-roles/directory-assign-admin-roles.md) | Владелец каталога |  |  | :heavy_check_mark: |  |
| Пользователь | Владелец каталога | Только в том случае, если владелец группы | Только в том случае, если владелец группы | Только если владелец приложения |  |

Чтобы определить наименьшую привилегированное роль для задачи, можно также ссылку на [роли администратора по задаче администратора в active-каталоге Azure.](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)

## <a name="next-steps"></a>Дальнейшие действия

- [Делегировать управление доступом создателям каталогов](entitlement-management-delegate-catalog.md)
- [Создание и управление каталогом ресурсов](entitlement-management-catalog-create.md)
