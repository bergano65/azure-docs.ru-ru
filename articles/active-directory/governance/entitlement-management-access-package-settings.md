---
title: Ссылка на общий доступ для запроса пакета Access в управлении назначением Azure AD (Предварительная версия) — Azure Active Directory
description: Узнайте, как поделиться ссылкой для запроса пакета Access в Azure Active Directory управления назначением (Предварительная версия).
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
ms.openlocfilehash: 91b98df1f93991d4781283bc38b02dc20d11268d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392336"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management-preview"></a>Ссылка на общий доступ для запроса пакета Access в управлении назначением Azure AD (Предварительная версия)

> [!IMPORTANT]
> Управление правами Azure Active Directory (Azure AD) сейчас предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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