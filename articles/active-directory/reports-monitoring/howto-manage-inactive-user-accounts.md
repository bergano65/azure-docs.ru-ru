---
title: Как управлять неактивными учетными записями пользователей в Azure AD Документы Майкрософт
description: Узнайте о том, как обнаруживать учетные записи пользователей в Azure AD, которые устарели
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886047"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Как: Управление неактивными учетными записями пользователей в Azure AD

В больших средах учетные записи пользователей не всегда удаляются, когда сотрудники покидают организацию. Как ИТ-администратор, необходимо обнаруживать и обрабатывать эти устаревшие учетные записи пользователей, поскольку они представляют угрозу безопасности.

В этой статье объясняется метод обработки устаревших учетных записей пользователей в Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Что такое неактивные учетные записи пользователей?

Неактивные учетные записи являются учетными записями пользователей, которые больше не требуются членам вашей организации для получения доступа к вашим ресурсам. Одним из ключевых идентификаторов для неактивных учетных записей является то, что они не использовались *в течение некоторого времени* для входе в среду. Поскольку неактивные учетные записи привязаны к активности ввместь, можно использовать отметку времени последнего входной системы, которая была успешной для их обнаружения. 

Задача этого метода заключается в определении того, что *на некоторое время* означает в случае вашей среды. Например, пользователи могут не входить в среду *на некоторое время,* потому что они находятся в отпуске. При определении того, что ваша дельта для неактивных учетных записей пользователей, вы должны учитывать все законные причины не входить в вашу среду. Во многих организациях дельта для неактивных учетных записей пользователей составляет от 90 до 180 дней. 

Последний успешный вход обеспечивает потенциальную информацию о постоянной потребности пользователя в доступе к ресурсам.  Это может помочь с определением, если членство в группе или доступ к приложению по-прежнему необходимо или могут быть удалены. Для внешнего управления пользователем можно понять, активен ли внешний пользователь в рамках арендатора или должен быть очищен. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Как обнаружить неактивные учетные записи пользователей

Вы обнаруживаете неактивные учетные записи, оценивая **свойство lastSignInDate,** выставленное по типу ресурса **signInActivity** API **Microsoft Graph.** Используя это свойство, можно реализовать решение для следующих сценариев:

- **Пользователи по имени**: В этом сценарии вы ищете конкретного пользователя по имени, что позволяет оценить lastSignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Пользователи по дате**: В этом сценарии вы запрашиваете список пользователей с последнимSignInDateTime до указанной даты:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Это важно знать

В этом разделе перечислены данные о свойствах lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Как я могу получить доступ к этому свойству?

**Последнее свойствоSignInDateTime** подвергается [типу ресурса signInActivity](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) [aPI Microsoft Graph REST.](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph)   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Доступно ли последнее свойствоSignInDateTime через cmdlet Get-AzureAdUser?

Нет.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Какое издание Azure AD нужно мне получить доступ к свойству?

Вы можете получить доступ к этому свойству во всех изданиях Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Какое разрешение нужно для чтения имущества?

Чтобы прочитать это свойство, необходимо предоставить следующие права: 

- AuditLogs.Read.All
- Организация.Читать.Все  


### <a name="when-does-azure-ad-update-the-property"></a>Когда Azure AD обновляет свойство?

Каждый интерактивный входе, который был успешным, приводит к обновлению базового хранилища данных. Как правило, успешные входе отображаются в соответствующем отчете о входе в течение 10 минут.
 

### <a name="what-does-a-blank-property-value-mean"></a>Что означает значение пустого свойства?

Для создания метки времени lastSignInDate необходимо успешновойти в систему. Поскольку свойство lastSignInDateTime является новой функцией, значение свойства lastSignInDateTime может быть пустым, если:

- Последний успешный входе пользователя состоялся до выхода этой функции (1 декабря 2019 года).
- Затронутая учетная запись пользователя никогда не использовалась для успешного входини.

## <a name="next-steps"></a>Дальнейшие действия

* [Получение данных с помощью API отчетов Azure Active Directory с сертификатами](tutorial-access-api-with-certificates.md)
* [Справочник по API аудита](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Справочник по API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
