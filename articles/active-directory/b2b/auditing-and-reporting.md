---
title: Аудит и отчетность пользователя совместной работы B2B - Azure AD
description: Свойства гостевого пользователя службы совместной работы Azure Active Directory B2B можно настраивать.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273287"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Аудит и отчеты для пользователей службы совместной работы B2B
Для гостевых пользователей доступны такие же возможности аудита, как и для пользователей-участников. 

## <a name="access-reviews"></a>Проверки доступа
Проверки доступа можно использовать для периодических оценок необходимости доступа гостевых пользователей к вашим ресурсам. Функцию **Проверки доступа** можно найти в **Azure Active Directory** в разделе **Управление** > **Организационные связи**. (Вы также можете искать "обзоры доступа" от **всех служб** на портале Azure.) Чтобы узнать, как использовать отзывы о доступе, [см. Управление доступом для гостей с помощью обзоров доступа Azure AD.](../governance/manage-guest-access-with-access-reviews.md)

## <a name="audit-logs"></a>Журналы аудита

Журналы аудита Azure AD содержат записи о системных и пользовательских действиях, в том числе действиях, инициированных гостевыми пользователями. Чтобы получить доступ к журналам аудита, в **Azure Active Directory** в разделе **Мониторинг** выберите **Журналы аудита**. Ниже приведен пример журнала приглашений и активаций приглашенного пользователя Сэма Угла (Sam Oogle).

![Скриншот, показывающий и пример вывода журнала аудита](./media/auditing-and-reporting/audit-log.png)

Вы можете подробней рассмотреть каждое из этих событий. Например, давайте просмотрим сведения о принятии приглашения.

![Скриншот, показывающий и пример вывода деталей активности](./media/auditing-and-reporting/activity-details.png)

Вы можете также экспортировать эти журналы из Azure AD и с помощью любого инструмента для создания отчетов создавать собственные настраиваемые отчеты.

### <a name="next-steps"></a>Дальнейшие действия

- [Свойства пользователя службы совместной работы Azure Active Directory B2B](user-properties.md)

