---
title: Сценарии приложений и потоки для проверки подлинности на платформе удостоверений Майкрософт | Azure
description: Сведения о сценариях приложений для платформы Microsoft Identity, включая проверку подлинности удостоверений, получение маркеров и вызов защищенных интерфейсов API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: bf47a352542be0ad4467ed420cc38f62da0d47dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534898"
---
# <a name="authentication-flows-and-application-scenarios"></a>Потоки проверки подлинности и сценарии приложений

Конечная точка платформы удостоверений Майкрософт (версии 2.0) поддерживает проверку подлинности для разных типов архитектур современных приложений. Все эти архитектуры основаны на стандартных отраслевых протоколах [OAuth 2.0 и OpenID Connect](active-directory-v2-protocols.md).  Используя [библиотеки проверки подлинности платформы идентификации Майкрософт](reference-v2-libraries.md), приложения проверяют подлинность удостоверений и получают маркеры для доступа к защищенным API.

В этой статье описываются потоки проверки подлинности и сценарии использования приложений в.

- [сценарии приложений и поддерживаемые потоки проверки подлинности](#scenarios-and-supported-authentication-flows);
- [сценарии приложений и поддерживаемые платформы и языки](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Категории приложений

Токены могут быть получены из нескольких типов приложений, включая:

- Веб-приложения
- Мобильные приложения
- Классические приложения
- Веб-API

Токены также могут быть получены из приложений, выполняющихся на устройствах, которые не имеют браузера или работают в IoT.

Приложения можно классифицировать по следующим категориям:

- [Защищенные ресурсы и клиентские приложения](#protected-resources-vs-client-applications). Некоторые сценарии предназначены для защиты таких ресурсов, как веб-приложения или веб-API. Другие сценарии нужны для получения маркера безопасности для вызова защищенного веб-API.
- [С пользователями или без пользователей](#with-users-or-without-users). в некоторых сценариях предполагается, что пользователь вошел в учетную запись, но другие, как и сценарии управляющей программы, не используют пользователя.
- [Одностраничные, общедоступные клиенты и конфиденциальные клиентские приложения](#single-page-public-client-and-confidential-client-applications). Эти типы являются тремя крупными категориями приложений. Каждая из них используется с разными библиотеками и объектами.
- [Аудитория входа](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). доступные потоки проверки подлинности различаются в зависимости от аудитории для входа. Некоторые потоки доступны только для рабочих или учебных учетных записей. Для некоторых других допустимы рабочие или учебные учетные записи и личные учетные записи Майкрософт. Допустимая аудитория зависит от потоков проверки подлинности.
- [Поддерживаемые потоки OAuth 2,0](#scenarios-and-supported-authentication-flows). потоки проверки подлинности используются для реализации сценариев приложений, запрашивающих маркеры. Между сценариями приложений и потоками проверки подлинности не существует строгого соответствия.
- [Поддерживаемые платформы](#scenarios-and-supported-platforms-and-languages): не все сценарии приложений доступны для каждой платформы.

### <a name="protected-resources-vs-client-applications"></a>Защищенные ресурсы или клиентские приложения

Сценарии проверки подлинности предполагают два действия:

- **Получение маркеров безопасности для защищенного веб-API**. Мы рекомендуем использовать [клиентские библиотеки, поддерживаемые корпорацией Майкрософт](reference-v2-libraries.md#microsoft-supported-client-libraries) , для получения маркеров, в частности, в семействе библиотеки проверки подлинности Майкрософт (MSAL).
- **Защита веб-API или веб-приложения**. Одна из проблем защиты веб-API или ресурса веб-приложения — проверка маркера безопасности. Для некоторых платформ корпорация Майкрософт предоставляет [библиотеки ПО промежуточного слоя](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>С пользователями или без пользователей

Большинство сценариев проверки подлинности получают маркеры от имени пользователей, выполнивших вход.

![Сценарии взаимодействия с пользователями](media/scenarios/scenarios-with-users.svg)

Но есть и сценарии управляющей программы, в которых приложения получают маркеры от своего имени, без участия пользователя.

![Сценарии с приложениями управляющей программы](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Одностраничные приложения, общедоступные клиентские приложения или конфиденциальные клиентские приложения

Маркеры безопасности можно получать из приложений многих типов. Их можно разделить на три категории:

- **Одностраничные приложения**: также известные как одностраничные приложения, эти приложения являются веб-приложениями, в которых маркеры получаются из приложения JavaScript или TypeScript, работающего в браузере. Для многих современных приложений интерфейс реализован как одностраничное приложение, чаще всего на языке JavaScript. Для таких приложений часто применяются платформы Angular, React, Vue и др. MSAL.js — это единственная библиотека проверки подлинности Майкрософт, которая поддерживает одностраничные приложения.

- **Общедоступные клиентские приложения**. Эти приложения всегда входят в систему пользователей:
  - классические приложения, вызывающие веб-API от имени выполнившего вход пользователя;
  - Мобильные приложения
  - приложения, которые выполняются на устройствах без браузера, например в Интернете вещей.

  Эти приложения представлены классом [PublicClientApplication](/dotnet/api/microsoft.identity.client.publicclientapplication) библиотеки MSAL. Дополнительные сведения см. в разделе [общедоступные клиентские и конфиденциальные клиентские приложения](msal-client-applications.md).

- **Конфиденциальные клиентские приложения**:
  - веб-приложения, вызывающие веб-API;
  - веб-API, вызывающие другие веб-API;
  - приложения управляющей программы, даже если они реализованы как консольная служба (например, управляющая программа в Linux или служба Windows).

  Эти типы приложений используют класс [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication). Дополнительные сведения см. в разделе [общедоступные клиентские и конфиденциальные клиентские приложения](msal-client-applications.md).

## <a name="application-scenarios"></a>Сценарии приложений

Конечная точка платформы Microsoft Identity поддерживает проверку подлинности для различных архитектур приложений:

- Одностраничные приложения
- Веб-приложения
- Веб-API
- Мобильные приложения
- Собственные приложения
- Управляющие программы
- Приложения серверной части

Приложения используют разные потоки проверки подлинности для входа пользователей и получения маркеров для вызова защищенных API.

### <a name="a-single-page-application"></a>Одностраничное приложение

Многие современные веб-приложения создаются как одностраничные приложения на стороне клиента. Эти приложения используют JavaScript или одностраничную платформу приложения, например, под углом, Vue. js и реагируют. js. Эти приложения выполняются в веб-браузере.

Их характеристики проверки подлинности отличаются от традиционных серверных веб-приложений. Использование платформы удостоверений Майкрософт позволяет одностраничным приложениям обрабатывать вход пользователей и получать маркеры для доступа к серверным службам или веб-API.

![Одностраничное приложение](media/scenarios/spa-app.svg)

См. сведения об [одностраничных приложениях](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Веб-приложение, которое обрабатывает вход пользователя

![Веб-приложение, которое обрабатывает вход пользователя](media/scenarios/scenario-webapp-signs-in-users.svg)

Рекомендации по защите веб-приложения, которое обрабатывает вход пользователя:

- если вы ведете разработку в среде .NET, используйте ASP.NET или ASP.NET Core с ПО промежуточного слоя Open ID Connect ASP.NET. В этом случае защита ресурса включает проверку маркера безопасности, которая выполняется с помощью [расширений IdentityModel для библиотеки .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki), а не библиотек MSAL;

- если разработка ведется на языке Node.js, используйте Passport.js.

См. сведения см. о [веб-приложениях, которые обрабатывают вход пользователей](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Веб-приложения, которые поддерживают вход пользователя и вызывают веб-API от имени этого пользователя

![Веб-приложение, которое вызывает веб-API](media/scenarios/web-app.svg)

Чтобы вызвать веб-API из веб-приложения от имени пользователя, используйте класс **ConfidentialClientApplication** из библиотеки MSAL. Вы будете использовать поток кода авторизации и сохранять полученный маркер в кэше маркеров. MSAL обновляет маркеры по мере необходимости, а контроллер прозрачно получает маркеры из кэша.

См. сведения о [веб-приложениях, которые вызывают веб-API](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Классическое приложение, которое вызывает веб-API от имени выполнившего вход пользователя

Чтобы вызвать веб-API из классического приложения, которое поддерживает вход пользователей, используйте интерактивные методы получения маркера, предоставленные классом **PublicClientApplication** библиотеки MSAL. Эти интерактивные методы позволяют управлять интерфейсом входа пользователя в систему. Чтобы реализовать такое взаимодействие, MSAL использует веб-браузер.

![Классическое приложение, которое вызывает веб-API](media/scenarios/desktop-app.svg)

Для приложений, выполняющихся на компьютерах Windows, присоединенных к домену Windows или к Azure Active Directory, есть еще одна возможность. Эти приложения могут автоматически получать маркер, используя [встроенную проверку подлинности Windows](https://aka.ms/msal-net-iwa).

Приложения на устройстве без браузера также могут вызывать API от имени пользователя. Для проверки подлинности пользователю нужно войти на другое устройство, где есть веб-браузер. В этом сценарии требуется использовать [поток кода устройства](https://aka.ms/msal-net-device-code-flow).

![Поток кода устройства](media/scenarios/device-code-flow-app.svg)

Мы не рекомендуем использовать этот вариант, но для общедоступных клиентских приложений доступен [поток имени пользователя и пароля](https://aka.ms/msal-net-up). Этот поток пока еще необходим для некоторых сценариев, например при использовании DevOps.

Но использование этого потока ограничивает ваши приложения. Например, приложения не могут войти в систему пользователя, который должен использовать многофакторную проверку подлинности или условный доступ. Также приложение не сможет использовать механизм единого входа.

Аутентификация в потоке имени пользователя и пароля противоречит принципам современной проверки подлинности и поддерживается только для обеспечения совместимости со старыми технологиями.

В классических приложениях, если необходимо сохранить кэш маркеров, настройте [сериализацию кэша маркеров](https://aka.ms/msal-net-token-cache-serialization). Реализуя [сериализацию двойного кэша](https://aka.ms/msal-net-dual-cache-serialization), можно использовать обратные и совместимые с ними кэши маркеров. Эти маркеры поддерживают предыдущие поколения библиотек проверки подлинности. Сюда относятся, среди прочего, библиотеки аутентификации Azure AD для .NET (ADAL.NET) версий 3 и 4.

См. сведения о [классическом приложении, которое вызывает веб-API](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Мобильное приложение, которое вызывает веб-API от имени текущего пользователя

Как и в классических приложениях, для получения маркера для вызова веб-API мобильное приложение использует интерактивные методы получения маркера из класса **PublicClientApplication** библиотеки MSAL.

![Мобильное приложение, которое вызывает веб-API](media/scenarios/mobile-app.svg)

MSAL для iOS и MSAL для Android по умолчанию используют системный веб-браузер. Но вы можете настроить для них использование встроенного веб-представления. Существуют особенности, зависящие от мобильной платформы: универсальная платформа Windows (UWP), iOS или Android.

В некоторых сценариях, например при использовании условного доступа с привязкой к идентификатору устройства или регистрации устройства, требуется установить на устройство брокер. К таким брокерам относятся корпоративный портал Майкрософт (на Android) и Microsoft Authenticator (Android и iOS). MSAL теперь может взаимодействовать с брокерами. Дополнительные сведения см. в статье Использование [брокеров в Android и iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

См. сведения о [мобильных приложениях, которые вызывают веб-API](scenario-mobile-overview.md).

> [!NOTE]
> Если мобильное приложение использует MSAL.iOS, MSAL.Android или MSAL.NET на Xamarin, к нему могут применяться политики защиты приложений. Например, политика может запретить пользователю копировать защищенный текст. Мобильным приложением управляет служба Intune, которая распознает его как управляемое приложение. Дополнительные сведения см. в разделе [Обзор пакета SDK для Microsoft Intune приложений](https://docs.microsoft.com/intune/app-sdk).
>
> [Пакет SDK для приложений в Intune](https://docs.microsoft.com/intune/app-sdk-get-started) отделен от библиотек MSAL и самостоятельно взаимодействует с AAD.

### <a name="a-protected-web-api"></a>Защищенный веб-API

Конечную точку платформы Microsoft Identity можно использовать для защиты веб-служб, таких как веб-API RESTFUL вашего приложения. Защищенный веб-API вызывается с помощью маркера доступа. Маркер обеспечивает защиту данных API и проверку подлинности входящих запросов. Объект, вызывающий такой веб-API, добавляет маркер доступа в заголовок авторизации в HTTP-запросе.

Если вы хотите защитить веб-API ASP.NET или ASP.NET Core, необходимо проверить маркер доступа. Для такой проверки применяется ПО промежуточного слоя JWT для ASP.NET. Проверка выполняется с помощью [расширений IdentityModel для библиотеки .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki), а не через MSAL.NET.

См. сведения о [защищенных веб-API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Веб-API, который вызывает другой веб-API от имени пользователя

Чтобы ASP.NET или ASP.NET Core защищенный веб-API для вызова другого веб-API от имени пользователя, приложение должно получить маркер для подчиненного веб-API. Чтобы получить маркер, приложение вызывает метод [аккуиретокенонбехалфоф](https://aka.ms/msal-net-on-behalf-of) класса **конфидентиалклиентаппликатион** . Такие вызовы также называются вызовами *между службами* . Веб-API, вызывающие другие веб-API, должны реализовать настраиваемую сериализацию кэша.

  ![Веб-API, который вызывает другой веб-API](media/scenarios/web-api.svg)

См. сведения о [веб-API, которые вызывают веб-API](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Приложение управляющей программы, которое вызывает веб-API от имени управляющей программы

Приложениям, которые выполняют долговременные процессы или работают без взаимодействия с пользователем, также нужен метод доступа к защищенным веб-API. Такое приложение может проверять подлинность и получать маркеры с помощью удостоверения приложения. Приложение подтверждает свою идентификацию с помощью секрета или сертификата клиента.

Вы можете создать приложение (управляющую программу), которое получает маркер для вызывающего приложения с помощью методов получения [учетных данных клиента](https://aka.ms/msal-net-client-credentials) из класса **ConfidentialClientApplication** библиотеки MSAL. Для этих методов нужно, чтобы вызывающее приложение зарегистрировало секрет в Azure AD. Затем приложение передает этот секрет управляющей программе, которую оно вызывает. В качестве секретов могут использоваться пароли приложений, утверждение сертификата, утверждение клиента и т. д.

![Приложение управляющей программы, к которому обращаются другие приложения и API](media/scenarios/daemon-app.svg)

См. сведения об [управляющих приложениях, которые вызывают веб-API](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Сценарии и поддерживаемые потоки проверки подлинности

Сценарии, использующие получение маркеров, также сопоставляются с потоками проверки подлинности OAuth 2,0. Дополнительные сведения см. в разделе [протоколы OAuth 2,0 и OpenID Connect Connect на платформе Microsoft Identity](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Сценарий</th> <th>Подробный разбор сценария</th> <th>Поток и предоставление разрешения OAuth 2.0</th> <th>Аудитория</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Одностраничное приложение</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Неявно</a></td>
   <td>Рабочие или учебные учетные записи, личные учетные записи и Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Веб-приложение, которое поддерживает вход пользователей</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Код авторизации</a></td>
   <td>Рабочие или учебные учетные записи, личные учетные записи и Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Веб-приложение, которое вызывает веб-API</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Код авторизации</a></td>
   <td>Рабочие или учебные учетные записи, личные учетные записи и Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Классическое приложение, которое вызывает веб-API</a></td>
   <td>Интерактивный вход с использованием <a href="v2-oauth2-auth-code-flow.md">кода авторизации</a> и PKCE</td>
   <td>Рабочие или учебные учетные записи, личные учетные записи и Azure AD B2C</td>
 </tr>

  <tr>
   <td>Встроенная проверка подлинности Windows</td>
   <td>Рабочие или учебные учетные записи</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Пароль владельца ресурса</a></td>
   <td>Рабочие или учебные учетные записи и Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Код устройства</a></td>
   <td>Рабочие или учебные учетные записи</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Мобильное приложение, которое вызывает веб-API</a></td>
   <td>Интерактивный вход с использованием <a href="v2-oauth2-auth-code-flow.md">кода авторизации</a> и PKCE</td>
   <td>Рабочие или учебные учетные записи, личные учетные записи и Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Пароль владельца ресурса</a></td>
   <td>Рабочие или учебные учетные записи и Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Управляющая программа, которая вызывает веб-API</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Учетные данные клиента</a></td>
   <td>Разрешения только для приложений, которые используются без участия пользователей и только в организациях Azure AD</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Веб-API, которое вызывает веб-API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">От имени</a></td>
   <td>Рабочие или учебные учетные записи и личные учетные записи</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Сценарии, поддерживаемые платформы и языки

Библиотеки проверки подлинности Майкрософт поддерживают несколько платформ:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- iOS (собственная)
- MacOS
- Android (собственная)
- Java
- Python

Для создания приложений также можно использовать различные языки.

> [!NOTE]
> Некоторые типы приложений недоступны на каждой платформе.

В приведенной ниже таблице в столбце Windows каждое упоминание .NET Core подразумевает и платформу .NET Framework. Мы опустили эти упоминания, чтобы не загромождать таблицу.

|Сценарий  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Одностраничное приложение](scenario-spa-overview.md) <br/>[![Одностраничное приложение](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Веб-приложение, которое реализует вход пользователей в систему](scenario-web-app-sign-user-overview.md) <br/>[![Веб-приложение, которое входит в систему пользователей](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Веб-приложение, вызывающее веб-API](scenario-web-app-call-api-overview.md) <br/> <br/>[![Веб-приложение, вызывающее веб-API](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [Классическое приложение, которое вызывает веб-API](scenario-desktop-overview.md) <br/> <br/>Классическое ![ [приложение, вызывающее поток кода устройства веб ![-API](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md)](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS, Objective-C или swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Мобильное приложение, которое вызывает веб-API](scenario-mobile-overview.md) <br/> [![Мобильное приложение, которое вызывает веб-API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS, Objective-C или swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [Управляющая программа](scenario-daemon-overview.md) <br/> [![Управляющая программа](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Веб-API, которые вызывают веб-API](scenario-web-api-call-api-overview.md) <br/><br/> [![Веб-API, которые вызывают веб-API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Дополнительные сведения см. [в разделе библиотеки, поддерживаемые корпорацией Майкрософт, по ОС и языку](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Дальнейшие шаги

* Узнайте больше об [основах проверки подлинности](authentication-scenarios.md) и [маркерах доступа платформы удостоверений Майкрософт](access-tokens.md).
* Дополнительные сведения о [защите доступа к приложениям IOT](/azure/architecture/example-scenario/iot-aad/iot-aad).
