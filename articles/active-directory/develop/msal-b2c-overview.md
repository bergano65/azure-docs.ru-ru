---
title: Используйте MSAL с Azure Active Directory B2CLearn (ru) Azure
titleSuffix: Microsoft identity platform
description: Библиотека аутентификации Microsoft для JavaScript (MSAL.js) позволяет приложениям работать с Azure AD B2C и приобретать токены для вызова защищенных Web AIS. Этими веб-API могут быть Microsoft Graph, другие интерфейсы Microsoft API, веб-API других пользователей или ваш собственный веб-API.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534488"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Используйте библиотеку аутентификации Майкрософт для JavaScript для работы с Активным каталогом Azure B2C

[Библиотека аутентификации Microsoft для JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) позволяет разработчикам JavaScript аутентифицировать пользователей с социальными и локальными идентификаторами с помощью [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Используя Azure AD B2C в качестве службы управления идентификацией, вы можете настроить и контролировать, как клиенты регистрируются, регистрируются и управляют своими профилями при использовании ваших приложений.

Azure AD B2C также позволяет заклеймить и настроить интерфейс приложений в процессе проверки подлинности, чтобы обеспечить беспрепятственный опыт для ваших клиентов.

В этой статье показано, как использовать MSAL.js для работы с Azure AD B2C, и кратко излагаются ключевые моменты, о которых вы должны знать. Для полного обсуждения и руководства, пожалуйста, обратитесь к [Документации Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не создали свой собственный [арендатор Azure AD B2C,](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)начните с создания его сейчас (вы также можете использовать существующий арендатор Azure AD B2C, если он уже у вас есть).

Эта демонстрация состоит из двух частей:

- как защитить веб-API.
- как зарегистрировать одностраничное приложение для проверки подлинности и *вызова* этого веб-API.

## <a name="nodejs-web-api"></a>Веб-API Node.js

> [!NOTE]
> На данный момент, MSAL.js для узла все еще находится в разработке (см. [дорожную карту](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). В то же время, мы предлагаем использовать [паспорт-azure-ad](https://github.com/AzureAD/passport-azure-ad), библиотека аутентификации для Node.js разработана и поддерживается корпорацией Майкрософт.

Следующие шаги демонстрируют, как **веб-API** может использовать Azure AD B2C для защиты и предоставления выбранных областей клиенту.

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

Чтобы защитить веб-API с помощью Azure AD B2C, сначала необходимо зарегистрировать его. Подробные шаги описаны в статье [Register your application](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) (Регистрация вашего приложения).

### <a name="step-2-download-the-sample-application"></a>Шаг 2: Скачать пример приложения

Скачайте пример в виде ZIP-файла или скопируйте его из GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Шаг 3: Настройка аутентификации

1. Откройте файл `config.js` в примере.

2. Навлажив апробусс с учетными данными приложения, полученными ранее при регистрации приложения. Измените следующие строки кода, заменив значения именами вашего clientID, host, tenantId и имени политики.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Для получения дополнительной информации, проверить этот [Node.js B2C веб-API образца](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

Следующие шаги демонстрируют, как **одностраничное приложение** может использовать Azure AD B2C для регистрации, регистрации и вызова защищенного web API.

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

Чтобы выполнить аутентификацию, сначала необходимо зарегистрировать приложение. Подробные шаги описаны в статье [Register your application](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) (Регистрация вашего приложения).

### <a name="step-2-download-the-sample-application"></a>Шаг 2: Скачать пример приложения

Скачайте пример в виде ZIP-файла или скопируйте его из GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Шаг 3: Настройка аутентификации

В настройке приложения есть две точки, представляющие интерес:

- Настройка конечных точек API и открытых областей
- Настройка параметров проверки подлинности и областей маркеров

1. Откройте файл `apiConfig.js` в примере.

2. Налажить образец с параметрами, которые вы получили ранее при регистрации веб-API. Измените следующие строки кода, заменив значения адресом вашего web API и открытыми областями.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Откройте файл `authConfig.js` в примере.

4. Навлажив апробу с параметрами, полученными ранее при регистрации одностраничного приложения. Измените следующие строки кода, заменив значения на ваши значения ClientId, метаданные авторитета и области запроса токенов.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Для получения дополнительной информации, проверить этот [JavaScript B2C одностраничный образец приложения](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Следующие шаги

См. также:
- [Потоки пользователей](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Пользовательские политики](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Настройка UX](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
