---
title: Поведение запросов в интерактивных запросах (Библиотека проверки подлинности Майкрософт для JavaScript)
titleSuffix: Microsoft identity platform
description: Узнайте, как настроить поведение запросов в интерактивных вызовах с помощью библиотеки проверки подлинности Майкрософт для JavaScript (MSAL. js).
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
ms.openlocfilehash: 42d6c4415a3eeb28c999d95b838c6dd7c0f6e606
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803025"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Поведение запросов в интерактивных запросах MSAL. js

Когда пользователь устанавливает активный сеанс Azure AD с несколькими учетными записями пользователей, страница входа Azure AD по умолчанию будет предлагать пользователю выбрать учетную запись, прежде чем продолжить вход. Пользователи не увидят возможности выбора учетной записи, если в Azure AD есть только один сеанс, прошедший проверку подлинности.

Библиотека MSAL. js (начиная с версии v 0.2.4) не отправляет параметр prompt во время интерактивных запросов (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` и `acquireTokenPopup`) и, таким образом, не обеспечивает никаких действий с запросом. Для запросов токенов без уведомления с помощью метода `acquireTokenSilent` MSAL. js передает параметр prompt, установленный в `none`.

В зависимости от сценария приложения можно управлять поведением запросов для интерактивного запроса, задавая параметр prompt в параметрах запроса, передаваемых методам. Например, если вы хотите вызвать интерфейс выбора учетной записи:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


При проверке подлинности в Azure AD можно передать следующие значения запроса:

**имя входа:** Это значение заставляет пользователя ввести учетные данные для запроса проверки подлинности.

**select_account:** Это значение предоставит пользователю возможность выбора учетной записи со списком всех учетных записей в сеансе.

**согласие:** Это значение вызывает диалоговое окно согласия OAuth, которое позволяет пользователям предоставлять разрешения для приложения.

**Нет:** Это значение обеспечит, чтобы пользователь не выводит никаких интерактивных запросов. Не рекомендуется передавать это значение в интерактивные методы в MSAL. js, так как это может привести к непредвиденному поведению. Вместо этого используйте метод `acquireTokenSilent` для достижения безвозвратных вызовов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о параметре `prompt` в протоколе [неявного предоставления OAuth 2,0](v2-oauth2-implicit-grant-flow.md) , используемом библиотекой MSAL. js.
