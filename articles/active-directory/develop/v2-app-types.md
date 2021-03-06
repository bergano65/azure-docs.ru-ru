---
title: Типы приложений для платформы Microsoft Identity | Службы
description: Типы приложений и сценариев, поддерживаемых конечной точкой платформы Microsoft Identity Platform (v 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21f1da8738946078cb625c76e11ce1bfd62d97ac
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927159"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Типы приложений для платформы Microsoft Identity

Конечная точка платформы удостоверений Майкрософт (версии 2.0) поддерживает проверку подлинности для различных современных архитектур приложений, которые основаны на стандартном отраслевом протоколе [OAuth 2.0 или OpenID Connect](active-directory-v2-protocols.md). В этой статье описываются типы приложений, которые можно создавать с помощью платформы Microsoft Identity, независимо от предпочитаемого языка или платформы. Эта информация призвана помочь вам понять высокоуровневые сценарии до [начала работы с кодом](v2-overview.md#getting-started).

> [!NOTE]
> Конечная точка платформы Microsoft Identity не поддерживает все сценарии и функции Azure Active Directory (Azure AD). Чтобы определить, следует ли использовать конечную точку платформы идентификации Майкрософт, ознакомьтесь с [ограничениями платформы удостоверений Майкрософт](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Основные сведения

Необходимо зарегистрировать каждое приложение, использующее конечную точку платформы Microsoft Identity на новом [портале регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908). В процессе регистрации приложения будет задано несколько параметров приложения:

* **Идентификатор приложения (клиента)** , уникально идентифицирующий Ваше приложение.
* **универсальный код ресурса (URI) перенаправления**, который можно использовать для направления ответов к приложению;
* Несколько других значений, относящихся к сценарию, например поддерживаемые типы учетных записей

Дополнительные сведения см. в статье о [регистрации приложения](quickstart-register-app.md).

После регистрации приложения приложение взаимодействует с платформой Microsoft Identity, отправляя запросы к конечной точке. Мы предоставляем платформы и библиотеки с открытым кодом, которые отвечают обрабатывают данные этих запросов. Также имеется возможность самостоятельно реализовать логику аутентификации, создавая запросы к этим конечным точкам.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Одностраничные приложения (JavaScript)

Многие современные приложения содержат интерфейсное одностраничное приложение, созданное преимущественно на языке JavaScript. Часто он написан с помощью такой платформы, как угловой, реагирующий или Vue. Конечная точка платформы Microsoft Identity поддерживает эти приложения с помощью [неявного потока OAuth 2,0](v2-oauth2-implicit-grant-flow.md).

В этом потоке приложение получает маркеры непосредственно от платформы удостоверений Майкрософт авторизовать конечную точку без обмена данными между серверами. Вся логика аутентификации и обработки сеансов размещается в клиенте JavaScript без перенаправления на дополнительные страницы.

![Показывает неявный поток проверки подлинности](./media/v2-app-types/convergence-scenarios-implicit.svg)

Чтобы увидеть этот сценарий в действии, попробуйте один из примеров кода одностраничного приложения в разделе [Приступая к работе с платформой Microsoft Identity](v2-overview.md#getting-started) .

## <a name="web-apps"></a>веб-приложений:

Для веб-приложений (.NET, PHP, Java, Ruby, Python, Node), доступных пользователю через браузер, для входа пользователей можно использовать [OpenID Connect](active-directory-v2-protocols.md). В OpenID Connect веб-приложение получает маркер идентификации. Это маркер безопасности, который проверяет удостоверение пользователя и предоставляет сведения о пользователе в форме утверждений.

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Дополнительные сведения о различных типах токенов, используемых в конечной точке платформы Microsoft Identity, доступны в справочнике по [маркерам доступа](access-tokens.md) и [id_token](id-tokens.md)

В приложениях веб-сервера поток аутентификации для входа состоит из следующих базовых этапов.

![Показывает поток проверки подлинности веб-приложения](./media/v2-app-types/convergence-scenarios-webapp.svg)

Удостоверение пользователя можно проверить, проверив маркер идентификации с помощью открытого ключа подписывания, полученного от конечной точки платформы Microsoft Identity. Для этого задается файл cookie сеанса, с помощью которого можно идентифицировать пользователя при последующих запросах страницы.

Чтобы увидеть этот сценарий в действии, попробуйте один из примеров кода для входа в веб-приложение в разделе [Приступая к работе с платформой Microsoft Identity](v2-overview.md#getting-started) .

Помимо простого входа в систему, приложению веб-сервера может потребоваться доступ к некоторым другим веб-службам, например интерфейсу REST API. В этом случае приложение веб-сервера участвует в объединенном потоке OpenID Connect и OAuth 2.0 с использованием [потока кода авторизации OAuth 2.0](active-directory-v2-protocols.md). Чтобы больше узнать об этом сценарии, ознакомьтесь с тем, как [приступить к работе с веб-приложениями и интерфейсами веб-API](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Веб-API

Конечную точку платформы удостоверений Майкрософт можно использовать для защиты веб-служб, таких как веб-API REST. Веб-API можно реализовать на множестве платформ и языков. Их также можно реализовать с помощью триггеров HTTP в функциях Azure. Веб-API вместо маркеров идентификации и файлов cookie сеанса использует маркер доступа OAuth 2.0 для защиты данных и аутентификации входящих запросов. Объект, вызывающий веб-API, добавляет маркер доступа в начале заголовка авторизации HTTP-запроса, как показано ниже.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Веб-API использует маркер доступа для проверки удостоверения объекта, вызывающего API, и получения сведений о нем из утверждений, которые закодированы в маркере доступа. Дополнительные сведения о различных типах токенов, используемых в конечной точке платформы Microsoft Identity, доступны в справочнике по [маркерам доступа](access-tokens.md) и [id_token](id-tokens.md)

Веб-API может дать пользователям возможность применять определенные функции и данные или отказаться от них, предоставляя разрешения, которые также называют [областями](v2-permissions-and-consent.md). Чтобы вызывающее приложение получило разрешение для области, пользователь должен дать согласие на это. Конечная точка платформы идентификации Майкрософт запрашивает у пользователя разрешение, а затем записывает разрешения во все маркеры доступа, получаемые веб-API. Веб-API проверяет маркеры доступа, получаемые при каждом вызове, и выполняет проверки авторизации.

Веб-API может получать маркеры доступа от всех типов приложений, включая приложения веб-сервера, классические и мобильные приложения, одностраничные приложения, серверные управляющие программы и даже другие веб-API. Общий поток для веб-API выглядит следующим образом.

![Показывает поток проверки подлинности веб-API](./media/v2-app-types/convergence-scenarios-webapi.svg)

Чтобы узнать, как защитить веб-API с помощью маркеров доступа OAuth2, ознакомьтесь с примерами кода веб-API в разделе [Приступая к работе с платформой Microsoft Identity](v2-overview.md#getting-started) .

Во многих случаях веб-API также должны выполнять исходящие запросы к другим нижестоящим веб-API, защищенным платформой Microsoft Identity. Для этого веб-API может воспользоваться потоком «от **имени** », что позволяет веб-API обмениваться входящим маркером доступа для использования в исходящих запросах. Дополнительные сведения см. [в статье о платформе Microsoft Identity и OAuth 2,0 от имени потока](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Мобильные и собственные приложения

Приложениям, установленным на устройстве, например мобильным и классическим приложениям, часто требуется доступ к внутренним службам или интерфейсам веб-API, которые хранят данные и выполняют различные функции от имени пользователя. В этих приложениях можно реализовать процесс входа и авторизации во внутренних службах с помощью [потока кода авторизации OAuth 2.0](v2-oauth2-auth-code-flow.md).

В этом потоке приложение получает код авторизации от конечной точки платформы идентификации Майкрософт при входе пользователя в систему. Код авторизации представляет собой разрешение, полученное от приложения, на вызов внутренних служб от имени пользователя, выполнившего вход. Приложение может передать код авторизации в фоновом режиме, чтобы получить маркер доступа OAuth 2.0 и маркер обновления. Приложение может использовать маркер доступа для аутентификации в интерфейсах веб-API в HTTP-запросах и использовать маркер обновления, чтобы получать новые маркеры доступа после истечения срока действия старых маркеров.

![Показывает поток проверки подлинности собственного приложения](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Управляющие программы и серверные приложения

Приложениям, использующим долговременные процессы или работающим без взаимодействия с пользователем, тоже нужна возможность доступа к защищенным ресурсам, таким как интерфейсы веб-API. Такие приложения могут выполнять аутентификацию и получать маркеры, используя удостоверение приложения (а не делегированное удостоверение пользователя) с помощью потока учетных данных клиента OAuth 2.0. Можно подтвердить удостоверение приложения с помощью секрета или сертификата клиента. Дополнительные сведения см. [в статье Проверка подлинности на платформе Microsoft Identity в приложениях управляющей программы с сертификатами](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/).

В этом потоке приложение взаимодействует непосредственно с конечной точкой `/token` для получения доступа:

![Показывает поток проверки подлинности приложения управляющей программы](./media/v2-app-types/convergence-scenarios-daemon.svg)

Чтобы создать управляющую программу, изучите [документацию по учетным данным клиентов](v2-oauth2-client-creds-grant-flow.md) или ознакомьтесь с [примером приложения .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
