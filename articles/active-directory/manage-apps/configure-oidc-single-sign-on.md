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
ms.openlocfilehash: 487bf3c83be16ee80838b3bbe8bf8532eb6ffe12
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780065"
---
# <a name="understand-oidc-based-single-sign-on"></a>Общие сведения о едином входе на основе OIDC
В [серии "быстрый](view-applications-portal.md) запуск" по управлению приложениями вы узнали, как использовать Azure AD в качестве поставщика удостоверений (IDP) для приложения. В этой статье приводятся более подробные сведения о приложениях, использующих стандарт OpenID Connect Connect для реализации единого входа. 

## <a name="before-you-begin"></a>Подготовка к работе
Процесс добавления приложения в клиент Azure Active Directory зависит от типа единого входа, реализуемого приложением. Дополнительные сведения о параметрах единого входа, доступных для приложений, которые могут использовать Azure AD для управления удостоверениями, см. в разделе [Параметры единого входа](sso-options.md). В этой статье рассматриваются приложения на основе OIDC.


## <a name="basic-oidc-configuration"></a>Базовая конфигурация OIDC
В [серии "быстрый](add-application-portal-setup-oidc-sso.md)запуск" приведена статья о настройке единого входа. В нем вы узнаете, как добавить приложение на основе OIDC в клиент Azure.

Замечательно, что при добавлении приложения, использующего стандарт OIDC для единого входа, конфигурация является минимальной. Ниже приведено краткое видео, в котором показано, как добавить приложение на основе OIDC в клиент.

Добавление приложения на основе OIDC в Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Дополнительные сведения о согласии пользователей и администраторов см. в разделе Общие сведения о [согласии пользователя и администратора](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Дальнейшие действия

- [Серия кратких руководств по управлению приложениями](add-application-portal-setup-oidc-sso.md)
- [Варианты реализации единого входа](sso-options.md)
- [Руководство. Sign in any Azure Active Directory user using the multi-tenant application pattern](../develop/howto-convert-app-to-be-multi-tenant.md) (Вход любого пользователя Azure Active Directory с помощью шаблона мультитенантного приложения)
