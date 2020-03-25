---
title: Самостоятельная регистрация на портале для службы "Совместная работа B2B" — Azure AD
description: Служба совместной работы Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfa67f529408efcc2a703a4f80b15143c774f0b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77195799"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Самостоятельная регистрация на портале для службы совместной работы Azure AD B2B

С помощью встроенных функций, доступных на [портале Azure](https://portal.azure.com) и [панели доступа к приложениям](https://myapps.microsoft.com) для пользователей, клиенты могут выполнять разные операции. Тем не менее необходимо настроить рабочий процесс подключения для пользователей B2B в соответствии с потребностями вашей организации.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Управление правами Azure AD для регистрации гостевых пользователей B2B

Ваша приглашающая организация не может знать наперед, каким отдельным внешним сотрудникам необходимо получить доступ к вашим ресурсам. Вам нужно, чтобы пользователи из партнерских компаний могли сами регистрироваться с применением управляемых вами политик. Если вы хотите разрешить пользователям из других организаций запрашивать доступ, а при утверждении получать учетную запись гостя и назначение в группу, к приложениям и сайтам SharePoint Online, можно использовать [управление правами Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview), чтобы настроить политики, которые будут [управлять доступом для внешних пользователей](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>API-интерфейс B2B-приглашения Azure Active Directory

Организации могут использовать [API диспетчера приглашений Microsoft Graph](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) для создания собственных возможностей подключения гостевых пользователей B2B. Если вы хотите предложить самостоятельную регистрацию гостевых пользователей B2B, рекомендуется использовать [управление правами Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Но если вы хотите создать собственный интерфейс, можно использовать [API создания приглашения](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) для автоматической отправки настраиваемого сообщения с приглашением непосредственно пользователю B2B, например. Или ваше приложение может использовать inviteRedeemUrl, возвращенный в ответе на создание, чтобы создать собственное приглашение (с помощью выбранного механизма связи) приглашенному пользователю.

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое служба совместной работы Azure AD B2B?](what-is-b2b.md)
* [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](licensing-guidance.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](faq.md)
