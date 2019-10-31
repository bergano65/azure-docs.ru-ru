---
title: Ссылка на общий доступ для запроса пакета Access в управлении назначением Azure AD — Azure Active Directory
description: Узнайте, как поделиться ссылкой для запроса пакета Access в Azure Active Directory управлении обслуживанием.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94d32bf26def6832f51b4e09c697bca885162dc2
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174418"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Ссылка на общий доступ для запроса пакета Access в управлении назначением Azure AD

Большинство пользователей в каталоге могут войти на портал My Access и автоматически просмотреть список пакетов Access, которые они могут запросить. Однако для пользователей внешних бизнес-партнеров, которые еще не находятся в каталоге, необходимо отправить им ссылку, которую можно использовать для запроса пакета Access. 

Если каталог для пакета Access [включен для внешних пользователей](entitlement-management-catalog-create.md) , а у вас есть [Политика для каталога внешнего пользователя](entitlement-management-access-package-request-policy.md), внешний пользователь может использовать ссылку на портал доступа для запроса пакета Access.

## <a name="share-link-to-request-an-access-package"></a>Ссылка на общий доступ для запроса пакета Access

**Предварительная роль:** Глобальный администратор, администратор пользователей, владелец каталога или диспетчер пакетов Access

1. На портале Azure щелкните **Azure Active Directory** и выберите **Управление удостоверениями**.

1. В меню слева щелкните **доступ к пакетам** и откройте пакет Access.

1. На странице Обзор скопируйте **ссылку на портал My Access**.

    ![Обзор пакета для доступа со ссылкой на портал "Мой доступ"](./media/entitlement-management-shared/my-access-portal-link.png)

    При отправке ИТ-партнеру для внутреннего делового партнера важно скопировать всю ссылку на портал My Access. Это гарантирует, что партнер получит доступ к порталу вашего каталога, чтобы выполнить свой запрос. Ссылка начинается с `myaccess`, включает указание каталога и заканчивается ИДЕНТИФИКАТОРом пакета доступа.

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Отправьте ссылку на ваш внешний бизнес-партнер по электронной почте. Они могут поделиться ссылкой с пользователями, чтобы запросить пакет Access.

## <a name="next-steps"></a>Дальнейшие действия

- [Запрос доступа к пакету Access](entitlement-management-request-access.md)
- [Утверждение или отклонение запросов на доступ](entitlement-management-request-approve.md)