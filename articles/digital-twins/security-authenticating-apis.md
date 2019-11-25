---
title: Understand API authentication - Azure Digital Twins | Microsoft Docs
description: Learn how to connect to and authenticate with APIs using Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 6c2b0ec5165652e77c92426bb62a30468eef04c2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456899"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Connect to and authenticate with APIs

Azure Digital Twins использует Azure Active Directory (Azure AD) для аутентификации пользователей и защиты приложений. Служба Azure AD поддерживает проверку подлинности для различных современных архитектур. Все они основаны на таких стандартных отраслевых протоколах, как OAuth 2.0 или OpenID Connect. Кроме того, разработчики могут использовать Azure AD для создания приложения с одним клиентом, а также бизнес-приложений. Developers also can use Azure AD to develop [multitenant applications](how-to-multitenant-applications.md).

Общие сведения об Azure AD, пошаговые руководства и понятия см. в статье [Базовая документация по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/).

> [!TIP]
> Follow the [Tutorial](tutorial-facilities-setup.md) to set up and run an Azure Digital Twins sample app.

Чтобы интегрировать приложение или службу с Azure AD, разработчику сначала необходимо зарегистрировать приложение в Azure AD. Дополнительные сведения см. в статье [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](../active-directory/develop/quickstart-register-app.md).

[Пять основных сценариев приложений](../active-directory/develop/v2-app-types.md), которые поддерживает система Azure AD, приведены далее.

* Одностраничное приложение (SPA). Пользователю нужно войти в одностраничное приложение, защиту которого обеспечивает Azure AD.
* Веб-браузер — веб-приложение. Пользователю нужно войти в веб-приложение, защиту которого обеспечивает Azure AD.
* Собственное приложение — веб-API. Собственному приложению, которое выполняется на телефоне, планшете или компьютере, необходимо выполнить аутентификацию пользователя для получения ресурсов из веб-API, защиту которого обеспечивает Azure AD.
* Веб-приложение — веб-API. Веб-приложению требуется получать ресурсы из веб-API, защиту которого обеспечивает Azure AD.
* Управляющая программа или серверное приложение — веб-API. Управляющей программе или серверному приложению, в котором нет веб-интерфейса пользователя, требуется получить ресурсы из веб-API, защиту которого обеспечивает Azure AD.

> [!IMPORTANT]
> Azure Digital Twins supports both of the following authentication libraries:
> * The more recent [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * The [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Вызов Digital Twins из веб-API среднего уровня

Когда разработчики создают решения Digital Twins, обычно они также создают приложение среднего уровня или API. Затем приложение или API вызывает нижний уровень API Digital Twins. Чтобы обеспечить поддержку этой стандартной веб-архитектуры решений, сначала убедитесь в следующем:

1. Пользователи проходят аутентификацию для приложения среднего уровня.

1. Во время аутентификации запрашивается маркер On-Behalf-Of в OAuth 2.0.

1. Полученный маркер затем применяется для аутентификации или вызова интерфейсов API нижнего уровня с использованием потока On-Behalf-Of.

Подробные инструкции по организации потока On-Behalf-Of см. в статье [Azure Active Directory версии 2.0 и поток On-Behalf-Of в OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Кроме того, вы можете просмотреть примеры кода в статье о [вызове веб-API нижнего уровня](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы настроить и проверить Azure Digital Twins с помощью потока неявного предоставления разрешения в OAuth 2.0, ознакомьтесь со статьей о [настройке Postman](./how-to-configure-postman.md).

Дополнительные сведения о безопасности Azure Digital Twins см. в статье [Создание назначений ролей и управление ими](./security-create-manage-role-assignments.md).