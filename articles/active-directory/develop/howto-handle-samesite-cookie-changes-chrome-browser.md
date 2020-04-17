---
title: Как обрабатывать изменения cookie SameSite в браузере Chrome Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как обрабатывать изменения файлов cookie SameSite в браузере Chrome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: f28d3722d56582bd925d31b43b4a0219bca2ae30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534607"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Обработка изменений свойства SameSite в файлах cookie в браузере Chrome

## <a name="what-is-samesite"></a>Что такое SameSite?

`SameSite`— это свойство, которое можно установить в файлах HTTP для предотвращения атак Cross Site Request Forgery (CSRF) в веб-приложениях:

- Когда `SameSite` установлен **лакса,** cookie отправляется в запросах в том же сайте и в GET запросы с других сайтов. Он не отправляется в запросах GET, которые являются кросс-доменом.
- Значение **Strict** гарантирует, что файлы cookie отправляются в запросах только на том же сайте.

По умолчанию `SameSite` значение НЕ устанавливается в браузерах, и поэтому нет никаких ограничений на файлы cookie, отправляемые в запросах. Заявка должна будет отказаться от защиты CSRF, установив **Lax** или **Strict** в соответствии с их требованиями.

## <a name="samesite-changes-and-impact-on-authentication"></a>Изменения SameSite и влияние на аутентификацию

Последние [обновления стандартов на SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) предлагают защитить приложения, сделав поведение по умолчанию, `SameSite` когда не значение установлено лаке. Это смягчение означает, что файлы cookie будут ограничены в запросах HTTP, за исключением GET, сделанных с других сайтов. Кроме того, для снятия ограничений на отправку файлов файлов вводится значение **None** None. Эти обновления скоро будут выпущены в предстоящей версии браузера Chrome.

Когда веб-приложения аутентифицируются с помощью платформы Microsoft Identity, используя режим ответа "form_post", сервер входа реагирует на приложение с помощью HTTP POST для отправки токенов или auth code. Поскольку этот запрос является запросом `login.microsoftonline.com` на перекрестный `https://contoso.com/auth`домен (например, из вашего домена), файлы cookie, установленные вашим приложением, теперь подпадают под новые правила в Chrome. Файлы cookie, которые должны использоваться в сценариях кросс-сайтов, — это файлы cookie, в которых хранятся значения *состояния* и *nonce,* которые также отправляются в запросе входа. Есть и другие файлы cookie, выброшенные Azure AD для проведения сеанса.

Если вы не обновите веб-приложения, это новое поведение приведет к сбоям в аутентификации.

## <a name="mitigation-and-samples"></a>Смягчение последствий и образцы

Чтобы преодолеть сбои аутентификации, веб-приложения, `SameSite` аутентифицированные с платформой идентификации Майкрософт, могут настроить свойство `None` для файлов cookie, которые используются в сценариях кросс-домена при работе в браузере Chrome.
Другие браузеры [(см. здесь](https://www.chromium.org/updates/same-site/incompatible-clients) полный `SameSite` список) следуют предыдущему `SameSite=None` поведению и не будут включать файлы cookie, если они установлены.
Вот почему, для поддержки аутентификации на нескольких `SameSite` браузерах `None` веб-приложений придется установить значение только на Chrome и оставить значение пустым на других браузерах.

Этот подход демонстрируется в наших образцах кода ниже.

# <a name="net"></a>[.NET](#tab/dotnet)

В таблице ниже представлены запросы на вытягивание, которые работали вокруг изменений SameSite в наших образцах ASP.NET и ASP.NET Core.

| Пример | Запрос на вытягивание |
| ------ | ------------ |
|  [ASP.NET Core веб-приложение инкрементный учебник](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Тот же сайт печенье исправить #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET образец веб-приложения MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Тот же сайт печенье исправить #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [активный-каталог-dotnet-админ-ограниченные-области-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Тот же сайт печенье исправить #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

для получения подробной информации о том, как обрабатывать файлы cookie SameSite в ASP.NET и ASP.NET Core, см.

- [Работайте с cookie-файлами SameSite в ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [ASP.NET блог на SameSite вопрос](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Пример |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Пример | Запрос на вытягивание |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Тот же сайт печенье исправить #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Тот же сайт печенье исправить #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Следующие шаги

Подробнее о SameSite и сценарии веб-приложений:

> [!div class="nextstepaction"]
> [Часто задаваемые вопросы Google Chrome на SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Страница Хром SameSite](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Сценарий: Веб-приложение, которое подписывает в пользователях](scenario-web-app-sign-user-overview.md)