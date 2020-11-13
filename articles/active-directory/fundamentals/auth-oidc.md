---
title: OpenID Connect подключение проверки подлинности с помощью Azure Active Directory
description: Рекомендации по архитектуре для обеспечения аутентификации OpenID Connect Connect с помощью Azure Active Directory.
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
ms.openlocfilehash: f38da0dd2dc2adc8049e2b307c861651a55ed700
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576994"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>OpenID Connect подключение проверки подлинности с помощью Azure Active Directory

OpenID Connect Connect (OIDC) — это протокол проверки подлинности, основанный на протоколе OAuth2 (который используется для авторизации). OIDC использует стандартизованные потоки сообщений от OAuth2 для предоставления служб удостоверений. 

Целью разработки OIDC является «создание простых и сложных вещей». OIDC позволяет разработчикам проходить проверку подлинности пользователей на веб-сайтах и в приложениях без необходимости владельца файлов паролей и управления ими. Это позволяет построителю приложений обеспечить безопасный способ проверки удостоверения лица, которое в настоящее время использует браузер или собственное приложение, подключенное к приложению.

Проверка подлинности пользователя должна выполняться в поставщике удостоверений, где будут проверяться сеанс или учетные данные пользователя. Для этого необходим доверенный агент. Собственные приложения обычно запускают браузер системы для этой цели. Внедренные представления считаются ненадежными, так как нет ничего препятствовать тому, что приложение будет подноситься к паролю пользователя. 

Помимо проверки подлинности пользователь может запросить согласие. Согласие — это явное разрешение пользователя, позволяющее приложению получать доступ к защищенным ресурсам. Согласие отличается от проверки подлинности, поскольку для ресурса необходимо предоставить согласие только один раз. Согласие остается действительным до тех пор, пока пользователь или администратор не отменят предоставление прав вручную. 

## <a name="use-when"></a>Используется, если

Требуется согласие пользователя и для входа в веб-систему.

![Схема архитектуры](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Компоненты системы

* **Пользователь** : запрашивает службу из приложения.

* **Доверенный агент**. компонент, с которым взаимодействует пользователь. Этот доверенный агент обычно является веб-браузером.

* **Приложение** : приложение или сервер ресурсов, где находится ресурс или данные. Он доверяет поставщику удостоверений для безопасной проверки подлинности и авторизации доверенного агента. 

* **Azure AD**. поставщик OIDC, также известный как поставщик удостоверений, безопасно управляет любыми данными пользователя, их доступом и отношениями доверия между сторонами в последовательности. Он проверяет подлинность пользователя, предоставляет и отзывает доступ к ресурсам, а также выдает маркеры. 

## <a name="implement-oidc-with-azure-ad"></a>Реализация OIDC с помощью Azure AD

* [Интеграция приложений с Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Протоколы OAuth 2,0 и OpenID Connect Connect на платформе Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Платформа удостоверений Майкрософт и протокол OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 

* [Вход в веб-приложения с помощью OpenID Connect в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/openid-connect) 

* [Защита приложения с помощью OpenID Connect и Azure AD](https://docs.microsoft.com/learn/modules/secure-app-with-oidc-and-azure-ad/) 

 
