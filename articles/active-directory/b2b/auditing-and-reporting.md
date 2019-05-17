---
title: Аудит и отчеты пользователя службы совместной работы B2B — Azure Active Directory | Документация Майкрософт
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
ms.openlocfilehash: 506721a23a5a18eaea76efaca61df49f20dc9228
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812466"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Аудит и отчеты для пользователей службы совместной работы B2B
Для гостевых пользователей доступны такие же возможности аудита, как и для пользователей-участников. 

## <a name="access-reviews"></a>Проверки доступа
Проверки доступа можно использовать для периодических оценок необходимости доступа гостевых пользователей к вашим ресурсам. Функцию **Проверки доступа** можно найти в **Azure Active Directory** в разделе **Управление** > **Организационные связи**. (Также выполнить поиск по словам "проверки доступа" в группе **Все службы** на портале Azure). Сведения о том, как использовать проверки доступа, см. в разделе [Управление гостевым доступом с помощью проверок доступа Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Журналы аудита

Журналы аудита Azure AD содержат записи о системных и пользовательских действиях, в том числе действиях, инициированных гостевыми пользователями. Чтобы получить доступ к журналам аудита, в **Azure Active Directory** в разделе **Мониторинг** выберите **Журналы аудита**. Ниже приведен пример журнала приглашений и активаций приглашенного пользователя Сэма Угла (Sam Oogle).

![Снимок экрана, показывающий и примером аудита записи выходных данных](./media/auditing-and-reporting/audit-log.png)

Вы можете подробней рассмотреть каждое из этих событий. Например, давайте просмотрим сведения о принятии приглашения.

![Снимок экрана, показывающий и пример выходных данных действия сведения](./media/auditing-and-reporting/activity-details.png)

Вы можете также экспортировать эти журналы из Azure AD и с помощью любого инструмента для создания отчетов создавать собственные настраиваемые отчеты.

### <a name="next-steps"></a>Дальнейшие действия

- [Свойства пользователя службы совместной работы Azure Active Directory B2B](user-properties.md)

