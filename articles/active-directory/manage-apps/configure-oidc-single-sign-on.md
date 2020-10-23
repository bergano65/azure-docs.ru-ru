---
title: Общие сведения об единого входа (SSO) на основе OIDC для приложений в Azure Active Directory
description: Общие сведения об единого входа (SSO) на основе OIDC для приложений в Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperfq2
ms.openlocfilehash: e819073c3bc723a66f8beb2f9b2a094a07233ca9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427681"
---
# <a name="understand-oidc-based-single-sign-on"></a>Общие сведения о едином входе на основе OIDC
В [серии "быстрый](view-applications-portal.md) запуск" по управлению приложениями вы узнали, как использовать Azure AD в качестве поставщика удостоверений (IDP) для приложения. В этой статье приводятся более подробные сведения о приложениях, использующих стандарт OpenID Connect Connect для реализации единого входа. 

## <a name="before-you-begin"></a>Перед началом
Процесс добавления приложения в клиент Azure Active Directory зависит от типа единого входа, реализуемого приложением. Дополнительные сведения о параметрах единого входа, доступных для приложений, которые могут использовать Azure AD для управления удостоверениями, см. в разделе [Параметры единого входа](sso-options.md). В этой статье рассматриваются приложения на основе OIDC.


## <a name="basic-oidc-configuration"></a>Базовая конфигурация OIDC
В [серии "быстрый](add-application-portal-setup-oidc-sso.md)запуск" приведена статья о настройке единого входа. В нем вы узнаете, как добавить приложение на основе OIDC в клиент Azure.

Замечательно, что при добавлении приложения, использующего стандарт OIDC для единого входа, конфигурация является минимальной. Ниже приведено краткое видео, в котором показано, как добавить приложение на основе OIDC в клиент.

Добавление приложения на основе OIDC в Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/4kv-upsZCI0]

## <a name="next-steps"></a>Дальнейшие действия

- [Серия кратких руководств по управлению приложениями](add-application-portal-setup-oidc-sso.md)
- [Варианты реализации единого входа](sso-options.md)
- [Руководство. Sign in any Azure Active Directory user using the multi-tenant application pattern](../develop/howto-convert-app-to-be-multi-tenant.md) (Вход любого пользователя Azure Active Directory с помощью шаблона мультитенантного приложения)