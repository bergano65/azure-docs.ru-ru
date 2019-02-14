---
title: Аудит и отчеты для пользователей службы совместной работы Azure Active Directory B2B | Документация Майкрософт
description: Свойства гостевого пользователя службы совместной работы Azure Active Directory B2B можно настраивать.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc36128f8f998d78dd2cf9ef112fe5961bbef5b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204616"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Аудит и отчеты для пользователей службы совместной работы B2B
Для гостевых пользователей доступны такие же возможности аудита, как и для пользователей-участников. 

## <a name="access-reviews"></a>Проверки доступа
Проверки доступа можно использовать для периодических оценок необходимости доступа гостевых пользователей к вашим ресурсам. Функцию **Проверки доступа** можно найти в **Azure Active Directory** в разделе **Управление** > **Организационные связи**. (Также выполнить поиск по словам "проверки доступа" в группе **Все службы** на портале Azure). Сведения о том, как использовать проверки доступа, см. в разделе [Управление гостевым доступом с помощью проверок доступа Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Журналы аудита

Журналы аудита Azure AD содержат записи о системных и пользовательских действиях, в том числе действиях, инициированных гостевыми пользователями. Чтобы получить доступ к журналам аудита, в **Azure Active Directory** в разделе **Мониторинг** выберите **Журналы аудита**. Ниже приведен пример журнала приглашений и активаций приглашенного пользователя Сэма Угла (Sam Oogle).

![журнал аудита](./media/auditing-and-reporting/audit-log.png)

Вы можете подробней рассмотреть каждое из этих событий. Например, давайте просмотрим сведения о принятии приглашения.

![сведения о действии](./media/auditing-and-reporting/activity-details.png)

Вы можете также экспортировать эти журналы из Azure AD и с помощью любого инструмента для создания отчетов создавать собственные настраиваемые отчеты.

### <a name="next-steps"></a>Дополнительная информация

- [Свойства пользователя службы совместной работы Azure Active Directory B2B](user-properties.md)

