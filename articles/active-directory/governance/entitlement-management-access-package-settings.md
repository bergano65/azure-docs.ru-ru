---
title: Поделиться ссылкой, чтобы запросить пакет доступа в управлении правами Azure AD - Активный каталог Azure
description: Узнайте, как обмениваться ссылками для запроса пакета доступа в управлении правами На Azure Active Directory.
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
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968767"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Поделиться ссылкой для запроса пакета доступа в управлении правами Azure AD

Большинство пользователей в вашем каталоге могут войти на портал My Access и автоматически увидеть список пакетов доступа, которые они могут запросить. Однако для пользователей внешних бизнес-партнеров, которые еще не находятся в вашем каталоге, вам нужно будет отправить им ссылку, которую они могут использовать для запроса пакета доступа. 

До тех пор, как каталог для пакета доступа [включен для внешних пользователей,](entitlement-management-catalog-create.md) и у вас есть [политика для каталога внешнего пользователя,](entitlement-management-access-package-request-policy.md)внешний пользователь может использовать ссылку портала My Access для запроса пакета доступа.

## <a name="share-link-to-request-an-access-package"></a>Поделиться ссылкой для запроса пакета доступа

**Роль предпосылки:** Глобальный администратор, администратор пользователей, владелец каталога или менеджер пакетов доступа

1. На портале Azure щелкните **Azure Active Directory** и выберите **Управление удостоверениями**.

1. В левом меню щелкните **пакеты Access,** а затем откройте пакет доступа.

1. На странице Обзор скопируйте **ссылку на портал «Мой доступ».**

    ![Обзор пакета для доступа со ссылкой на портал "Мой доступ"](./media/entitlement-management-shared/my-access-portal-link.png)

    Важно, чтобы вы скопировали всю ссылку портала My Access при отправке ее внутреннему деловому партнеру. Это гарантирует, что партнер получит доступ к порталу вашего каталога, чтобы сделать их запрос. Ссылка начинается с, `myaccess`включает подсказку каталога и заканчивается идентификатором пакета доступа.  (Для правительства США, домен в ссылке `myaccess.microsoft.us`портала My Access будет .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Отправьте или отправьте ссылку своему внешнему деловому партнеру. Они могут поделиться ссылкой со своими пользователями, чтобы запросить пакет доступа.

## <a name="next-steps"></a>Дальнейшие действия

- [Запрос доступа к пакету доступа](entitlement-management-request-access.md)
- [Утверждение или отказ в запросах на доступ](entitlement-management-request-approve.md)