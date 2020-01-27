---
title: Протоколы проверки подлинности платформы Microsoft Identity | Документация Майкрософт
description: Обзор протоколов проверки подлинности, поддерживаемых платформой идентификации Майкрософт
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 43168ec7217d8f016857ba6dc54ca30bce2dd594
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699297"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Протоколы проверки подлинности платформы Microsoft Identity

Платформа Microsoft Identity поддерживает несколько наиболее широко используемых протоколов проверки подлинности и авторизации. В подразделах этого раздела описываются поддерживаемые протоколы и их реализация на платформе Microsoft Identity. Эти разделы содержат обзор поддерживаемых типов утверждений, основные сведения об использовании метаданных федерации, подробную документацию по протоколам OAuth 2.0. и SAML 2.0, а также советы по устранению неполадок.

## <a name="authentication-protocols-articles-and-reference"></a>Статьи и Справочник по протоколам проверки подлинности

* [Важная информация о смене ключей подписывания на платформе Microsoft Identity](active-directory-signing-key-rollover.md) . сведения о частоте смены ключей подписывания для платформы идентификации Майкрософт, изменения, которые можно внести для автоматического обновления ключа, и обсуждение способов обновления наиболее распространенных сценариев приложений.
* [Поддерживаемые типы маркеров и утверждений](id-tokens.md) . сведения об утверждениях в маркерах, устраняемых платформой идентификации Майкрософт.
* [Oauth 2,0 на платформе Microsoft Identity](v2-oauth2-auth-code-flow.md) — сведения о реализации OAuth 2,0 на платформе Microsoft Identity.
* [OpenID Connect 1.0](v2-protocols-oidc.md) — сведения об использовании протокола авторизации OAuth 2.0 для проверки подлинности.
* [Служба обслуживания вызовов с помощью учетных данных клиента](v2-oauth2-client-creds-grant-flow.md) — сведения об использовании потока предоставления учетных данных клиента OAuth 2.0 для вызовов между службами.
* [Служба обслуживания вызовов с помощью потока On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) — сведения об использовании потока On-Behalf-Of OAuth 2.0 для вызовов между службами.
* [Справочник по протоколу SAML](active-directory-saml-protocol-reference.md) . сведения о профилях единого входа и единого выхода для платформы Microsoft Identity.

## <a name="see-also"></a>См. также

* [Обзор платформы идентификации Майкрософт](v2-overview.md)
* [Примеры кода Active Directory](sample-v2-code.md)
