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
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 93ae820f8c98b749ef8f71b17bf3d540d7886ed6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832129"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Краткое руководство. Добавление возможности входа в веб-приложение Java с помощью учетной записи Майкрософт

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

В этом кратком руководстве вы узнаете, как интегрировать веб-приложение Java с Платформой удостоверений Майкрософт. Ваше приложение сможет авторизовать пользователя, получить маркер доступа для вызова API Microsoft Graph и выполнить запрос к API Microsoft Graph.

Когда вы завершите работу с этим кратким руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Предварительные требования

Для запуска этого примера потребуется:

- [Пакет средств разработки Java (JDK)](https://openjdk.java.net/) версии 8 или последующей и [Maven](https://maven.apache.org/).
- Клиент Azure Active Directory (Azure AD). Дополнительные сведения о том, как получить клиент Azure AD, см. в [этой статье](https://azure.microsoft.com/documentation/articles/active-directory-howto-tenant/).

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
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
>
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
>
> 1. Перейдите на страницу [Регистрация приложений](/azure/active-directory/develop/) Платформы удостоверений Майкрософт для разработчиков.
> 1. Выберите **Новая регистрация**.
> 1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
>    - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `java-webapp`.
>    - Оставьте поле **URI-перенаправление** пустым, и выберите **Регистрация**.
> 1. На странице **Обзор** найдите **идентификатор приложения (клиента)** и **каталога (клиента)** . Скопируйте эти значения для дальнейшего использования.
> 1. В меню выберите **Проверка подлинности**, а затем добавьте следующие сведения:
>    - В поле**URI-перенаправление** добавьте `http://localhost:8080/msal4jsamples/secure/aad` и `http://localhost:8080/msal4jsamples/graph/me`.
>    - Щелкните **Сохранить**.
> 1. В меню выберите **Сертификаты и секреты**, а затем в разделе **Секреты клиента** щелкните **Новый секрет клиента**:
>
>    - Введите описание ключа (для экземпляра секрета приложения).
>    - Выберите срок действия ключа **в 1 год**.
>    - Значение ключа отобразится при нажатии кнопки **Добавить**.
>    - Скопируйте значение ключа для дальнейшего использования. Это значение ключа не будет отображаться повторно и не сможет быть извлечено другими способами, поэтому запишите его, как только оно отобразится на портале Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
>
> Для работы примера кода в этом кратком руководстве необходимо:
>
> 1. Добавить URL-адрес ответа, как `http://localhost:8080/msal4jsamples/secure/aad` и `http://localhost:8080/msal4jsamples/graph/me`.
> 1. Создание Секрета клиента.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести эти изменения для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-aspnet-webapp/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-the-code-sample"></a>Шаг 2. Скачивание примера кода

 [Скачайте пример кода.](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

#### <a name="step-3-configure-the-code-sample"></a>Шаг 3. Настройка примера кода

 1. Извлеките ZIP-файл в локальную папку.
 1. Если вы используете интегрированную среду разработки, откройте образец в вашей избранной интегрированной среде разработки (необязательно).

 1. Откройте файл application.properties, расположенный в папке src/main/resources/, и замените значения полей *aad.clientId*, *aad.authority* и *aad.secretKey* соответствующими значениями **идентификатора приложения**, **идентификатора клиента** и **секрета клиента** следующим образом:

    ```file
    aad.clientId=Enter_the_Application_Id_here
    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
    aad.secretKey=Enter_the_Client_Secret_Here
    aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
    aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
    ```

> [!div renderon="docs"]
> Описание
>
> - `Enter_the_Application_Id_here` — идентификатор регистрируемого приложения.
> - `Enter_the_Client_Secret_Here` — это **секрет клиента**, созданного вами в пункте **Сертификаты и Секреты** для зарегистрированного приложения.
> - `Enter_the_Tenant_Info_Here` — это значение **Идентификатор каталога (клиента)** приложения, которое вы зарегистрировали.

#### <a name="step-4-run-the-code-sample"></a>Шаг 4. Запуск примера кода

Запустить проект можно следующим образом:

Запустите его непосредственно из интегрированной среды разработки, используя встроенный сервер загрузки, или упакуйте его в WAR-файл, используя [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html), и разверните в решение контейнера J2EE, например [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Запуск из интегрированной среды разработки

Если вы запускаете веб-приложение из интегрированной среды разработки, щелкните "Запустить", а затем перейдите на домашнюю страницу проекта. В этом примере стандартная домашняя страница имеет следующий URL-адрес: http://localhost:8080.

1. На основной странице нажмите кнопку **Вход**, чтобы перенаправиться в Azure Active Directory и запросить у пользователя учетные данные.

1. После проверки подлинности пользователь перенаправляется на страницу *http://localhost:8080/msal4jsamples/secure/aad* . После входа на странице появятся сведения об учетной записи, с помощью которой выполнен вход. В примере пользовательского интерфейса есть следующие кнопки:
    - *Выход*: пользователь выходит из приложения и перенаправляется на домашнюю страницу.
    - *Показать сведения о пользователе*: получение маркера Microsoft Graph и вызов Microsoft Graph с помощью запроса, содержащего этот маркер. Этот запрос возвращает сведения о пользователе, который выполнил вход в систему.

> [!IMPORTANT]
> В этом кратком руководстве приложение использует секрет клиента для собственной идентификации в качестве конфиденциального клиента. Так как секрет клиента добавляется в качестве обычного текста в файлы проекта, по соображениям безопасности рекомендуется использовать сертификат вместо секрета клиента, прежде чем использовать приложение в качестве рабочего. Дополнительные сведения о том, как использовать сертификат, см. в статье [Учетные данные сертификата для аутентификации приложения](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Дополнительные сведения

### <a name="getting-msal"></a>Получение MSAL

MSAL4J — это библиотека Java, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному Платформой удостоверений Майкрософт.

Добавьте библиотеку MSAL4J в приложение с помощью Maven или Gradle для управления зависимостями, внеся следующие изменения в файл pom.xml (Maven) или build.gradle (Gradle) приложения.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Инициализация MSAL

Добавьте ссылку на MSAL4J, добавив следующий код в начало файла, в котором будет использоваться MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разрешениях и согласии:

> [!div class="nextstepaction"]
> [Разрешения и согласие](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Дополнительные сведения о потоке авторизации для этого сценария, см. в разделе "Поток кода авторизации Oauth 2.0":

> [!div class="nextstepaction"]
> [Поток кода авторизации Oauth](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
