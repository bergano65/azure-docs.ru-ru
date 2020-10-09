---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87311553"
---
> [!IMPORTANT]
> MSAL.js 2.0 в настоящее время не поддерживает Azure AD B2C для использования с потоком кода авторизации PKCE. В настоящее время Azure AD B2C рекомендует использовать неявный поток, как описано в статье [Руководство по регистрации веб-приложения в Azure Active Directory B2C][implicit-flow]. Сведения об отслеживании хода решения этой проблемы см. на вики-странице [MSAL.js][msal-wiki].

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue
