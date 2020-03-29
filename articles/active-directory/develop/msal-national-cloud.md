---
title: Используйте MSAL в национальном облачном приложении Azure
titleSuffix: Microsoft identity platform
description: Библиотека аутентификации Microsoft (MSAL) позволяет разработчикам приложений приобретать токены для вызова защищенных Web-aIS. Эти web-аПО могут быть Microsoft Graph, другими API Майкрософт, партнерскими web-aPI или вашим собственным веб-API. MSAL поддерживает несколько архитектур и платформ приложений.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: dfca2b1311f1b55f19d5709f7c9ca7c3e366769c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695744"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Используйте MSAL в национальной облачной среде

[Национальные облака,](authentication-national-cloud.md)также известные как Суверенные облака, являются физически изолированными экземплярами Azure. Эти регионы Azure помогают убедиться в том, что требования к резидентству, суверенитету и соответствию данных соблюдаются в географических границах.

В дополнение к облаку Microsoft по всему миру Библиотека подлинности Microsoft (MSAL) позволяет разработчикам приложений в национальных облаках приобретать токены для аутентификации и вызова защищенных Web ABI. Эти web-аПО могут быть Microsoft Graph или другими AA Microsoft.

Включая глобальное облако, Активный каталог Azure (Azure AD) развертывается в следующих национальных облаках:  

- Azure для государственных организаций
- Azure China 21Vianet
- Azure для Германии

