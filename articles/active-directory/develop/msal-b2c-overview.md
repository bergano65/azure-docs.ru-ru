---
title: Использование MSAL с Azure Active Directory B2CLearn | Службы
titleSuffix: Microsoft identity platform
description: Библиотека проверки подлинности Microsoft для JavaScript (MSAL. js) позволяет приложениям работать с Azure AD B2C и получать маркеры для вызова защищенных веб-API. Этими веб-API могут быть Microsoft Graph, другие интерфейсы Microsoft API, веб-API других пользователей или ваш собственный веб-API.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534488"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Использование библиотеки проверки подлинности Майкрософт для JavaScript для работы с Azure Active Directory B2C

[Библиотека проверки подлинности Microsoft для JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) позволяет разработчикам JavaScript проверять подлинность пользователей с помощью социальных сетей и местных удостоверений с помощью [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Используя Azure AD B2C в качестве службы управления удостоверениями, можно настроить и контролировать, как пользователи подписываются, подписываются и управляют своими профилями при использовании ваших приложений.

Azure AD B2C также позволяет фирменной символике и настраивать пользовательский интерфейс приложений во время процесса проверки подлинности, чтобы обеспечить бесперебойную работу клиентов.

В этой статье показано, как использовать MSAL. js для работы с Azure AD B2C и суммирует ключевые моменты, о которых следует знать. Полное описание и руководство см. [Azure AD B2C документации](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Предварительные условия

Если вы еще не создали собственный [клиент Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), начните создавать его сейчас (вы также можете использовать существующий клиент Azure AD B2C, если он уже есть).

Эта демонстрация состоит из двух частей:

- Защита веб-API.
- как зарегистрировать одностраничное приложение для проверки подлинности и вызова *этого* веб-API.

## <a name="nodejs-web-api"></a>Веб-API Node.js

> [!NOTE]
> В данный момент MSAL. js для Node все еще разрабатывается (см. [план](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Тем временем мы предлагаем использовать [Passport-Azure-AD](https://github.com/AzureAD/passport-azure-ad), библиотеку проверки подлинности для Node. js, разработанную и поддерживаемую корпорацией Майкрософт.

В следующих шагах показано, как **веб-API** может использовать Azure AD B2C для защиты и предоставления доступа к выбранным областям клиентскому приложению.

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

Чтобы защитить веб-API с помощью Azure AD B2C, необходимо сначала зарегистрировать его. Подробные шаги описаны в статье [Register your application](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) (Регистрация вашего приложения).

### <a name="step-2-download-the-sample-application"></a>Шаг 2. скачивание примера приложения

Скачайте пример в виде ZIP-файла или скопируйте его из GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Шаг 3. Настройка проверки подлинности

1. Откройте файл `config.js` в примере.

2. Настройте пример с учетными данными приложения, полученными ранее при регистрации приложения. Измените следующие строки кода, заменив значения именами clientID, Host, tenantId и именем политики.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Дополнительные сведения см. в [примере веб-API для Node. js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

В следующих шагах показано, как **одностраничное приложение** может использовать Azure AD B2C для регистрации, входа и вызова защищенного веб-API.

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

Чтобы выполнить аутентификацию, сначала необходимо зарегистрировать приложение. Подробные шаги описаны в статье [Register your application](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) (Регистрация вашего приложения).

### <a name="step-2-download-the-sample-application"></a>Шаг 2. скачивание примера приложения

Скачайте пример в виде ZIP-файла или скопируйте его из GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Шаг 3. Настройка проверки подлинности

Настройка приложения состоит из двух моментов:

- Настройка конечной точки API и предоставленных областей
- Настройка параметров проверки подлинности и областей токенов

1. Откройте файл `apiConfig.js` в примере.

2. Настройте пример с параметрами, полученными ранее при регистрации веб-API. Измените следующие строки кода, заменив значения адресом веб-API и предоставляемыми областями.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Откройте файл `authConfig.js` в примере.

4. Настройте пример с параметрами, полученными ранее при регистрации одностраничного приложения. Измените следующие строки кода, заменив значения вашими идентификаторами ClientId, центра сертификации и запросами маркеров.

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

Дополнительные сведения см. в [примере одностраничного приложения JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Дальнейшие действия

См. также:
- [Потоки пользователей](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Пользовательские политики](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Настройка UX](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
