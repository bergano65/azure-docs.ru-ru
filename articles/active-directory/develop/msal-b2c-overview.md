---
title: Узнайте, как выполнять интеграцию с Azure AD B2C с помощью библиотеки аутентификации Майкрософт (MSAL)
description: Библиотека аутентификации Майкрософт (MSAL) позволяет разработчикам приложений выполнять интеграцию с Azure AD B2C и получать маркеры для вызова защищенных веб-API. Это могут быть веб-API Microsoft Graph, другие API Майкрософт, сторонние веб-API или ваши собственные веб-API.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87bd8834f840c2246bf3adc1d1f9cd9b8f635915
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191026"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Интеграция библиотеки аутентификации Майкрософт (MSAL) с Azure Active Directory B2C

Библиотека аутентификации Майкрософт (MSAL) позволяет разработчикам приложений аутентифицировать пользователей с помощью удостоверений социальных сетей и локальных удостоверений, используя [Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). Azure Active Directory (Azure AD) B2C — это служба управления идентификацией, которая позволяет настраивать и администрировать процессы входа и регистрации пользователей, а также управлять их профилями при использовании приложений.

Azure Active Directory (Azure AD) B2C также поддерживает настройку пользовательского интерфейса приложений с использованием фирменной символики для обеспечения эффективного взаимодействия клиентов.

В этом руководстве описано, как интегрировать библиотеку аутентификации Майкрософт (MSAL) с Azure Active Directory (Azure AD) B2C.


## <a name="prerequisites"></a>Предварительные требования

Если вы еще не создали собственный [клиент Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), создайте его сейчас. Вы можете использовать имеющийся клиент Azure Active Directory B2C. 

## <a name="javascript"></a>JavaScript

В этом примере показано, как использовать Azure AD B2C в одностраничном приложении для регистрации и входа пользователя, а также вызова защищенного веб-API.

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

Чтобы реализовать аутентификацию, сначала необходимо зарегистрировать приложение. Чтобы зарегистрировать приложение, следуйте [этим инструкциям](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c).

### <a name="steps-2-download-applications"></a>Шаг 2. Скачивание приложений

Скачайте ZIP-файл или клонируйте пример с GitHub.
>git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Шаг 3. Authentication

1. Откройте файл index.html в примере.

2. Укажите для примера идентификатор приложения и ключ, которые вы записали ранее при регистрации приложения. Измените следующие строки кода, заменив значения именами используемого каталога и API:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Имя [сценария поведения пользователя](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies), используемое в этом руководстве, — B2C_1_signupsignin1. Если вы используете другое имя сценария поведения пользователя, укажите его в значении authority.


### <a name="configure-application-to-use-b2clogincom"></a>Настройка приложения для использования `b2clogin.com`

Вы можете использовать `b2clogin.com` вместо `login.microsoftonline.com` в качестве URL-адреса перенаправления при настройке поставщика удостоверений для регистрации и входа в приложение Azure Active Directory (Azure AD) B2C.

**`b2clogin.com`**, например 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` используется в следующих целях:

- Пространство, используемое службами Майкрософт в заголовке файлов cookie, уменьшается.
- URL-адреса больше не содержат ссылку на корпорацию Майкрософт. Например, приложение Azure AD B2C, вероятно, относится к домену login.microsoftonline.com.


 Чтобы использовать b2clogin.com, необходимо обновить конфигурацию приложения.  

1. Укажите для **validateAuthority** значение `false`. Если вы укажете для **ValidateAuthority** значение false, для b2clogin.com будет разрешено перенаправление.

Далее приведен пример установки свойства.
```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Скорее всего приложение Azure AD B2C ссылается на login.microsoftonline.com в нескольких местах, например, в ссылках на потоки пользователя и маркерах утверждений. Убедитесь, что конечная точка авторизации, конечная точка маркера и издателя были обновлены для использования имя_клиента.b2clogin.com.

Выполните этот [пример кода JS для MSAL](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c), чтобы узнать, как использовать предварительную версию библиотеки аутентификации Майкрософт для JavaScript (msal.js) для получения маркера доступа и вызова API, защищенного с помощью Azure AD B2C.

## <a name="next-steps"></a>Дополнительная информация

См. также:

- [Пользовательские политики](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Настройка пользовательского интерфейса](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)