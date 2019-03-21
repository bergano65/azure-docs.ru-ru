---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: f9993058f93cf94ee468a92ac7cbb2d6881e61be
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58214215"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации

На этом шаге необходимо настроить URL-адрес перенаправления сведений о регистрации приложения, а затем добавить идентификатор приложения в одностраничное приложение JavaScript.

### <a name="configure-redirect-url"></a>Настройка URL-адреса перенаправления

Настройте поле `Redirect URL`, используя URL-адрес страницы index.html на основе веб-сервера, а затем нажмите кнопку *Обновить*.

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Инструкции для Visual Studio по получению URL-адреса перенаправления
> Чтобы получить URL-адрес перенаправления, выполните следующие действия:
> 1. Выберите проект в **обозревателе решений** и просмотрите сведения в окне **Свойства**. Если окно **Свойства** не отображается, нажмите клавишу **F4**.
> 2. Скопируйте значение **URL-адреса** в буфер обмена:<br/> ![Свойства проекта](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Вставьте значение в поле **URL-адрес перенаправления** вверху страницы и щелкните **Обновить**.

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Настройка URL-адреса перенаправления для Node.js
> При использовании Node.js порт веб-сервера можно задать в файле *server.js*. В этом руководстве в качестве примера использован порт 30662, но можно применить любой другой доступный порт. Выполните инструкции ниже, чтобы указать URL-адрес перенаправления в сведениях о регистрации приложения.<br/>
> В верхней части этой страницы укажите `http://localhost:30662/` в качестве **URL-адреса перенаправления** или воспользуйтесь `http://localhost:[port]/`, если используете настраиваемый TCP-порт (где *[port]* — это номер настраиваемого TCP-порта), а затем нажмите кнопку **Обновить**.

### <a name="configure-your-javascript-spa-application"></a>Настройка одностраничного приложения JavaScript

1. Внесите сведения о регистрации приложения в файл `index.html`, созданный во время настройки проекта. В начале вашего файла `index.html` между тегами `<script></script>` вставьте следующий код:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/common",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
