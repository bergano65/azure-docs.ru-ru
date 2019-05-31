---
title: Использование библиотеки аутентификации Майкрософт (MSAL) в национальных облаках - платформой Microsoft identity
description: Библиотеки аутентификации Майкрософт (MSAL) позволяет разработчикам приложений для получения маркеров для вызова защищенного веб-API. Эти веб-API может быть Microsoft Graph, другие API Майкрософт, партнеров веб-API или собственных веб-API. MSAL поддерживает несколько архитектур и платформ приложений.
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
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235268"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Использование MSAL в национальном облаке среде

[Национальные облака](authentication-national-cloud.md) — это физически изолированный экземпляры Azure. Эти регионы Azure того, чтобы гарантировать, что в рамках географических соблюдаются требования к хранению, независимости и соответствия данных.

В дополнение к Microsoft cloud по всему миру библиотеки аутентификации Майкрософт (MSAL) позволяет разработчикам приложений в национальных облаках получать маркеры для проверки подлинности и вызова защищенного веб-API. Эти веб-API может быть Microsoft Graph или другие API Майкрософт.

Включая глобальное облако Azure Active Directory (Azure AD) развертывается в следующие национальных облаках:  

- Azure Government
- Azure China 21Vianet
- Azure для Германии

В этом руководстве показано, как выполнить вход для работы и учебных учетных записей, получение токена доступа и вызвать Microsoft Graph API в [Azure для государственных организаций](https://azure.microsoft.com/global-infrastructure/government/) среды.

## <a name="prerequisites"></a>Технические условия

Перед началом работы убедитесь, что выполнены следующие предварительные требования.

### <a name="choose-the-appropriate-identities"></a>Выберите соответствующий удостоверений

[Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/) приложения могут использовать удостоверения Azure AD для государственных организаций и Azure AD Public удостоверений для проверки подлинности пользователей. Так как можно использовать любой из этих удостоверений, необходимо решить, какая конечная точка центра, следует выбрать для вашего сценария:

- Общедоступный Azure AD: Обычно используется, если в вашей организации уже есть общедоступной версии Azure AD клиента в службу поддержки Office 365 (Public или GCC) или другим приложением.
- Azure AD для государственных организаций: Обычно используется, если вашей организации уже есть клиент Azure AD для государственных организаций в службу поддержки Office 365 (GCC High или министерства обороны США) или создания нового клиента в Azure AD для государственных организаций.

После определения, особое внимание является местом, где выполняются регистрации приложения. Если общедоступной версии Azure AD удостоверения для приложения Azure для государственных организаций, необходимо зарегистрировать приложение в клиенте Azure AD Public.

### <a name="get-an-azure-government-subscription"></a>Оформление подписки Azure для государственных организаций

Чтобы получить подписку Azure для государственных организаций, см. в разделе [подключение к подписке в Azure для государственных организаций и управление ими](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Если у вас нет подписки Azure для государственных организаций, создайте [бесплатную учетную запись](https://azure.microsoft.com/global-infrastructure/government/request/) перед началом работы.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

1. Войдите на [портале Azure](https://portal.azure.us/).
    
   Чтобы найти конечные точки на портале Azure для других национальных облаков, см. в разделе [конечные точки регистрации приложения](authentication-national-cloud.md#app-registration-endpoints).

1. Если учетная запись предоставляет доступ к более чем одного клиента, выберите свою учетную запись в правом верхнем углу и задать сеанса работы с порталом для нужного Azure AD клиента.
1. Перейдите к [регистрация приложений](https://aka.ms/ra/ff) страницы на платформе Microsoft identity для разработчиков.
1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
1. В разделе **поддерживаемые типы учетных записей**выберите **учетных записей в любой организации directory**.
1. В **URI перенаправления** выберите **Web** платформы и набора, значение URL-адрес приложения, основанное на веб-сервере. См. в следующих разделах инструкции о том, как задать и получить URL-адрес перенаправления в Visual Studio и узел.
1. Выберите **Зарегистрировать**.
1. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)** .
1. Этот учебник необходимо включить [неявного потока предоставления](v2-oauth2-implicit-grant-flow.md). В области слева в зарегистрированном приложении выберите **проверки подлинности**.
1. В **Дополнительные параметры**в разделе **неявное предоставление**выберите **маркеры Идентификации** и **маркеры доступа** флажки. Маркеры Идентификации и токены доступа необходимы, так как это приложение должно входа пользователей и вызова API.
1. Щелкните **Сохранить**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Шаг 2.  Настройка веб-сервера или проекта

- [Загрузить файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) локального веб-сервера, например узел.

  или

- [Загрузите проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Затем переходите к [настройки своего одностраничного приложения JavaScript](#step-4-configure-your-javascript-spa) Чтобы настроить пример кода перед его запуском.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Шаг 3. Использовать библиотеку проверки подлинности Майкрософт для входа пользователя

Выполните шаги [руководстве JavaScript](tutorial-v2-javascript-spa.md#create-your-project) для создания проекта и интеграции с помощью MSAL, чтобы выполнить вход пользователя.

### <a name="step-4-configure-your-javascript-spa"></a>Шаг 4. Настройка одностраничного приложения JavaScript

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

- `Enter_the_Application_Id_here` — **идентификатор приложения (клиент)** значением для данного приложения, которое вы зарегистрировали.
- `Enter_the_Tenant_Info_Here` задается одно из следующих вариантов:
    - Если приложение поддерживает **учетные записи в этот каталог организации**, замените это значение с Идентификатором клиента, или имя (например, contoso.microsoft.com) клиента.
    - Если приложение поддерживает **учетных записей в любой организации directory**, замените это значение с `organizations`.
    
    Чтобы найти конечные точки проверки подлинности для всех национальных облаков, см. в разделе [конечные точки проверки подлинности Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Личные учетные записи Майкрософт не поддерживаются в национальных облаках.
  
- `graphEndpoint` является конечной точкой Microsoft Graph для Microsoft cloud для государственных организаций США.

   Чтобы найти конечных точек Microsoft Graph для всех национальных облаков, см. в разделе [конечных точек Microsoft Graph в национальных облаках](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

MSAL.NET можно использовать для входа пользователей, получения маркеров и вызвать Microsoft Graph API в национальных облаках.

В следующих учебниках показано, как создать .NET Core 2.2 MVC, веб-приложения. Приложение использует OpenID Connect для входа в систему с учетной записью рабочих и учебных в организации, которая принадлежит национальном облаке.

- Для входа в систему и получения маркеров, выполните [учебником](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Чтобы вызвать Microsoft Graph API, выполните [учебником](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet в Azure для Китая](https://docs.microsoft.com/azure/china/)
- [Azure для Германии](https://docs.microsoft.com/azure/germany/)