---
title: Изменение параметров жизненного цикла для пакета доступа в управлении правами Azure AD - Активный каталог Azure
description: Узнайте, как изменить параметры жизненного цикла для пакета доступа в управлении правами Azure Active Directory.
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261987"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Изменение параметров жизненного цикла для пакета доступа в управлении правами Azure AD

Как менеджер пакетов доступа, вы можете изменить параметры жизненного цикла для пакета доступа в любое время, редактируя существующую политику. Если вы измените срок действия политики, срок действия запросов, которые уже находятся в состоянии утверждения или утверждения, не изменится.

В этой статье описывается, как изменить параметры жизненного цикла для существующего пакета доступа.

## <a name="open-lifecycle-settings"></a>Настройки открытого жизненного цикла

Чтобы изменить параметры жизненного цикла для пакета доступа, необходимо открыть соответствующую политику. Выполните следующие действия, чтобы открыть параметры жизненного цикла для пакета доступа.

**Роль предпосылки:** Глобальный администратор, администратор пользователей, владелец каталога или менеджер пакетов доступа

1. На портале Azure щелкните **Azure Active Directory** и выберите **Управление удостоверениями**.

1. В левом меню щелкните **пакеты Access,** а затем откройте пакет доступа.

1. Нажмите **«Политики»,** а затем нажмите на политику, которая имеет параметры жизненного цикла, которые вы хотите отобразить.

    Панель деталей политики открывается в нижней части страницы.

    ![Пакет доступа - Панель деталей политики](./media/entitlement-management-shared/policy-details.png)

1. Нажмите **"Оторите",** чтобы отсвануть политику.

    ![Пакет доступа - Политика отсылки](./media/entitlement-management-shared/policy-edit.png)

1. Нажмите на вкладку **Lifecycle,** чтобы открыть параметры жизненного цикла.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Изменение параметров запроса и утверждения пакета доступа](entitlement-management-access-package-request-policy.md)