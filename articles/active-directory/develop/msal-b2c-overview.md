---
title: Узнайте, как приложения могут взаимодействовать с Azure AD B2C с помощью библиотеки проверки подлинности Майкрософт
description: Библиотека аутентификации Майкрософт (MSAL) позволяет приложениям взаимодействовать с Azure AD B2C и получать маркеры для вызова защищенных веб-API. Этими веб-API могут быть Microsoft Graph, другие интерфейсы Microsoft API, веб-API других пользователей или ваш собственный веб-API.
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
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 784ec507027d6ec0ac1b5288c101e2a76cab436e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835054"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Использование библиотеки проверки подлинности Майкрософт для взаимодействия с Azure Active Directory B2C

Библиотека аутентификации Майкрософт (MSAL) позволяет разработчикам приложений аутентифицировать пользователей с помощью удостоверений социальных сетей и локальных удостоверений, используя [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C — это служба управления удостоверениями. С его помощью вы можете настраивать и контролировать процесс регистрации, входа и управления профилями клиентов при использовании ими ваших приложений.

Azure AD B2C также поддерживает настройку пользовательского интерфейса приложений с использованием фирменной символики для обеспечения эффективного взаимодействия клиентов.

В этом руководстве показано, как использовать MSAL для взаимодействия с Azure AD B2C.

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не создали собственный [Арендатор Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), создайте его сейчас. Вы также можете использовать имеющийся арендатор Azure AD B2C.

## <a name="javascript"></a>JavaScript

В этом примере показано, как использовать Azure AD B2C в одностраничном приложении для регистрации и входа, а также вызова защищенного веб-API.

### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения

Чтобы выполнить аутентификацию, сначала необходимо зарегистрировать приложение. Подробные шаги описаны в статье [Register your application](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) (Регистрация вашего приложения).

### <a name="step-2-download-the-sample-application"></a>Шаг 2. Загрузка примера приложения

Скачайте пример в виде ZIP-файла или скопируйте его из GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Шаг 3. Настройка аутентификации

1. Откройте файл **index.html** в примере.

1. Укажите для примера идентификатор приложения и ключ, которые вы записали ранее при регистрации приложения. Измените следующие строки кода, заменив значения именами используемого каталога и API:

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

Название [потока пользователя](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies), используемое в этом руководстве, — **B2C_1_signupsignin1**. Если вы используете другое название потока пользователей, установите для параметра **Центр** значение с таким названием.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Шаг 4. Настройте приложение для использования `b2clogin.com`

Вы можете использовать `b2clogin.com` вместо `login.microsoftonline.com` в качестве URL-адреса перенаправления. Это можно сделать в приложении Azure AD B2C, когда вы настраиваете поставщик удостоверений для регистрации и входа.

Использование `b2clogin.com` в контексте `https://your-tenant-name.b2clogin.com/your-tenant-guid` имеет следующие последствия.

- Службы Майкрософт потребляют меньше пространства в заголовке файла cookie.
- URL-адреса больше не содержат ссылку на корпорацию Майкрософт. Например, приложение Azure AD B2C, скорее всего, ссылается на `login.microsoftonline.com`.

 Чтобы использовать `b2clogin.com` необходимо обновить конфигурацию приложения.  

- Установите свойство **validateAuthority** в значение `false`, чтобы можно было выполнять перенаправление с помощью `b2clogin.com`.

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
> Скорее всего приложение Azure AD B2C ссылается на `login.microsoftonline.com` в нескольких местах, например, в ссылках на потоки пользователя и маркерах утверждений. Убедитесь, что конечная точка авторизации, конечная точка маркера и издателя были обновлены для использования `your-tenant-name.b2clogin.com`.

Следуйте этому [примеру сценария MSAL JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c), чтобы узнать, как использовать MSAL Preview for JavaScript (MSAL.js). Образец получает маркер доступа и вызывает API, защищенный Azure AD B2C.

## <a name="next-steps"></a>Дополнительная информация

См. также:

- [Пользовательские политики](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Настройка пользовательского интерфейса](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)