---
title: Проверка подлинности SAML с Azure Active Directory
description: Руководство по архитектуре для обеспечения проверки подлинности SAML с помощью Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ab14413de1f999747e5b3fb58b505e0a9258a55
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441221"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Проверка подлинности SAML с Azure Active Directory

Язык разметки зявлений системы безопасности (SAML) (SAML) — это открытый стандарт для обмена данными проверки подлинности и авторизации между поставщиком удостоверений и поставщиком услуг. SAML — это язык разметки на основе XML для утверждений безопасности, которые являются операторами, используемыми поставщиками служб для принятия решений по управлению доступом. 

Спецификация SAML определяет три роли:

* Участник, как правило, является пользователем
* Поставщик удостоверений (IdP)
* Поставщик услуг (SP)


## <a name="use-when"></a>Используется, если

Для корпоративного приложения SAML необходимо предоставить единый вход (SSO).

Хотя одним из наиболее важных вариантов использования SAML является единый вход, особенно путем расширения единого входа в разных доменах безопасности, существуют и другие варианты использования (называемые профилями). 

![Схема архитектуры для SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Компоненты системы

* **Пользователь**: запрашивает службу из приложения.

* **Веб-браузер**: компонент, с которым взаимодействует пользователь.

* **Веб-приложение**: корпоративное приложение, поддерживающее SAML и использующее Azure AD в качестве IDP.

* **Token**: утверждение SAML (также называемое маркерами SAML), которое несет наборы заявок, выполняемых IDP о принципе (пользователя). Он содержит сведения о проверке подлинности, атрибуты и инструкции по авторизации.

* **Azure AD**— корпоративный Cloud IDP, который предоставляет единый вход и многофакторную идентификацию для приложений SAML. Он синхронизирует, обслуживает и управляет сведениями об удостоверениях для пользователей, а также предоставляет службы проверки подлинности для проверяющих приложений. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Реализация проверки подлинности SAML с помощью Azure AD

* [Руководства по интеграции приложений SaaS с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Настройка единого входа на основе SAML для приложений не из коллекции](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [Как в Azure AD используется протокол SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
