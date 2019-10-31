---
title: Изменение параметров жизненного цикла для пакета Access в управлении назначениями Azure AD — Azure Active Directory
description: Узнайте, как изменить параметры жизненного цикла пакета Access в Azure Active Directory управлении обслуживанием.
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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174735"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Изменение параметров жизненного цикла пакета Access в управлении назначениями Azure AD

В качестве диспетчера пакетов доступа можно изменить параметры жизненного цикла пакета доступа в любое время, изменив существующую политику. Если изменить дату истечения срока действия политики, то дата истечения срока действия запросов, которые уже находятся в состоянии ожидания утверждения или утверждения, не изменится.

В этой статье описывается, как изменить параметры жизненного цикла для существующего пакета Access.

## <a name="open-lifecycle-settings"></a>Открыть параметры жизненного цикла

Чтобы изменить параметры жизненного цикла пакета доступа, необходимо открыть соответствующую политику. Выполните следующие действия, чтобы открыть параметры жизненного цикла пакета Access.

**Предварительная роль:** Глобальный администратор, администратор пользователей, владелец каталога или диспетчер пакетов Access

1. На портале Azure щелкните **Azure Active Directory** и выберите **Управление удостоверениями**.

1. В меню слева щелкните **доступ к пакетам** и откройте пакет Access.

1. Щелкните **политики** , а затем выберите политику, для которой необходимо изменить параметры жизненного цикла.

    В нижней части страницы откроется область сведения о политике.

    ![Доступ к области сведений о политике пакета](./media/entitlement-management-shared/policy-details.png)

1. Нажмите кнопку **изменить** , чтобы изменить политику.

    ![Доступ к пакету — изменение политики](./media/entitlement-management-shared/policy-edit.png)

1. Перейдите на вкладку **жизненный** цикл, чтобы открыть параметры жизненного цикла.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Изменение параметров запроса и утверждения для пакета Access](entitlement-management-access-package-request-policy.md)