В этом руководстве показано, как войти в систему для работы и школьных учетных записей, получить токен доступа и вызвать API Microsoft Graph в облачной среде [правительства Azure.](https://azure.microsoft.com/global-infrastructure/government/)

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать, убедитесь, что вы отвечаете этим предпосылкам.

### <a name="choose-the-appropriate-identities"></a>Выбрать соответствующие удостоверения

Приложения [Azure Government](https://docs.microsoft.com/azure/azure-government/) могут использовать идентификаторы Azure AD Government и идентификаторы Azure AD Public для проверки подлинности пользователей. Поскольку вы можете использовать любую из этих идентификационных данных, вам необходимо решить, какую конечную точку полномочий вы должны выбрать для вашего сценария:

- Public AD Azure: обычно используется, если в вашей организации уже есть наемщик Azure AD Public для поддержки Office 365 (public или GCC) или другого приложения.
- Правительство Azure AD: Обычно используется, если у вашей организации уже есть арендатор Azure AD government для поддержки Office 365 (GCC High или DOD) или создается новый арендатор в правительстве Azure AD.

После того, как вы решите, особое внимание, где вы выполняете регистрацию приложения. Если вы выбираете идентификаторы Azure AD Public для приложения Azure Government, необходимо зарегистрировать приложение в своем публичном арендаторе Azure AD.

### <a name="get-an-azure-government-subscription"></a>Получить подписку правительства Azure

Чтобы получить правительственную подписку Azure, см. [Управление и подключение к подписке в правительстве Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)

Если у вас нет правительственной подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/global-infrastructure/government/request/) перед началом.

Для получения подробной информации об использовании национального облака с определенным языком программирования выберите вкладку, соответствующую вашему языку:

## <a name="net"></a>[.NET](#tab/donet)

Вы можете использовать MSAL.NET для регистрации пользователей, приобретения токенов и вызова API Microsoft Graph в национальных облаках.

Следующие учебники демонстрируют, как создать web-приложение .NET Core 2.2 MVC. Приложение использует OpenID Connect для регистрации пользователей с рабочей и школьной учетной записью в организации, которая принадлежит к национальному облаку.

- Чтобы войти в число пользователей и приобрести токены, следуйте этому учебнику: [Создайте ASP.NET пользователей Web-приложений Core в суверенных облаках с платформой идентификации Microsoft.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)
- Чтобы вызвать Microsoft Graph API, следуйте этому учебнику: [Используя идентификационную платформу Майкрософт для вызова API Microsoft Graph от веб-приложения An ASP.NET Core 2.x от имени пользователя, восопданным с помощью своей учетной записи в Microsoft National Cloud.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)

## <a name="javascript"></a>[Javascript](#tab/javascript)

Для включения приложения MSAL.js для суверенных облаков:

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

1. Войдите на [портал Azure](https://portal.azure.us/).
    
   Чтобы найти конечные точки портала Azure для других национальных облаков, смотрите [конечные точки регистрации приложений.](authentication-national-cloud.md#app-registration-endpoints)

1. Если ваша учетная запись предоставляет вам доступ к более чем одному арендатору, выберите учетную запись в правом верхнем углу и установите сеанс портала для желаемого арендатора Azure AD.
1. Перейдите на страницу [регистрации приложений](https://aka.ms/ra/ff) на платформе идентификации Майкрософт для разработчиков.
1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
1. Под **типами поддерживаемых учетных записей**выберите **учетные записи в любом каталоге организационных элементов.**
1. В разделе **Redirect URI** выберите **веб-платформу** и установите значение URL приложения на основе вашего веб-сервера. В следующих разделах смотрите инструкции о том, как установить и получить URL-адрес перенаправления в Visual Studio и Node.
1. Выберите **Зарегистрировать**.
1. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)**.
1. Этот учебник требует от вас, чтобы включить [неявный поток гранта.](v2-oauth2-implicit-grant-flow.md) В левой области зарегистрированного приложения выберите **Проверка подлинности**.
1. В окне **Дополнительные параметры** в разделе **Неявное предоставление** установите флажки **Токен идентификатора** и **Маркеры доступа**. Идентификаторы и токены доступа необходимы, потому что это приложение должно войти в пользователя и вызвать API.
1. Нажмите кнопку **Сохранить**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Шаг 2: Настройка веб-сервера или проекта

- [Загрузите файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) для локального веб-сервера, например Узла.

  или диспетчер конфигурации служб

- [Скачать проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Затем перейдите к [настройке JavaScript SPA,](#step-4-configure-your-javascript-spa) чтобы настроить образец кода перед запуском.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Шаг 3: Используйте библиотеку аутентификации Майкрософт для регистрации пользователя

Выполните последующие шаги в [учебнике JavaScript,](tutorial-v2-javascript-spa.md#create-your-project) чтобы создать свой проект и интегрироваться с MSAL, чтобы войти в пользователя.

### <a name="step-4-configure-your-javascript-spa"></a>Шаг 4: Налаживайте JavaScript SPA

Внесите сведения о регистрации приложения в файл `index.html`, созданный во время настройки проекта. В начале вашего файла `index.html` между тегами `<script></script>` вставьте следующий код:

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

- `Enter_the_Application_Id_here`является значением **id-значения приложения (клиента)** для зарегистрированного приложения.
- `Enter_the_Tenant_Info_Here`устанавливается на один из следующих вариантов:
    - Если приложение поддерживает **учетные записи в этом каталоге организации,** замените это значение идентификатором или идентификатором клиента (например, contoso.microsoft.com).
    - Если приложение поддерживает **учетные записи в любом каталоге организации,** замените это значение `organizations`на .
    
    Чтобы найти конечные точки проверки подлинности для всех национальных облаков, смотрите [конечные точки проверки подлинности Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

    > [!NOTE]
    > Личные учетные записи Майкрософт не поддерживаются в национальных облаках.
  
- `graphEndpoint`— конечная точка Microsoft Graph для облака Майкрософт для правительства США.

   Чтобы найти конечные точки Microsoft Graph [Microsoft Graph endpoints in national clouds](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)для всех национальных облаков, см.

## <a name="python"></a>[Python](#tab/python)

Для включения приложения MSAL Python для суверенных облаков:

- Зарегистрируйте приложение на определенном портале в зависимости от облака. Для получения дополнительной информации о том, как выбрать портал, обратитесь к [конечным точкам регистрации приложений](authentication-national-cloud.md#app-registration-endpoints)
- Используйте любой из [образцов](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) репо с некоторыми изменениями в конфигурации, в зависимости от облака, которое упоминается дальше.
- Используйте определенный орган, в зависимости от облака, в которое вы зарегистрировали приложение. Для получения дополнительной информации о органах власти для различных облаков, обратитесь [Azure AD аутентификации конечных точек](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Вот пример органа:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- Для вызова графика Майкрософт требуется определенный URL-адрес точки графика Graph, который зависит от того, какое облако вы используете. Чтобы найти конечные точки Microsoft Graph для всех национальных облаков, обратитесь к [конечным точкам службы Microsoft Graph и Graph Explorer.](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

    Вот пример конечной точки графика с областью охвата:
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="java"></a>[Java](#tab/java)

Для включения приложения MSAL для Java для суверенных облаков:

- Зарегистрируйте приложение на определенном портале в зависимости от облака. Для получения дополнительной информации о том, как выбрать портал, обратитесь к [конечным точкам регистрации приложений](authentication-national-cloud.md#app-registration-endpoints)
- Используйте любой из [образцов](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) репо с некоторыми изменениями в конфигурации, в зависимости от облака, которые упоминаются дальше.
- Используйте определенный орган, в зависимости от облака, в которое вы зарегистрировали приложение. Для получения дополнительной информации о органах власти для различных облаков, обратитесь [Azure AD аутентификации конечных точек](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Вот пример органа:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Для вызова графика Майкрософт требуется определенный URL-адрес точки графика Graph, который зависит от того, какое облако вы используете. Чтобы найти конечные точки Microsoft Graph для всех национальных облаков, обратитесь к [конечным точкам службы Microsoft Graph и Graph Explorer.](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

Вот пример конечной точки графика с областью охвата:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL для iOS и macOS может использоваться для приобретения токенов в `MSALPublicClientApplication`национальных облаках, но это требует дополнительной конфигурации при создании.

Например, если вы хотите, чтобы ваше приложение было мультитенантным приложением в национальном облаке (здесь правительство США), вы можете написать:

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

## <a name="swift"></a>[Swift](#tab/swift)

MSAL для iOS и macOS может использоваться для приобретения токенов в `MSALPublicClientApplication`национальных облаках, но это требует дополнительной конфигурации при создании.

Например, если вы хотите, чтобы ваше приложение было мультитенантным приложением в национальном облаке (здесь правительство США), вы можете написать:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Аутентификация в национальных облаках](authentication-national-cloud.md)
- [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/)
- [Лазурный Китай 21Вианет](https://docs.microsoft.com/azure/china/)
- [Azure для Германии](https://docs.microsoft.com/azure/germany/)
