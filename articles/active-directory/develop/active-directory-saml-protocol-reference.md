---
title: Как в Azure AD используется протокол SAML | Документация Майкрософт
description: В этой статье содержится обзор профилей SAML для единого входа и единого выхода в Azure Active Directory.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07d07f73412e889b018c1f667a500d7625912751
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546149"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Как в Azure AD используется протокол SAML

Azure Active Directory (Azure AD) использует протокол SAML 2.0, чтобы приложения могли предоставлять пользователям единый вход. Профили [единого входа](single-sign-on-saml-protocol.md) и [единого выхода](single-sign-out-saml-protocol.md) SAML в Azure AD содержат сведения о том, как в службе поставщика удостоверений используются утверждения SAML, протоколы и привязки.

Протокол SAML подразумевает, что поставщик удостоверений (Azure AD) и поставщик услуг (приложение) обмениваются информацией друг о друге.

При регистрации приложения в Azure AD разработчик приложения регистрирует в Azure AD сведения, относящиеся к федерации. К ним относятся **URI перенаправления** и **URI метаданных** для приложения.

Azure AD использует **URI метаданных** облачной службы, чтобы получить ключ подписывания и URI выхода. Пользователь может открыть приложение в разделе **Azure AD -> Регистрация приложения**, а затем обновить URL-адрес выхода в разделе **Параметры -> Свойства**. После этого Azure AD будет отправлять ответ по правильному URL-адресу. 

Azure Active Directory предоставляет клиентские и общие (единые для всех клиентов) конечные точки единого входа и единого выхода. Эти URL-адреса представляют собой адресуемые расположения, а не просто идентификаторы. Вы можете обратиться по ним к конечной точке для чтения метаданных.

* Конечная точка конкретного клиента находится по адресу `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. *\<TenantDomainName >* заполнитель представляет зарегистрированное доменное имя или GUID TenantID клиента Azure AD. Например, метаданные федерации клиента contoso.com находится здесь: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Независимая от клиента конечная точка расположена по адресу `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. В этом адресе конечной точки вместо доменного имени или идентификатора клиента содержится ключевое слово **common**.

Сведения о документах метаданных федерации, публикуемых в Azure AD, см. в статье [Метаданные федерации](azure-ad-federation-metadata.md).
