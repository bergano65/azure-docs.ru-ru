---
title: Использование библиотеки проверки подлинности Майкрософт (MSAL) в национальных облаках — платформа Microsoft Identity
description: Библиотека проверки подлинности Майкрософт (MSAL) позволяет разработчикам приложений получать маркеры для вызова защищенных веб-API. Эти веб-API можно Microsoft Graph, других API Майкрософт, партнерских веб-API или собственного веб-API. MSAL поддерживает несколько архитектур и платформ приложений.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f1c73d89b0efc17e8f8836d080595927d500ad6
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481868"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Использование MSAL в национальной облачной среде

[Местные облака](authentication-national-cloud.md), также известные как независимых облака, являются физически изолированными экземплярами Azure. Эти области справки Azure обеспечивают соблюдение требований к местонахождениеам данных, независимости и соответствию в географических границах.

Помимо Microsoft Worldwide, Библиотека проверки подлинности Майкрософт (MSAL) позволяет разработчикам приложений в национальных облаках получать маркеры для проверки подлинности и вызова защищенных веб-API. Эти веб-API можно Microsoft Graph или других интерфейсов API Майкрософт.

Включая глобальное облако, Azure Active Directory (Azure AD) развертывается в следующих национальных облаках:  

- Azure Government
- Azure China 21Vianet
- Azure для Германии

