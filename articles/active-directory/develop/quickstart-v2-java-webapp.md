---
title: Краткое руководство. Добавление возможности входа с помощью учетной записи Майкрософт в веб-приложение Java | Azure
titleSuffix: Microsoft identity platform
description: Из этого краткого руководства вы узнаете, как реализовать вход с учетной записью Майкрософт в веб-приложении Java с помощью OpenID Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: f00a935815b64f7c2c06dd33130c1a950582e5c3
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743494"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Краткое руководство. Добавление возможности входа в веб-приложение Java с помощью учетной записи Майкрософт

В этом кратком руководстве вы узнаете, как интегрировать веб-приложение Java с Платформой удостоверений Майкрософт. Ваше приложение сможет авторизовать пользователя, получить маркер доступа для вызова API Microsoft Graph и выполнить запрос к API Microsoft Graph.

Когда вы завершите работу с этим кратким руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory. (Иллюстрацию см. в разделе [Как работает этот пример](#how-the-sample-works).)

## <a name="prerequisites"></a>Предварительные требования

Для запуска этого примера потребуется:

- [Пакет средств разработки Java (JDK)](https://openjdk.java.net/) версии 8 или последующей и [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве: оперативно (вариант 1) или вручную (вариант 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте страницу [регистрации приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs) на портале Azure и приступите к работе.
> 1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
> 1. Следуйте инструкциям на экране, чтобы скачать автоматически настроенный код приложения.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
>
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
>
> Чтобы вручную зарегистрировать приложение и добавить сведения о его регистрации в приложение, сделайте следующее:
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
>
> 1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
> 1. Выберите **Новая регистрация**.
> 1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
>    - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `java-webapp`.
>    - Выберите **Зарегистрировать**.
> 1. На странице **Обзор** найдите **идентификатор приложения (клиента)** и **каталога (клиента)** . Скопируйте эти значения для дальнейшего использования.
> 1. В меню выберите **Проверка подлинности**, а затем добавьте следующие сведения:
>    - Добавьте конфигурацию **веб-платформы**.  В качестве **URI-кодов перенаправления** укажите `https://localhost:8443/msal4jsample/secure/aad` и `https://localhost:8443/msal4jsample/graph/me`.
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
> 1. Добавить URL-адрес ответа как `https://localhost:8443/msal4jsample/secure/aad` и `https://localhost:8443/msal4jsample/graph/me`.
> 1. Создание Секрета клиента.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести эти изменения для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-aspnet-webapp/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-the-code-sample"></a>Шаг 2. Скачивание примера кода
> [!div renderon="docs"]
> [Скачайте пример кода.](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Скачайте проект и извлеките ZIP-файл в локальную папку, расположенную как можно ближе к корневой папке (например, **C:\Azure-Samples**).
>
> Для использования протокола HTTPS с localhost нужно указать значения для свойств server.ssl.key. Чтобы создать самозаверяющий сертификат, используйте служебную программу keytool (входит в JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Поместите созданный файл хранилища ключей в папку "resources".

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Скачивание примера кода](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Шаг 3. Настройка примера кода
> 1. Извлеките ZIP-файл в локальную папку.
> 1. Если вы используете интегрированную среду разработки, откройте образец в вашей избранной интегрированной среде разработки (необязательно).
> 1. Откройте файл application.properties, расположенный в папке src/main/resources/, и замените значения полей *aad.clientId*, *aad.authority* и *aad.secretKey* соответствующими значениями **идентификатора приложения**, **идентификатора клиента** и **секрета клиента** следующим образом:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Где:
>
> - `Enter_the_Application_Id_here` — идентификатор регистрируемого приложения.
> - `Enter_the_Client_Secret_Here` — это **секрет клиента**, созданного вами в пункте **Сертификаты и Секреты** для зарегистрированного приложения.
> - `Enter_the_Tenant_Info_Here` — это значение **Идентификатор каталога (клиента)** приложения, которое вы зарегистрировали.
> 1. Для использования протокола HTTPS с localhost нужно указать значения для свойств server.ssl.key. Чтобы создать самозаверяющий сертификат, используйте служебную программу keytool (входит в JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Поместите созданный файл хранилища ключей в папку "resources".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Шаг 3. Запуск примера кода
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Шаг 4. Запуск примера кода

Запустить проект можно следующим образом:

Запустите его непосредственно из интегрированной среды разработки, используя встроенный сервер загрузки, или упакуйте его в WAR-файл, используя [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html), и разверните в решение контейнера J2EE, например [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Запуск из интегрированной среды разработки

Если вы запускаете веб-приложение из интегрированной среды разработки, щелкните "Запустить", а затем перейдите на домашнюю страницу проекта. В этом примере стандартная домашняя страница имеет следующий URL-адрес: https://localhost:8443.

1. На основной странице нажмите кнопку **Вход**, чтобы перенаправиться в Azure Active Directory и запросить у пользователя учетные данные.

1. После проверки подлинности пользователь перенаправляется на страницу *https://localhost:8443/msal4jsample/secure/aad* . После входа на странице появятся сведения об учетной записи, с помощью которой выполнен вход. В примере пользовательского интерфейса есть следующие кнопки:
    - *Выход*: пользователь выходит из приложения и перенаправляется на домашнюю страницу.
    - *Показать сведения о пользователе*: получение маркера Microsoft Graph и вызов Microsoft Graph с помощью запроса, содержащего этот маркер. Этот запрос возвращает сведения о пользователе, который выполнил вход в систему.

##### <a name="running-from-tomcat"></a>Запуск из Tomcat

Если вы хотите развернуть пример веб-приложения в Tomcat, необходимо внести несколько изменений в исходный код.

1. Откройте файл ms-identity-java-webapp/pom.xml.
    - В разделе `<name>msal-web-sample</name>` добавьте `<packaging>war</packaging>`.

2. Откройте файл ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication.

    - Удалите весь исходный код, заменив его следующим:

   ```Java
    package com.microsoft.azure.msalwebsample;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.builder.SpringApplicationBuilder;
    import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

    @SpringBootApplication
    public class MsalWebSampleApplication extends SpringBootServletInitializer {

     public static void main(String[] args) {
      SpringApplication.run(MsalWebSampleApplication.class, args);
     }

     @Override
     protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(MsalWebSampleApplication.class);
     }
    }
   ```

3.   HTTP-порт Tomcat по умолчанию — 8080, но для HTTPS-подключений требуется порт 8443. Чтобы выполнить настройку:
        - Перейдите к файлу tomcat/conf/server.xml
        - Найдите тег `<connector>` и замените существующий соединитель следующим:

        ```xml
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ```

4. Откройте командную строку, перейдите в корневую папку этого примера (где находится файл pom.xml) и запустите `mvn package` для сборки проекта.
    - В целевом каталоге будет создан файл `msal-web-sample-0.1.0.war`.
    - Измените имя файла на `msal4jsample.war`.
    - Разверните этот WAR-файл с помощью Tomcat или любого другого решения J2EE для контейнеров.
        - Чтобы выполнить развертывание, скопируйте файл msal4jsample.war в каталог `/webapps/` в своей установке Tomcat, а затем запустите сервер Tomcat.

5. После завершения развертывания перейдите по адресу https://localhost:8443/msal4jsample в браузере.


> [!IMPORTANT]
> В этом кратком руководстве приложение использует секрет клиента для собственной идентификации в качестве конфиденциального клиента. Так как секрет клиента добавляется в качестве обычного текста в файлы проекта, по соображениям безопасности рекомендуется использовать сертификат вместо секрета клиента, прежде чем использовать приложение в качестве рабочего. Дополнительные сведения о том, как использовать сертификат, см. в статье [Учетные данные сертификата для аутентификации приложения](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Дополнительные сведения

### <a name="how-the-sample-works"></a>Как работает этот пример
![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Получение MSAL

MSAL для Java (MSAL4J) — это библиотека Java, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт.

Добавьте библиотеку MSAL4J в приложение с помощью Maven или Gradle для управления зависимостями, внеся следующие изменения в файл pom.xml (Maven) или build.gradle (Gradle) приложения.

В файле pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

В файле build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Инициализация MSAL

Добавьте ссылку на MSAL для Java, добавив следующий код в начало файла, в котором будет использоваться MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Next Steps

Тема создания веб-приложений, которые выполняют вход пользователей с помощью платформы Microsoft Identity, подробно рассматривается в нашей серии сценариев:

> [!div class="nextstepaction"]
> [Scenario: Веб-приложение, выполняющее вход пользователей](scenario-web-app-sign-user-overview.md?tabs=java)
