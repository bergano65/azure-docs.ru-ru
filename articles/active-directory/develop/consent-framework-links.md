---
title: Как действует согласие для приложения
description: Дополнительные сведения о действии инфраструктуры согласия Azure AD, а также о том, как вы можете использовать ее при разработке приложений в Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.openlocfilehash: 15270a998aff174c04acf2969d984eb022852635
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117368"
---
# <a name="how-application-consent-works"></a>Как действует согласие для приложения

Эта статья содержит дополнительные сведения о действии инфраструктуры согласия Azure AD, используя которые вы сможете оптимизировать разработку приложений.

## <a name="recommended-documents"></a>Рекомендуемые документы

- Получите общее представление о том, [как владелец ресурса может управлять доступом приложения к ресурсам благодаря согласию](./developer-glossary.md#consent).
- Ознакомьтесь с пошаговым руководством по [реализации согласия в инфраструктуре согласия Azure AD](./quickstart-register-app.md).
- Получите более подробные сведения об [использовании инфраструктуры согласия в мультитенантном приложении](./howto-convert-app-to-be-multi-tenant.md), чтобы реализовать согласие пользователя и администратора, с поддержкой нескольких дополнительных шаблонов многоуровневого приложения.
- Узнайте подробнее о том, [как согласие поддерживается на уровне протокола OAuth 2.0 во время предоставления кода авторизации.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>Дальнейшие действия
[StackOverflow в AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)