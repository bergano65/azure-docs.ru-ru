---
title: Общие сведения об аутентификации API с помощью Azure Digital Twins | Документация Майкрософт
description: Сведения о подключении к API и выполнении аутентификации с помощью Azure Digital Twins
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711061"
---
# <a name="connect-and-authenticate-to-apis"></a>Подключение к API и аутентификация

Azure Digital Twins использует Azure Active Directory (Azure AD) для аутентификации пользователей и защиты приложений. Служба Azure AD поддерживает проверку подлинности для различных современных архитектур. Все они основаны на таких стандартных отраслевых протоколах, как OAuth 2.0 или OpenID Connect. Кроме того, разработчики могут использовать Azure AD для создания приложения с одним клиентом, а также бизнес-приложений. Разработчики также могут использовать Azure AD для разработки мультиканальных приложений.

Общие сведения об Azure AD, пошаговые руководства и понятия см. в статье [Базовая документация по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index).

Чтобы интегрировать приложение или службу с Azure AD, разработчику сначала необходимо зарегистрировать приложение в Azure AD. Дополнительные сведения см. в статье [Краткое руководство. Регистрация приложения в конечной точке Azure Active Directory версии 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Пять основных сценариев приложений](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types), которые поддерживает система Azure AD, приведены далее.

* Одностраничное приложение (SPA). Пользователю нужно войти в одностраничное приложение, защиту которого обеспечивает Azure AD.
* Веб-браузер — веб-приложение. Пользователю нужно войти в веб-приложение, защиту которого обеспечивает Azure AD.
* Собственное приложение — веб-API. Собственному приложению, которое выполняется на телефоне, планшете или компьютере, необходимо выполнить аутентификацию пользователя для получения ресурсов из веб-API, защиту которого обеспечивает Azure AD.
* Веб-приложение — веб-API. Веб-приложению требуется получать ресурсы из веб-API, защиту которого обеспечивает Azure AD.
* Управляющая программа или серверное приложение — веб-API. Управляющей программе или серверному приложению, в котором нет веб-интерфейса пользователя, требуется получить ресурсы из веб-API, защиту которого обеспечивает Azure AD.

Библиотека аутентификации Windows Azure предлагает множество способов получения токенов Active Directory. Дополнительные сведения о библиотеках и примерах кода см. [здесь](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Вызов Digital Twins из веб-API среднего уровня

Когда разработчики создают решения Digital Twins, обычно они также создают приложение среднего уровня или API. Затем приложение или API вызывает нижний уровень API Digital Twins. Чтобы обеспечить поддержку этой стандартной веб-архитектуры решений, сначала убедитесь в следующем:

1. Пользователи проходят аутентификацию для приложения среднего уровня.

1. Во время аутентификации запрашивается маркер On-Behalf-Of в OAuth 2.0.

1. Полученный маркер затем применяется для аутентификации или вызова интерфейсов API нижнего уровня с использованием потока On-Behalf-Of.

Подробные инструкции по организации потока On-Behalf-Of см. в статье [Azure Active Directory версии 2.0 и поток On-Behalf-Of в OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Кроме того, вы можете просмотреть примеры кода в статье о [вызове веб-API нижнего уровня](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Дополнительная информация

Чтобы настроить и проверить Azure Digital Twins с помощью потока неявного предоставления разрешения в OAuth 2.0, ознакомьтесь со статьей о [настройке Postman](./how-to-configure-postman.md).

Дополнительные сведения о безопасности Azure Digital Twins см. в статье [Создание назначений ролей и управление ими](./security-create-manage-role-assignments.md).