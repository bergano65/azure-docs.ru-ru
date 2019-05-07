---
title: Использование библиотеки аутентификации Майкрософт (MSAL) в национальных облаках - платформой Microsoft identity
description: Библиотеки аутентификации Майкрософт (MSAL) позволяет разработчикам приложений для получения маркеров для вызова защищенного веб-API. Эти веб-API может быть Microsoft Graph, другие API Майкрософт, сторонних веб-API или собственных веб-API. MSAL поддерживает несколько архитектур приложений и платформ.
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
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075795"
---
# <a name="use-msal-in-national-cloud-environment"></a>Использование MSAL в национальном облаке среде

[Национальные облака](authentication-national-cloud.md) — это физически изолированный экземпляры Azure. Эти регионы Azure призваны обеспечить соблюдение требований по размещению и независимости данных, а также законодательных требований в пределах географических границ.

В дополнение к Microsoft cloud по всему миру библиотеки аутентификации Майкрософт (MSAL) также позволяет разработчикам приложений в национальных облаках получать маркеры для проверки подлинности и вызова защищенного веб-API. Эти веб-API может быть Microsoft Graph или другие API Майкрософт.

Включая глобальное облако Azure Active Directory (Azure AD) развертывается в следующие национальных облаках:  

- Azure для государственных организаций США
- Azure China 21Vianet
- Azure для Германии

В этом руководстве показано, как войти в рабочих и учебных учетных записей, получение токена доступа и вызвать Microsoft Graph API в [Microsoft cloud для государственных организаций США](https://azure.microsoft.com/global-infrastructure/government/) среды.

## <a name="prerequisites"></a>Технические условия

Прежде чем начать, убедитесь, что необходимо выполнить следующие условия.

### <a name="choose-the-appropriate-identities"></a>Выберите соответствующий удостоверений

[Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/) приложения могут использовать удостоверения Azure AD для государственных организаций, а также общих удостоверений Azure AD для проверки подлинности пользователей. Поскольку можно использовать любой из этих удостоверений, необходимо понять и решить, какая конечная точка центра, следует выбрать для вашего сценария:

- Общедоступный Azure AD: Обычно используется, если в вашей организации уже есть общедоступной версии Azure AD клиента в службу поддержки Office 365 (Public или GCC) или другим приложением.
- Azure AD для государственных организаций: Обычно используется, если вашей организации уже есть клиент Azure AD для государственных организаций в службу поддержки Office 365 (GCC High или министерства обороны США) или создаете новый клиент в Azure AD для государственных организаций.

Сделав это, особое внимание является местом, где выполняются регистрации приложения. Если общедоступной версии Azure AD удостоверения для приложения Azure для государственных организаций, необходимо зарегистрировать приложение в клиенте Azure AD Public.

### <a name="get-an-azure-government-subscription"></a>Оформление подписки Azure для государственных организаций

- Чтобы получить подписку Azure для государственных организаций, см. в разделе [управление и подключение к подписке в Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).
- Если у вас нет подписки Azure для государственных организаций, создайте [бесплатную учетную запись](https://azure.microsoft.com/global-infrastructure/government/request/) перед началом работы.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

1. Чтобы зарегистрировать приложение, войдите на [портал Azure](https://portal.azure.us/).
    1. Чтобы найти конечные точки на портале Azure для других национальных облаков, см. в разделе [конечные точки регистрации приложения](authentication-national-cloud.md#app-registration-endpoints)

1. Если учетная запись предоставляет доступ к более чем одного клиента, выберите свою учетную запись в правом верхнем углу и задать сеанса работы с порталом для нужного Azure AD клиента.
1. Перейдите на страницу [Регистрация приложений](https://aka.ms/ra/ff) Платформы удостоверений Майкрософт для разработчиков.
1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
1. В разделе **поддерживаемые типы учетных записей**выберите **учетных записей в любой организации directory**.
1. В разделе **URI перенаправления** выберите платформу **Интернет** и задайте в качестве значения URL-адрес приложения в зависимости от вашего веб-сервера. Инструкции по определению и получению URL-адреса перенаправления в Visual Studio и Node см. в разделах ниже.
1. По завершении щелкните **Зарегистрировать**.
1. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)**.
1. В этом руководстве требуется [потоке предоставления неявные](v2-oauth2-implicit-grant-flow.md) требуется включить. В левой области навигации зарегистрированного приложения выберите **Проверка подлинности**.
1. В окне **Дополнительные параметры** в разделе **Неявное предоставление** установите флажки **Токен идентификатора** и **Маркеры доступа**. Токены идентификатора и маркеры доступа необходимы, так как это приложение должно выполнять вход пользователей и вызов API.
1. Щелкните **Сохранить**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Шаг 2.  Настройка веб-сервера или проекта

- [Загрузить файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) локального веб-сервера, например узел.

  или

- [Загрузите проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

А затем переходите к [«Настройка своего одностраничного приложения JavaScript»](#step-4-configure-your-javascript-spa) Чтобы настроить пример кода перед его выполнением.

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Шаг 3. Использование библиотеки аутентификации Майкрософт (MSAL) для входа пользователя

Выполните шаги [руководстве Javascript](tutorial-v2-javascript-spa.md#create-your-project) для создания проекта и интеграции с помощью библиотеки аутентификации Майкрософт (MSAL) для входа пользователя.

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

Описание

- `Enter_the_Application_Id_here` — это **идентификатор приложения (клиента)**, которое вы зарегистрировали.
- `Enter_the_Tenant_Info_Here` может иметь несколько значений.
    - Если приложение поддерживает **учетные записи в этот каталог организации**, замените это значение с **Арендатора** или **имя_клиента** (например, Contoso.Microsoft.com)
    - Если ваше приложение поддерживает вариант **Учетные записи в любом каталоге организации**, замените это значение на `organizations`.
    -  Чтобы найти конечные точки проверки подлинности для всех национальных облаков, см. в разделе [конечные точки проверки подлинности Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

     > [!NOTE]
     > Личные сценариев Microsoft (MSA) учетные записи не поддерживаются в национальных облаках.
  
-   `graphEndpoint` -Конечная точка Microsoft Graph для Microsoft cloud для государственных организаций США.
    -  Чтобы найти конечных точек Microsoft Graph для всех национальных облаков, см. в разделе [конечных точек Microsoft Graph в национальном облаке](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="net"></a>.NET

MSAL для .NET предоставляет возможность входа пользователей, получить маркер и вызвать Microsoft Graph API в национальных облаках.

Следующие руководства показано, как создать приложение веб-MVC .NET Core 2.2, которое использует OpenID Connect для входа в систему с учетной записью «рабочими и учебными» в своей организации, принадлежащие к национальным облакам.

- Чтобы получить маркер входа в систему, выполните [учебником](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Чтобы вызвать Microsoft Graph API, выполните [учебником](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet в Azure для Китая](https://docs.microsoft.com/azure/china/)
- [Azure для Германии](https://docs.microsoft.com/azure/germany/)