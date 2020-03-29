---
title: Мигрируйте на основе веб-аПО на основе OWIN в b2clogin.com
titleSuffix: Azure AD B2C
description: Узнайте, как включить API .NET web для поддержки токенов, выпущенных несколькими эмитентами токенов во время переноса приложений на b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184100"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Мигрируйте на основе веб-API на основе OWIN в b2clogin.com

В этой статье описывается метод, позволяющий поддерживать несколько эмитентов токенов в web-аПО, которые реализуют [Открытый веб-интерфейс для .NET (OWIN).](http://owin.org/) Поддержка нескольких конечных токенов полезна при миграции B2C-A2C Active Directory B2C (Azure AD B2C) и их приложений с *login.microsoftonline.com* на *b2clogin.com.*

Добавляя поддержку в API для приема токенов, выпущенных как b2clogin.com, так и login.microsoftonline.com, можно поэтапно перенести веб-приложения, прежде чем удалить из API поддержку login.microsoftonline.com токенов.

В следующих разделах приводится пример того, как включить несколько эмитентов в веб-aPI, использующих компоненты среднего посуды [Microsoft OWIN][katana] (Katana). Хотя примеры кода специфичны для промежуточного программного обеспечения Microsoft OWIN, общий метод должен быть применим к другим библиотекам OWIN.

> [!NOTE]
> Эта статья предназначена для клиентов Azure AD B2C с `login.microsoftonline.com` развернутыми в настоящее `b2clogin.com` время AIS и приложениями, которые ссылаются и которые хотят перенестись в рекомендуемую конечную точку. Если вы настраиваете новое приложение, используйте [b2clogin.com](b2clogin.md) в том виде, b2clogin.com в том виде, в каком вы даете указания.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить действия в этой статье, вам нужны следующие ресурсы Azure AD B2C:

* [Потоки пользователей](tutorial-create-user-flows.md) или [пользовательские политики, созданные](custom-policy-get-started.md) в вашем арендаторе

## <a name="get-token-issuer-endpoints"></a>Получить конечные точки эмитента токенов

Сначала необходимо получить ури-изуи-конечные точки эмитента токенов для каждого эмитента, который вы хотите поддерживать в вашем API. Чтобы получить *b2clogin.com* и *login.microsoftonline.com* конечные точки, поддерживаемые вашим арендатором Azure AD B2C, используйте следующую процедуру на портале Azure.

Начните с выбора одного из существующих потоков пользователей:

1. Перейдите к арендатору Azure AD B2C на [портале Azure](https://portal.azure.com)
1. В **соответствии**с политиками выберите **потоки пользователей (политики)**
1. Выберите существующую политику, например *B2C_1_signupsignin1,* затем выберите **поток пользователей Run**
1. Под **заголовком потока пользователей Run** в верхней части страницы выберите гиперссылку для навигации по конечной точке обнаружения OpenID Connect для этого потока пользователей.

    ![Известная гиперссылка URI на странице портала Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. На странице, которая открывается в `issuer` вашем браузере, запишите значение, например:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Используйте **выпадение домена Select,** чтобы выбрать другой домен, а `issuer` затем выполнить предыдущие два шага еще раз и записать его значение.

Теперь вы должны иметь два URIs записаны, которые похожи на:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Пользовательские политики

Если вместо потоков пользователей у вас есть пользовательские политики, можно использовать аналогичный процесс для получения URIs эмитента.

1. Перейдите к арендатору Azure AD B2C
1. Выберите **рамки опыта идентификации**
1. Выберите одну из политик, например, *B2C_1A_signup_signin*
1. Используйте **выпадение домена Select,** чтобы выбрать домен, например, *yourtenant.b2clogin.com*
1. Выберите гиперссылку, отображаемую в **точке обнаружения OpenID Connect**
1. Запись `issuer` значения
1. Выполните шаги 4-6 для другого домена, *например, login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Получение кода примера

Теперь, когда у вас есть оба конечных момента маркера, необходимо обновить код, чтобы указать, что обе конечные точки являются действительными эмитентами. Чтобы пройти пример, загрузите или клоните образец приложения, а затем обновите образец для поддержки обеих конечных точек в качестве действительных эмитентов.

Скачать архив: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Включить несколько эмитентов в веб-API

В этом разделе вы обновляете код, чтобы указать, что обе конечные точки эмитента маркеров являются действительными.

1. Откройте решение **B2C-WebAPI-DotNet.sln** в Visual Studio
1. В проекте **TaskService** откройте *файл\\TaskService App_Start\\*в редакторе Startup.Auth.cs
1. Добавьте `using` следующую директиву в верхнюю часть файла:

    `using System.Collections.Generic;`
1. Добавьте [`ValidIssuers`][validissuers] свойство [`TokenValidationParameters`][tokenvalidationparameters] в определение и укажите оба URIs, записанные в предыдущем разделе:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`предоставляется MSAL.NET и потребляется посредника OWIN в следующем разделе кода в *Startup.Auth.cs*. С несколькими указанными действенными эмитентами конвейер приложения OWIN узнает, что обе конечные точки токенов являются действительными эмитентами.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Как упоминалось ранее, другие библиотеки OWIN обычно предоставляют аналогичный механизм для поддержки нескольких эмитентов. Хотя приведение примеров для каждой библиотеки выходит за рамки данной статьи, для большинства библиотек можно использовать аналогичный метод.

## <a name="switch-endpoints-in-web-app"></a>Переключение конечных точек в веб-приложении

Теперь, когда оба URIs поддерживаются вашим веб-API, теперь необходимо обновить веб-приложение, чтобы он извлекал токены из b2clogin.com конечную точку.

Например, можно настроить образец веб-приложения для использования новой `ida:AadInstance` конечной точки, изменив значение в файле *TaskWebApp\\(Web.config)* проекта **TaskWebApp.**

Измените `ida:AadInstance` значение в *Web.config* TaskWebApp так, `{your-b2c-tenant-name}.b2clogin.com` чтобы `login.microsoftonline.com`он ссылался вместо .

Перед следующей операцией.

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

После (заменить `{your-b2c-tenant}` имя вашего арендатора B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Когда строки конечных точек строятся во время выполнения веб-приложения, конечные точки на основе b2clogin.com используются при запросе токенов.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье был представлен метод настройки веб-API, реализующего промежуточное программное обеспечение Microsoft OWIN (Katana) для приема токенов из нескольких конечных точек эмитента. Как вы можете заметить, есть несколько других строк в *файлах Web.Config* как TaskService, так и TaskWebApp, которые необходимо изменить, если вы хотите построить и запустить эти проекты против собственного арендатора. Вы можете изменить проекты надлежащим образом, если вы хотите увидеть их в действии, однако, полный проход через это выходит за рамки этой статьи.

Для получения дополнительной информации о различных типах токенов безопасности, [Overview of tokens in Azure Active Directory B2C](tokens-overview.md)испускаемых Azure AD B2C, см.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
