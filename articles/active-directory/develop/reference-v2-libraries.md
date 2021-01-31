---
title: Библиотеки проверки подлинности платформы Microsoft Identity Службы
description: Список клиентских библиотек и по промежуточного слоя, совместимых с платформой Microsoft Identity. Используйте эти библиотеки, чтобы добавить поддержку входа пользователя в систему и защищенный доступ к веб-API (авторизацию) в приложения.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218306"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Библиотеки проверки подлинности платформы Microsoft Identity

В следующих таблицах показана поддержка библиотеки проверки подлинности Майкрософт для нескольких типов приложений. Они включают ссылки на исходный код библиотеки, где можно получить пакет для проекта приложения, а также сведения о том, поддерживает ли библиотека вход пользователя в систему (аутентификацию), доступ к защищенным веб-API (авторизацию) или и то, и другое.

Платформа удостоверений Майкрософт сертифицирована OpenID Connect Foundation как [Сертифицированный поставщик OpenID Connect](https://openid.net/certification/). Если вы предпочитаете использовать библиотеку, отличную от библиотеки проверки подлинности Майкрософт (MSAL) или другой библиотеки, поддерживаемой Майкрософт, выберите ее с помощью [сертифицированной реализации OpenID Connect Connect](https://openid.net/developers/certified/).

Если вы решили вручную реализовать собственную реализацию на уровне протокола [OAuth 2,0 или OpenID Connect Connect 1,0](active-directory-v2-protocols.md), обратите внимание на вопросы безопасности в каждой спецификации Standard и следуйте методологии жизненного цикла разработки программного обеспечения (SDL), например [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Одностраничное приложение (SPA)

Одностраничное приложение выполняется полностью на поверхности браузера и извлекает данные страницы (HTML, CSS и JavaScript) динамически или во время загрузки приложения. Он может вызывать веб-API для взаимодействия с внутренними источниками данных.

Поскольку код SPA полностью выполняется в браузере, он считается *общедоступным клиентом* , который не может безопасно хранить секреты.

| Язык и платформа | Проект включен<br/>GitHub                                                                                                    | Пакет                                                                      | Появляться<br/>запущен                             | Выполнение входа пользователей                                         | Доступ к веб-API                                                 | Общедоступная версия (GA) *или*<br/>Общедоступная Предварительная версия<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL, угловая 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | Общедоступная предварительная версия                                               |
| Angular              | [MSALный угловой](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Руководство](tutorial-v2-angular.md)              | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | Общедоступная предварительная версия                                               |
| JavaScript           | [MSAL.js 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Руководство](tutorial-v2-javascript-auth-code.md) | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| React                | [MSAL реагировать](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | Общедоступная предварительная версия                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Дополнительные условия использования для Microsoft Azure предварительных версий][preview-tos] применяются к библиотекам в *общедоступной предварительной версии*.

## <a name="web-application"></a>Веб-приложение

Веб-приложение запускает код на сервере, который создает и отправляет HTML, CSS и JavaScript в веб-браузер пользователя для подготовки к просмотру. Удостоверение пользователя сохраняется в качестве сеанса между браузером пользователя (внешним интерфейсом) и веб-сервером (серверной частью).

Поскольку код веб-приложения выполняется на веб-сервере, он считается *конфиденциальным клиентом* , который может безопасно хранить секреты.

| Язык и платформа | Проект включен<br/>GitHub                                                                                     | Пакет                                                                                                    | Появляться<br/>запущен                               | Выполнение входа пользователей                                            | Доступ к веб-API                                                    | Общедоступная версия (GA) *или*<br/>Общедоступная Предварительная версия<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![Библиотека не может запросить маркеры идентификатора для входа пользователя.][n] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y]    | GA                                                           |
| ASP.NET Core         | [Безопасность ASP.NET](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y]    | ![Библиотека не может запросить маркеры доступа для защищенных веб-API.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y]    | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Краткое руководство](quickstart-v2-java-webapp.md)        | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y]    | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y]    | GA                                                           |
| Node.js              | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                                                | [Краткое руководство](quickstart-v2-nodejs-webapp-msal.md) | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y]    | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y]    | Общедоступная предварительная версия                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport-Azure-AD](https://www.npmjs.com/package/passport-azure-ad)                                       | [Краткое руководство](quickstart-v2-nodejs-webapp.md)      | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y]    | ![Библиотека не может запросить маркеры доступа для защищенных веб-API.][n] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Краткое руководство](quickstart-v2-python-webapp.md)      | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y]    | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Дополнительные условия использования для Microsoft Azure предварительных версий][preview-tos] применяются к библиотекам в *общедоступной предварительной версии*.

## <a name="desktop-application"></a>Классическое приложение

Классическое приложение обычно является двоичным (скомпилированным) кодом, который служит для работы с пользовательским интерфейсом и предназначен для запуска на рабочем столе пользователя.

Так как классическое приложение запускается на рабочем столе пользователя, оно считается *общедоступным клиентом* , который не может безопасно хранить секреты.

| Язык и платформа | Проект включен<br/>GitHub                                                                                     | Пакет                                                                               | Появляться<br/>запущен                        | Выполнение входа пользователей                                         | Доступ к веб-API                                                 | Общедоступная версия (GA) *или*<br/>Общедоступная Предварительная версия<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | Общедоступная предварительная версия                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| macOS (SWIFT/obj-C)  | [MSAL для iOS и MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Руководство](tutorial-v2-ios.md)             | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Руководство](tutorial-v2-windows-uwp.md)     | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Руководство](tutorial-v2-windows-desktop.md) | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Дополнительные условия использования для Microsoft Azure предварительных версий][preview-tos] применяются к библиотекам в *общедоступной предварительной версии*.

