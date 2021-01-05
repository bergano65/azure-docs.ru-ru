---
title: Сценарий обмена маркерами платформы идентификации Microsoft Identity с SAML и OIDC/OAuth в Azure Active Directory
description: Сведения об общих сценариях обмена маркерами при работе с SAML и OIDC/OAuth в Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 5841af4225f88b36fe727b60a597af208ac830f5
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827178"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Сценарии обмена маркерами платформы идентификации Microsoft Identity с SAML и OIDC/OAuth

SAML и OpenID Connect Connect (OIDC)/OAuth — это популярные протоколы, используемые для реализации единого Sign-On (SSO). Некоторые приложения могут реализовывать только SAML, а другие могут только реализовать OIDC/OAuth. Оба протокола используют токены для обмена секретами. Дополнительные сведения о SAML см. в разделе [Single Sign-On протокол SAML](single-sign-on-saml-protocol.md). Дополнительные сведения о OIDC/OAuth см. в статье [протоколы oauth 2,0 и OpenID Connect Connect на платформе Microsoft Identity](active-directory-v2-protocols.md).

В этой статье описывается распространенный сценарий, в котором приложение реализует SAML, но необходимо вызвать API Graph, в котором используется OIDC/OAuth. Для пользователей, работающих с этим сценарием, предоставляется основное руководство.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Сценарий. у вас есть токен SAML, который требуется вызвать API Graph
Многие приложения реализуются с помощью SAML. Однако API Graph использует протоколы OIDC/OAuth. Можно, хотя и не тривиальны, добавлять функции OIDC/OAuth в приложение SAML. После того как функциональные возможности OAuth доступны в приложении, можно использовать API Graph.

Общая стратегия заключается в добавлении в приложение стека OIDC/OAuth. В приложении, в котором реализованы оба стандарта, можно использовать файл cookie сеанса. Вы не обмениваетесь явным образом с маркером. Вы ведете вход пользователя в систему с помощью SAML, который создает файл cookie сеанса. Когда API Graph вызывает поток OAuth, для проверки подлинности используется файл cookie сеанса. Эта стратегия предполагает, что проверки условного доступа проходят успешно и пользователь прошел проверку подлинности.

> [!NOTE]
> Рекомендуемой библиотекой для добавления поведения OIDC/OAuth является библиотека проверки подлинности Майкрософт (MSAL). Дополнительные сведения о MSAL см. в статье [Обзор библиотеки проверки подлинности Майкрософт (MSAL)](msal-overview.md). Предыдущая библиотека называлась Библиотека проверки подлинности Active Directory (ADAL), но не рекомендуется, так как MSAL заменяет ее.

## <a name="next-steps"></a>Дальнейшие действия
- [Потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md)
