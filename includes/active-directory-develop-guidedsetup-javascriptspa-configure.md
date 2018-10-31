---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: b3d46e10facdef26b36c910a5c23b40a415a2894
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988428"
---
## <a name="register-your-application"></a>Регистрация приложения

Существует несколько способов регистрации приложений. Выберите тот, который лучше всего соответствует вашим потребностям:
* [экспресс-режим — использование быстрого запуска SPA для настройки приложения](#option-1-register-your-application-express-mode);
* [расширенный режим — ручная настройка параметров приложения](#option-2-register-your-application-advanced-mode).

### <a name="option-1-register-your-application-express-mode"></a>Вариант 1. Регистрация приложения (экспресс-режим).

1. Войдите на [портал регистрации приложений Azure (предварительная версия)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs), чтобы зарегистрировать приложение.
1. На странице **Register an application** (Регистрация приложения) введите имя приложения.
1. В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
1. По завершении щелкните **Зарегистрировать**.
1. Следуйте инструкциям быстрого запуска для загрузки и автоматической настройки нового приложения одним щелчком мыши.

### <a name="option-2-register-your-application-advanced-mode"></a>Вариант 2. Регистрация приложения (расширенный режим).

1. Войдите на [портал Azure](https://portal.azure.com/), чтобы зарегистрировать приложение.
1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
1. В области навигации слева выберите службу **Azure Active Directory**, а затем выберите **App registrations (Preview) (Регистрация приложений (предварительная версия)) > Новая регистрация**.
1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
1. В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
1. В разделе **URI перенаправления** выберите платформу **Интернет** и задайте в качестве значения URL-адрес приложения в зависимости от вашего веб-сервера. Инструкции по определению и получению URL-адреса перенаправления в Visual Studio и Node см. в разделах ниже.
1. По завершении щелкните **Зарегистрировать**.
1. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)**.
1. Для быстрого запуска должно быть включено [неявное предоставление потока](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md). В левой области навигации зарегистрированного приложения выберите **Проверка подлинности**.
1. В окне **Дополнительные параметры** в разделе **Неявное предоставление** установите флажки **Токены идентификатора** и **Маркеры доступа**. Токены идентификатора и маркеры доступа необходимы, так как это приложение должно выполнять вход пользователей и вызов API.
1. Щелкните **Сохранить**.

> #### <a name="setting-the-redirect-url-for-node"></a>Настройка URL-адреса перенаправления для Node
> При использовании Node.js порт веб-сервера можно задать в файле *server.js*. В этом руководстве в качестве примера использован порт 30662, но можно применить любой другой доступный порт. Выполните инструкции ниже, чтобы указать URL-адрес перенаправления в сведениях о регистрации приложения.<br/>
> - Вернитесь на страницу *регистрации приложений* и укажите для `http://localhost:30662/` значение `Redirect URL` или укажите `http://localhost:[port]/`, если вы используете другой номер TCP-порта (где *[port]*  — это номер пользовательского TCP-порта).

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Инструкции для Visual Studio по получению URL-адреса перенаправления
> Чтобы получить URL-адрес перенаправления, выполните следующие действия:
> 1. Выберите проект в **обозревателе решений** и просмотрите сведения в окне **Свойства**. Если окно **Свойства** не отображается, нажмите клавишу **F4**.
> 2. Скопируйте значение **URL-адреса** в буфер обмена:<br/> ![Свойства проекта](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Вернитесь на страницу *регистрации приложений* и вставьте это значение как **URL-адрес перенаправления**.

#### <a name="configure-your-javascript-spa"></a>Настройка одностраничного приложения JavaScript

1. Внесите сведения о регистрации приложения в файл `index.html`, созданный во время настройки проекта. В начале вашего файла `index.html` между тегами `<script></script>` вставьте следующий код:

    ```javascript
    var applicationConfig = {
        clientID: "[Enter the application Id here]",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Замените <code>Enter the application Id here</code> зарегистрированным идентификатором приложения.
</li>
</ol>
