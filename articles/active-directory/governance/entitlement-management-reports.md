---
title: Просмотр отчетов и журналов в управление правами Azure AD (Предварительная версия) — Azure Active Directory
description: Узнайте, как просматривать отчет назначения пользователей и журналы аудита на управление правами Azure Active Directory (Предварительная версия).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541545"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Просмотр отчетов и журналов в управление правами Azure AD (Предварительная версия)

> [!IMPORTANT]
> Управление правами Azure Active Directory (Azure AD) в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Просмотр ресурсов, которым пользователь имеет доступ

1. Нажмите кнопку **Azure Active Directory** и нажмите кнопку **функции управления удостоверениями**.

1. В меню слева щелкните **отчет назначения пользователей**.

1. Нажмите кнопку **выберите пользователей** чтобы открыть область выбранных пользователей.

1. Найдите пользователя в списке, который вы хотите просмотреть ресурсы, к которым они имеют доступ.

1. Выберите пользователя, а затем нажмите кнопку **выберите**.

    Отображается список ресурсов, к которым пользователь имеет доступ к. Он включает в себя пакет доступа, политики и дат.

    ![Отчет назначения пользователей](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Определить состояние запроса пользователя

Чтобы получить дополнительные сведения о запрошенных и получили доступ к пакету доступ пользователя, можно использовать журнал аудита Azure AD. В частности, можно использовать записи журнала в `EntitlementManagement` и `UserManagement` категории, чтобы получить дополнительные сведения об этапах обработки для каждого запроса.  

1. Нажмите кнопку **Azure Active Directory** и нажмите кнопку **журналы аудита**.

1. В верхней, измените **категории** либо `EntitlementManagement` или `UserManagement`в зависимости от записи аудита, который вы ищете.  

1. Нажмите кнопку **Применить**.

1. Чтобы скачать журналы, щелкните **загрузить**.

Когда Azure AD получает новый запрос, он записывает записи аудита, в котором **категории** — `EntitlementManagement` и **действия** обычно `User requests access package assignment`.  В случае прямого назначения, созданные на портале Azure **действия** поле запись аудита имеет `Administrator directly assigns user to access package`, и пользователь, выполняющий назначения идентифицируется по **ActorUserPrincipalName**.

Azure AD будет записывать дополнительные записи запроса во время выполнения, включая:

| Category | Действие | Request status (Состояние запроса) |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Запрос не требует утверждения |
| `UserManagement` | `Create request approval` | Запрос требует утверждения |
| `UserManagement` | `Add approver to request approval` | Запрос требует утверждения |
| `EntitlementManagement` | `Approve access package assignment request` | Запрос утвержден |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Запрос утвержден, или не требует утверждения |

При назначении доступ пользователя Azure AD записывает записи аудита для `EntitlementManagement` категории с **действия** `Fulfill access package assignment`.  Пользователь, который получил доступ определяется **ActorUserPrincipalName** поля.

Если доступ не было присвоено, то Azure AD записывает записи аудита для `EntitlementManagement` категории с **действия** либо `Deny access package assignment request`, если запрос был отклонен, утверждающее лицо или `Access package assignment request timed out (no approver action taken)`, если запрос истекло время ожидания перед Утверждающее лицо может утвердить.

По истечении срока действия пакетное назначение доступа пользователя отменена пользователем, или удалено администратором, а затем Azure AD записывает записи аудита для `EntitlementManagement` категории с **действия** из `Remove access package assignment`.

## <a name="next-steps"></a>Дальнейшие действия

- [Устранение неполадок управления прав Azure AD](entitlement-management-troubleshoot.md)
- [Распространенные сценарии](entitlement-management-scenarios.md)
