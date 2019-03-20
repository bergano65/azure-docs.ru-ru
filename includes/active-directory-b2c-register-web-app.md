---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 4d5c8373f2a8f02ccfbe51cb970ab08744950e7e
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "58115095"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Чтобы зарегистрировать веб-приложение, используйте параметры, указанные в таблице.

![Примеры параметров регистрации для нового веб-приложения](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| Параметр      | Образец значения  | ОПИСАНИЕ                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Имя** | Приложение Contoso B2C | Введите **имя** приложения, которое будет служить его описанием для пользователей. | 
| **Включить веб-приложение или веб-интерфейс API** | Yes | Выберите **Да** для веб-приложения. |
| **Разрешить неявный поток** | Yes | Выберите **Да**, если приложение использует [вход через OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md). |
| **URL-адрес ответа** | `https://localhost:44316` | URL-адреса ответа — это конечные точки, куда Azure AD B2C возвращает все токены, запрашиваемые вашим приложением. Введите [соответствующий](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **URL-адрес ответа**. В этом примере используется локальное приложение, которое ожидает передачи данных через порт 44316. |

Чтобы зарегистрировать приложение, щелкните **Создать** .

Только что зарегистрированное приложение отобразится в списке приложений для клиента B2C. Выберите веб-приложение в списке. Откроется панель свойств веб-приложения.

![Свойства веб-приложения](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

Запишите глобальный уникальный **идентификатор для клиента приложения**. Этот идентификатор понадобится для кода приложения.