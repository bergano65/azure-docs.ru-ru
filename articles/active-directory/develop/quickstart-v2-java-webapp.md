---
title: Краткое руководство. Использование веб-приложений Java с Платформой удостоверений Майкрософт | Azure
description: Узнайте, как реализовать единый вход Майкрософт на веб-приложении Java с помощью OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: 22d65d20220bfda821fe255e08dd056b761a828d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086997"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Краткое руководство. Добавление возможности входа в веб-приложение Java с помощью учетной записи Майкрософт

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

В этом кратком руководстве вы узнаете, как интегрировать веб-приложение Java с Платформой удостоверений Майкрософт. Ваше приложение сможет авторизовать пользователя, получить маркер доступа для вызова API Microsoft Graph и выполнить запрос к API Microsoft Graph. 

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Предварительные требования

Для запуска этого примера потребуется: 
- Комплект разработки Java 8 версии или более поздней и Maven.

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве: оперативно (вариант 1) или вручную (вариант 2).
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте [Регистрация приложений](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) на портале Azure.
> 1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
> 1. Следуйте инструкциям, чтобы быстро скачать и автоматически настроить новое приложение.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
> 
>
> #### <a name="step-1-download-the-code-sample"></a>Шаг 1. Скачивание примера кода
> 
> - [Скачайте пример кода.](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>Шаг 2. Откройте файл "application.properties"
>
> 1. Извлеките ZIP-файл в локальную папку.
> 1. (Необязательно) Если вы используете интегрированную среду разработки, откройте образец в вашей избранной интегрированной среды разработки.
> 1. Откройте файл *application.properties*. Значения для `aad.clientId`, `aad.authority` и `aad.secretKey` вводятся при регистрации заявки на следующем этапе.


> #### <a name="step-3-register-your-application"></a>Шаг 3. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
> 1. Выберите **Новая регистрация**.
> 1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
>    - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `java-webapp`.
>    - Оставьте поле **URI-перенаправление** пустым, и выберите **Регистрация**.
> 1. Найдите значение **Идентификатор приложения (клиента)** приложения. Обновите значение `Enter_the_Application_Id_here` в файле *application.properties*.
> 1. Найдите значение **Идентификатор каталога (клиента)** приложения. Обновите значение `Enter_the_Tenant_Info_Here` в файле *application.properties*. 
> 1. Выберите меню **Проверка подлинности**, а затем добавьте следующие сведения:
>    - В поле**URI-перенаправление** добавьте `http://localhost:8080/msal4jsamples/secure/aad` и `https://localhost:8080/msal4jsamples/graph/users`.
>    - Щелкните **Сохранить**.
> 1. В левом меню выберите **Сертификаты и секреты** и выберите **Новый секрет клиента** в разделе **Секреты клиента**:
>     
>    - Введите описание ключа (экземпляр секрета приложения).
>    - Выберите срок действия ключа**в 1 год**.
>    - При нажатии кнопки **Добавить** будет отображаться значение ключа. 
>    - Скопируйте значение ключа. Откройте файл *application.properties*, загруженный ранее, и обновите значение `Enter_the_Client_Secret_Here` с помощью значения ключа. 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода в этом кратком руководстве необходимо:
> 1. Добавить URL-адрес ответа, как `http://localhost:8080/msal4jsamples/secure/aad` и `https://localhost:8080/msal4jsamples/graph/users`.
> 1. Создание Секрета клиента.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-aspnet-webapp/green-check.png). Ваше приложение настроено с помощью этих атрибутов.
> 
> #### <a name="step-2-download-the-code-sample"></a>Шаг 2. Скачивание примера кода
> 
> - [Скачайте пример кода.](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>Шаг 3. Настройка примера кода 
> 
> 1. Извлеките ZIP-файл в локальную папку.
> 1. Если вы используете интегрированную среду разработки, откройте образец в вашей избранной интегрированной среде разработки (необязательно).
> 1. Откройте файл **application.properties**, который можно найти в *src/main/resources/* .
> 1. Измените свойства приложения.
>   1. Найти `aad.clientId` и измените значение `Enter_the_Application_Id_here` на **идентификатор зарегистрированного приложения (клиента)** . 
>   1. Найдите `aad.authority` и обновите значение `Enter_the_Tenant_Name_Here` значением **Идентификатор каталога (клиента)** для зарегистрированного приложения.
>   1. Найдите `aad.secretKey` и обновите значение `Enter_the_Client_Secret_Here` с помощью **Секрета клиента**, созданного вами в **Сертификаты и Секреты** для зарегистрированного приложения.

#### <a name="step-4-run-the-code-sample"></a>Шаг 4. Запуск примера кода
1. Запустите пример кода, откройте браузер и перейдите по адресу *http://localhost:8080* .
1. Передняя страница содержит кнопку**входа**. Нажмите кнопку **Вход**, чтобы перенаправится в Azure Active Directory. Пользователю будет предложено ввести свои учетные данные.  
1. После успешной проверки подлинности в Azure Active Directory данные будут перенаправлены в *http://localhost:8080/msal4jsamples/secure/aad* . Вход в приложение выполнен. На странице должны отображаться сведения об этой учетной записи. Приложение также будет содержать кнопки для: 
    - *Выход*: Выполнит выход из приложения текущего пользователя и перенаправит его на домашнюю страницу.
    - *Показать пользователей*: Запросит токен для Microsoft Graph, а затем вызовет Microsoft Graph с токеном, присоединенным к запросу, чтобы получить всех пользователей в Клиенте.


## <a name="more-information"></a>Дополнительные сведения

### <a name="getting-msal"></a>Получение MSAL
MSAL4J — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному Платформой удостоверений Майкрософт. Вы можете добавить MSAL4J в приложение с помощью Maven или Gradle для управления зависимостями, внеся следующие изменения в файл pom.xml или build.gradle в приложении. 

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Инициализация Msal
Вы можете добавить ссылку на MSAL4J, добавив следующий код в начало файла, в котором будет использоваться MSAL4J: 

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разрешениях и согласии:

> [!div class="nextstepaction"]
> [Разрешения и согласие](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent)

Дополнительные сведения о потоке авторизации для этого сценария, см. в разделе "Поток кода авторизации Oauth 2.0":

> [!div class="nextstepaction"]
> [Поток кода авторизации Oauth](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
