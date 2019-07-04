---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185895"
---
## <a name="register-your-application"></a>Регистрация приложения

Зарегистрировать приложение и добавить сведения о его регистрации в решение можно двумя способами:

### <a name="option-1-express-mode"></a>Вариант 1. Экспресс-режим

Чтобы быстро зарегистрировать приложение, сделайте следующее:

1. Откройте на [портале Azure новую панель регистрации приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Введите имя своего приложения и щелкните **Зарегистрировать**.
1. Следуйте инструкциям для загрузки и автоматической настройки нового приложения одним щелчком мыши.

### <a name="option-2-advanced-mode"></a>Вариант 2. Расширенный режим

Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:

1. Перейдите в Visual Studio и:
   1. В обозревателе решений выберите проект и просмотрите окно "Свойства" (если это окно не отображается, нажмите клавишу F4).
   1. Для параметра "SSL включен" измените значение на `True`.
   1. Щелкните правой кнопкой мыши проект в Visual Studio, а затем выберите **Свойства** и вкладку **Интернет**. В разделе *Серверы* измените *URL-адрес проекта* на URL-адрес SSL.
   1. Скопируйте URL-адрес SSL. Добавьте его в список URL-адресов перенаправления в списке на портале регистрации на следующем шаге:<br/><br/>![Свойства проекта](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
1. Выберите **Новая регистрация**.
1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
   1. В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `ASPNET-Tutorial`.
   1. Добавьте URL-адрес SSL, скопированный из Visual Studio на шаге 1 (для экземпляра `https://localhost:44368/`) в поле **URL-адреса ответа** и нажмите **Зарегистрировать**.
1. Выберите меню **Проверка подлинности**, установите значение **Токен идентификатора** в разделе **Неявное предоставление**, а затем нажмите кнопку **Сохранить**.
1. Добавьте следующий код в файл `web.config`, расположенный в корневой папке в разделе `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Замените `ClientId` зарегистрированным идентификатором приложения.
1. Замените `redirectUri` URL-адресом SSL проекта.
