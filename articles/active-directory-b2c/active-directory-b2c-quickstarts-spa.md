---
title: Краткое руководство. Настройка входа для одностраничного приложения (SPA)
titleSuffix: Azure AD B2C
description: В этом кратком руководстве рассматривается запуск примера одностраничного приложения, использующего Azure Active Directory B2C для предоставления возможности входа с учетной записью.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef6fd46ef51cff9823c93e2297a738f95494f577
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948340"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Краткое руководство. Настройка входа в одностраничное приложение с помощью Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C обеспечивает управление идентификаторами облака для защиты приложения, бизнеса и клиентов. Azure AD B2C позволяет приложениям аутентифицироваться в учетных записях социальных сетей и корпоративных учетных записях с помощью протоколов на базе открытых стандартов. В этом кратком руководстве мы будем использовать одностраничное приложение для входа с помощью поставщика удостоверений в социальных сетях и вызова защищенного веб-API Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) с рабочей нагрузкой **ASP.NET и веб-разработка**.
- [Node.js](https://nodejs.org/en/download/)
- Учетная запись социальных сетей Facebook, Google или Майкрософт
- Пример кода из GitHub: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Вы можете [скачать ZIP-архив](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) или клонировать репозиторий:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Выполнение приложения

1. Запустите сервер, выполнив в командной строке Node.js следующие команды:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js выводит номер порта, от которого на localhost ожидается передача данных.

    ```
    Listening on port 6420...
    ```

2. Перейдите по URL-адресу приложения. Например, `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Вход с помощью учетной записи

1. Нажмите кнопку **Login** (Вход), чтобы запустить рабочий процесс.

    ![Пример открытого в браузере одностраничного приложения](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    Образец поддерживает несколько вариантов регистрации, в том числе с использованием поставщика удостоверений в социальных сетях, а также создание локальной учетной записи путем использования адреса электронной почты. В рамках этого краткого руководства используется учетная запись поставщика удостоверений в социальных сетях (Facebook, Google или Майкрософт).

2. В Azure AD B2C в качестве примера веб-приложения предоставляется пользовательская страница входа для вымышленной компании Fabrikam. Чтобы зарегистрироваться с помощью поставщика удостоверений в социальных сетях, нажмите кнопку поставщика удостоверений, которого нужно использовать.

    ![Страница входа или регистрации с кнопками поставщика удостоверений](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Выполните проверку подлинности (вход) с помощью данных учетной записи социальных сетей и авторизацию приложения для чтения информации из учетной записи социальных сетей. Предоставляя доступ, приложение может получить сведения о профиле из учетной записи социальных сетей (например, имя и город).

3. Завершите процесс входа для поставщика удостоверений.

## <a name="access-a-protected-api-resource"></a>Получение доступа к защищенному ресурсу API

Нажмите кнопку **Call Web API** (Вызов веб-API), чтобы вызов веб-API возвратил ваше отображаемое имя в качестве объекта JSON.

![Пример приложения в браузере, отображающий ответ веб-API](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Наше одностраничное приложение включает маркер доступа в запрос к защищенному ресурсу веб-API.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете использовать свой клиент Azure AD B2C при работе с другими руководствами или краткими пособиями по Azure AD B2C. [Удалите клиент Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), если он больше не нужен.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве используется пример одностраничного приложения для выполнения следующих задач:

* вход в систему с пользовательской страницы входа;
* вход с помощью поставщика удостоверений социальных сетей;
* создание учетной записи Azure AD B2C;
* вызов веб-API, защищенного Azure AD B2C.

Попробуйте создать собственный клиент Azure AD B2C.

> [!div class="nextstepaction"]
> [Создание клиента Azure Active Directory B2C на портале Azure](tutorial-create-tenant.md)
