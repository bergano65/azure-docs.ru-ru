---
title: Самостоятельная регистрация на портале для службы "Совместная работа B2B" — Azure AD
description: Узнайте, как настроить рабочий процесс адаптации для Azure Active Directory пользователей B2B в соответствии с потребностями вашей организации.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9cd421224ad134b4df853b06b3bc34333a0613
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896112"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Самостоятельная регистрация на портале для службы совместной работы Azure AD B2B

С помощью встроенных функций, доступных на [портале Azure](https://portal.azure.com) и [панели доступа к приложениям](https://myapps.microsoft.com) для пользователей, клиенты могут выполнять разные операции. Тем не менее необходимо настроить рабочий процесс подключения для пользователей B2B в соответствии с потребностями вашей организации.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Управление правами Azure AD для регистрации гостевых пользователей B2B

Ваша приглашающая организация не может знать наперед, каким отдельным внешним сотрудникам необходимо получить доступ к вашим ресурсам. Вам нужно, чтобы пользователи из партнерских компаний могли сами регистрироваться с применением управляемых вами политик. Если вы хотите разрешить пользователям из других организаций запрашивать доступ, а при утверждении получать учетную запись гостя и назначение в группу, к приложениям и сайтам SharePoint Online, можно использовать [управление правами Azure AD](../governance/entitlement-management-overview.md), чтобы настроить политики, которые будут [управлять доступом для внешних пользователей](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>API-интерфейс B2B-приглашения Azure Active Directory

Организации могут использовать [API диспетчера приглашений Microsoft Graph](/graph/api/resources/invitation?view=graph-rest-1.0) для создания собственных возможностей подключения гостевых пользователей B2B. Если вы хотите предложить самостоятельную регистрацию гостевых пользователей B2B, рекомендуется использовать [управление правами Azure AD](../governance/entitlement-management-overview.md). Но если вы хотите создать собственный интерфейс, можно использовать [API создания приглашения](/graph/api/invitation-post?tabs=http&view=graph-rest-1.0) для автоматической отправки настраиваемого сообщения с приглашением непосредственно пользователю B2B, например. Или ваше приложение может использовать inviteRedeemUrl, возвращенный в ответе на создание, чтобы создать собственное приглашение (с помощью выбранного механизма связи) приглашенному пользователю.

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое служба совместной работы Azure AD B2B?](what-is-b2b.md)
* [Цены на внешние удостоверения](external-identities-pricing.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](faq.md)