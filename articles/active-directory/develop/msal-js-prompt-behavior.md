---
title: Поведение в интерактивные запросы (библиотека проверки подлинности Майкрософт для JavaScript) подсказки | Azure
description: Дополнительные сведения о настройке поведения запроса в интерактивный вызов событий с помощью библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544270"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Поведение в интерактивных запросов MSAL.js подсказки

Когда пользователь устанавливает active Azure AD сеанса с несколькими учетными записями пользователей, на странице входа Azure AD по умолчанию предложит пользователю выбрать учетную запись перед тем как продолжить вход. Пользователи не увидят выбора наблюдаться, если имеется только один прошедшего проверку подлинности сеанс с Azure AD.

В библиотеке MSAL.js (начиная с v0.2.4) не отправляет параметр командной строки во время интерактивных запросов (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` и `acquireTokenPopup`) и тем самым не применяет любые поведения запроса. Для автоматической запросов маркеров с помощью `acquireTokenSilent` метод, MSAL.js передает параметр командной строки, значение `none`.

Исходя из вашим приложением, можно управлять поведения запроса для интерактивных запросов параметром параметром запроса в параметрах запроса, передаваемые в методы. Например, если требуется инициировать процесс выбора учетной записи:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Следующие prompt значения могут передаваться при проверке подлинности с помощью Azure AD:

**имя для входа:** Это значение приведет к пользователю ввести учетные данные на запрос проверки подлинности.

**select_account:** Это значение будет предоставить пользователю интерфейс выбора учетной записи, перечисляя все учетные записи в сеанс.

**согласие:** Это значение будет вызывать диалоговое окно согласия OAuth, который позволяет пользователю предоставить разрешения для приложения.

**NONE:** Это значение будет убедитесь, что пользователь не видит любой интерактивной командной строки. Мы советуем не передавать это значение для интерактивных методов в MSAL.js, как и непредсказуемого поведения. Вместо этого используйте `acquireTokenSilent` метод обеспечения автоматической вызовов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о `prompt` параметр в [неявное разрешение OAuth 2.0](v2-oauth2-implicit-grant-flow.md) протокол, какую библиотеку MSAL.js.