## <a name="mobile-application"></a>Мобильное приложение

Мобильное приложение обычно является двоичным (скомпилированным) кодом, который служит для запуска пользовательского интерфейса и предназначен для работы на мобильном устройстве пользователя.

Так как мобильное приложение выполняется на мобильном устройстве пользователя, оно считается *общедоступным клиентом* , который не может безопасно хранить секреты.

| Платформа          | Проект включен<br/>GitHub                                                                          | Пакет                                                                               | Появляться<br/>запущен                    | Выполнение входа пользователей                                         | Доступ к веб-API                                                 | Общедоступная версия (GA) *или*<br/>Общедоступная Предварительная версия<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Краткое руководство](quickstart-v2-android.md) | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| Android (Котлин)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| iOS (SWIFT/obj-C) | [MSAL для iOS и MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Руководство](tutorial-v2-ios.md)         | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Библиотека может запросить маркеры идентификатора для входа пользователя.][y] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Дополнительные условия использования для Microsoft Azure предварительных версий][preview-tos] применяются к библиотекам в *общедоступной предварительной версии*.

## <a name="service--daemon"></a>Служба или управляющая программа

Службы и управляющие программы часто используются для обмена данными между сервером и другими автоматическими (иногда называемыми *автономными*). Так как на клавиатуре нет пользователя для ввода учетных данных или согласия на доступ к ресурсам, эти приложения проходят проверку подлинности как самостоятельно, а не для пользователя при запросе полномочного доступа к ресурсам веб-API.

Служба или управляющая программа, выполняемая на сервере, считается *конфиденциальным клиентом* , который может безопасно хранить свои секреты.

| Язык и платформа | Проект включен<br/>GitHub                                                                 | Пакет                                                                                | Появляться<br/>запущен                           | Выполнение входа пользователей                                            | Доступ к веб-API                                                 | Общедоступная версия (GA) *или*<br/>Общедоступная Предварительная версия<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Краткое руководство](quickstart-v2-netcore-daemon.md) | ![Библиотека не может запросить маркеры идентификатора для входа пользователя.][n] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Библиотека не может запросить маркеры идентификатора для входа пользователя.][n] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Краткое руководство](quickstart-v2-python-daemon.md)  | ![Библиотека не может запросить маркеры идентификатора для входа пользователя.][n] | ![Библиотека может запрашивать маркеры доступа для защищенных веб-API.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Дополнительные условия использования для Microsoft Azure предварительных версий][preview-tos] применяются к библиотекам в *общедоступной предварительной версии*.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о библиотеке проверки подлинности Майкрософт см. в [обзоре библиотеки проверки подлинности Майкрософт (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