В этом руководстве показано, как войти в рабочие и учебные учетные записи, получить маркер доступа и вызвать API Microsoft Graph в [облачной среде Azure для государственных организаций](https://azure.microsoft.com/global-infrastructure/government/) .

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем начать, убедитесь, что выполнены все необходимые условия.

### <a name="choose-the-appropriate-identities"></a>Выберите соответствующие удостоверения

Приложения [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/) могут использовать удостоверения государственных организаций Azure AD и общедоступные удостоверения Azure AD для проверки подлинности пользователей. Так как вы можете использовать любое из этих удостоверений, необходимо решить, какую конечную точку следует выбрать для вашего сценария:

- Общедоступная служба Azure AD. обычно используется, если в Организации уже есть общедоступный клиент Azure AD для поддержки Office 365 (общедоступная или GCC) или другого приложения.
- Azure AD для государственных организаций. обычно используется, если в Организации уже есть клиент Azure AD для государственных организаций, поддерживающий Office 365 (GCC High или DoD) или создающий новый клиент в Azure AD для государственных организаций.

После принятия решения обратите особое внимание на то, где выполняется регистрация приложения. Если вы выбрали общедоступные удостоверения Azure AD для приложения Azure для государственных организаций, необходимо зарегистрировать приложение в общедоступном клиенте Azure AD.

### <a name="get-an-azure-government-subscription"></a>Получение подписки Azure для государственных организаций

Чтобы получить подписку Azure для государственных организаций, см. статью [Управление подпиской и подключение к ней в Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)для государственных организаций.

Если у вас нет подписки Azure для государственных организаций, создайте [бесплатную учетную запись](https://azure.microsoft.com/global-infrastructure/government/request/) , прежде чем начинать работу.

Для получения сведений об использовании национального облака с определенным языком программирования выберите вкладку, соответствующую вашему языку:

## <a name="nettabdonet"></a>[.NET](#tab/donet)

Вы можете использовать MSAL.NET для входа пользователей, получения маркеров и вызова API Microsoft Graph в национальных облаках.

В следующих руководствах показано, как создать веб-приложение MVC для .NET Core 2,2. Приложение использует OpenID Connect Connect для входа пользователей с рабочей и учебной учетной записью в Организации, которая принадлежит национальной облаку.

- Чтобы войти в систему пользователей и получить маркеры, следуйте указаниям в этом руководстве: [Создание пользователей ASP.NET Core для входа в веб-приложение в облаках независимых с платформой Microsoft Identity](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Чтобы вызвать API Microsoft Graph, следуйте указаниям в этом руководстве: [использование платформы Microsoft Identity для вызова api Microsoft Graph из веб-приложения ASP.NET Core 2. x от имени входа пользователя с помощью рабочей и учебной учетной записи в Microsoft National Cloud](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Чтобы включить приложение MSAL. js для облаков независимых:

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

1. Войдите на [портале Azure](https://portal.azure.us/).
    
   Чтобы найти портал Azure конечные точки для других национальных облаков, см. раздел [конечные точки регистрации приложений](authentication-national-cloud.md#app-registration-endpoints).

1. Если ваша учетная запись предоставляет доступ к нескольким клиентам, выберите свою учетную запись в правом верхнем углу и задайте для сеанса портала нужный клиент Azure AD.
1. Перейдите на страницу [Регистрация приложений](https://aka.ms/ra/ff) платформы идентификации Майкрософт для разработчиков.
1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
1. В разделе **Поддерживаемые типы учетных записей**выберите **учетные записи в любом организационном каталоге**.
1. В разделе **URI перенаправления** выберите **веб-** платформу и задайте в качестве значения URL-адрес приложения, основанный на веб-сервере. Инструкции по установке и получению URL-адреса перенаправления в Visual Studio и узле см. в следующих разделах.
1. Выберите **Зарегистрировать**.
1. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)** .
1. Для работы с этим руководством необходимо включить [неявный поток предоставления](v2-oauth2-implicit-grant-flow.md). В левой области навигации зарегистрированного приложения выберите **Проверка подлинности**.
1. В окне **Дополнительные параметры** в разделе **Неявное предоставление** установите флажки **Токен идентификатора** и **Маркеры доступа**. Маркеры идентификации и маркеры доступа являются обязательными, так как это приложение должно входить в систему пользователей и вызывать API.
1. Щелкните **Сохранить**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Шаг 2. Настройка веб-сервера или проекта

- [Скачайте файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) для локального веб-сервера, например node.

  или

- [Скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Затем перейдите к [настройке JavaScript Spa](#step-4-configure-your-javascript-spa) , чтобы настроить пример кода перед его запуском.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Шаг 3. Использование библиотеки проверки подлинности Майкрософт для входа пользователя

Выполните действия, описанные в [руководстве по JavaScript](tutorial-v2-javascript-spa.md#create-your-project) , чтобы создать проект и интегрировать его с MSAL, чтобы войти в систему.

### <a name="step-4-configure-your-javascript-spa"></a>Шаг 4. Настройка JavaScript SPA

Внесите сведения о регистрации приложения в файл `index.html`, созданный во время настройки проекта. В начале вашего файла `<script></script>` между тегами `index.html` вставьте следующий код:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

В этом коде:

- `Enter_the_Application_Id_here` — это значение **идентификатора приложения (клиента)** для зарегистрированного приложения.
- для `Enter_the_Tenant_Info_Here` задан один из следующих параметров:
    - Если приложение поддерживает **учетные записи в этом каталоге Организации**, замените это значение на идентификатор клиента или имя клиента (например, contoso.Microsoft.com).
    - Если приложение поддерживает **учетные записи в любом каталоге Организации**, замените это значение на `organizations`.
    
    Чтобы найти конечные точки проверки подлинности для всех национальных облаков, см. раздел [конечные точки аутентификации Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Личные учетные записи Майкрософт не поддерживаются в национальных облаках.
  
- `graphEndpoint` — это Microsoft Graphная конечная точка для государственных организаций Microsoft Cloud для США.

   Чтобы найти Microsoft Graph конечных точек для всех национальных облаков, см. раздел [Microsoft Graph конечных точек в национальных облаках](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="pythontabpython"></a>[Python](#tab/python)

Чтобы включить приложение MSAL Python для облаков независимых, выполните следующие действия.

- Зарегистрируйте приложение на определенном портале в зависимости от облака. Дополнительные сведения о выборе портала см. в разделе [конечные точки регистрации приложений](authentication-national-cloud.md#app-registration-endpoints) .
- Используйте любой из [примеров](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) из репозитория с внесением нескольких изменений в конфигурацию, в зависимости от облака, которое упоминается далее.
- Используйте конкретный центр в зависимости от облака, в котором вы зарегистрировали приложение. Дополнительные сведения о центрах для различных облаков см. в разделе [Аутентификация Azure AD конечные точки](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Вот пример центра:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- Для вызова Microsoft Graph требуется конкретный URL-адрес конечной точки графа, зависящий от используемого облака. Чтобы найти Microsoft Graph конечных точек для всех национальных облаков, см. статью [Microsoft Graph и корневые конечные точки службы Graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Ниже приведен пример конечной точки графа с областью действия:
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="javatabjava"></a>[Java](#tab/java)

Чтобы включить приложение MSAL для Java в облаках независимых, выполните следующие действия.

- Зарегистрируйте приложение на определенном портале в зависимости от облака. Дополнительные сведения о выборе портала см. в разделе [конечные точки регистрации приложений](authentication-national-cloud.md#app-registration-endpoints) .
- Используйте любой из [примеров](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) из репозитория с небольшими изменениями в конфигурации, в зависимости от облака, которые упоминались далее.
- Используйте конкретный центр в зависимости от облака, в котором вы зарегистрировали приложение. Дополнительные сведения о центрах для различных облаков см. в разделе [Аутентификация Azure AD конечные точки](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Вот пример центра:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Для вызова Microsoft Graph требуется конкретный URL-адрес конечной точки графа, зависящий от используемого облака. Чтобы найти Microsoft Graph конечных точек для всех национальных облаков, см. статью [Microsoft Graph и корневые конечные точки службы Graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Ниже приведен пример конечной точки графа с областью действия:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

MSAL для iOS и macOS можно использовать для получения маркеров в национальных облаках, но при создании `MSALPublicClientApplication`требуется дополнительная настройка.

Например, если вы хотите, чтобы приложение было многопользовательским приложением в национальном облаке (здесь правительство США), можно написать:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swifttabswift"></a>[Swift](#tab/swift)

MSAL для iOS и macOS можно использовать для получения маркеров в национальных облаках, но при создании `MSALPublicClientApplication`требуется дополнительная настройка.

Например, если вы хотите, чтобы приложение было многопользовательским приложением в национальном облаке (здесь правительство США), можно написать:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Дополнительная информация

См. также:

- [Проверка подлинности в национальных облаках](authentication-national-cloud.md)
- [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure для Германии](https://docs.microsoft.com/azure/germany/)
