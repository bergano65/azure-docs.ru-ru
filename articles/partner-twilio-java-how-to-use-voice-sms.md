---
title: Использование Twilio для поддержки голосовых вызовов и SMS (Java) | Документация Майкрософт
description: Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры кода написаны на Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 18e93ce18ed746612996399dc1aeb258abd26165
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637210"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Использование Twilio для поддержки голосовых возможностей и SMS в Java
В этом руководстве показано, как выполнять типовые задачи программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и SMS в приложениях см. в разделе [Дальнейшие действия](#NextSteps).

## <a id="WhatIs"></a>Что такое Twilio?
Twilio — это API веб-службы телефонии, позволяющий использовать существующие языки веб-программирования и навыки для создания приложений для работы с голосовыми вызовами и SMS. Twilio — это сторонняя служба (не компонент Azure и не продукт Майкрософт).

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Twilio Client** позволяет приложениям реализовать речевую связь с помощью существующих интернет-подключений, в том числе на мобильных устройствах.

## <a id="Pricing"></a>Цены и специальные предложения Twilio
Сведения о ценах на Twilio доступны по [цене Twilio][twilio_pricing]. Клиенты Azure получают [специальное предложение][special_offer]: бесплатный кредит 1000 текстов или 1000 входящих минут. Чтобы зарегистрироваться для получения этого предложения или получить дополнительные сведения, перейдите по [https://ahoy.twilio.com/azure][special_offer]адресу.

## <a id="Concepts"></a>Основные понятия
Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на нескольких языках; список см. в разделе [библиотеки API Twilio][twilio_libraries].

Основные аспекты Twilio API: команды Twilio и язык разметки Twilio (TwiML).

### <a id="Verbs"></a>Команды Twilio
В API используются команды Twilio: например, команда **&lt;Say&gt;** указывает Twilio, что необходимо воспроизвести сообщение в случае вызова.

Ниже приведен список команд Twilio.

* **&lt;Dial&gt;** : подключение вызывающего абонента к другому телефону.
* **&lt;Gather&gt;** : сбор цифр, введенных на клавиатуре телефона.
* **&lt;Hangup&gt;** : окончание вызова.
* **&lt;Play&gt;** : воспроизведение звукового файла.
* **&lt;Queue&gt;** : добавление абонентов в очередь.
* **&lt;Pause&gt;** : бесшумное ожидание в течение указанного времени (в секундах).
* **&lt;Record&gt;** : запись голоса вызывающего абонента и возвращение URL-адреса файла, содержащего запись.
* **&lt;Redirect&gt;** : передача управления для вызова или SMS в TwiML по другому URL-адресу.
* **&lt;Reject&gt;** : отклонение входящего вызова на ваш номер Twilio без выставления счета.
* **&lt;Say&gt;** : преобразование текста в речь при вызове.
* **&lt;Sms&gt;** : отправка SMS-сообщения.

### <a id="TwiML"></a>TwiML
TwiML — это набор инструкций на основе XML и с использованием команд Twilio, которые сообщают службе Twilio, как необходимо обработать вызов или SMS.

Например, следующие инструкции TwiML преобразуют текст **Hello World** в речь.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Когда приложение вызывает Twilio API, одним из параметров API является URL-адрес, который возвращает ответ TwiML. Для целей разработки можно использовать URL-адреса из Twilio для предоставления ответов TwiML, используемых приложениями. Также может разместить свои собственные URL-адреса для получения ответов TwiML; другой вариант — использовать объект **TwiMLResponse** .

Дополнительные сведения о командах Twilio, их атрибутах и TwiML см. в разделе [TwiML][twiml]. Дополнительные сведения об API Twilio см. в разделе [API Twilio][twilio_api].

## <a id="CreateAccount"></a>Создание учетной записи Twilio
Когда вы будете готовы получить учетную запись Twilio, зарегистрируйтесь по адресу [try Twilio][try_twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

При регистрации учетной записи Twilio, вы получите идентификатор учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Идентификатор учетной записи и маркер проверки подлинности доступны для просмотра в [консоли Twilio][twilio_console]в ПОЛЯХ **ИД безопасности учетной записи** и **токен проверки**подлинности соответственно.

## <a id="create_app"></a>Создание приложения Java
1. Получите JAR-файл Twilio и добавьте его к пути построения Java и к WAR-сборке. В [https://github.com/twilio/twilio-java][twilio_java]можно скачать источники GitHub и создать собственный JAR-файл или скачать предварительно собранный JAR (с зависимостями или без них).
2. Убедитесь, что в хранилище ключей **cacerts** JDK содержится сертификат Equifax Secure Certificate Authority с отпечатком MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (серийный номер 35:DE:F4:CF, отпечаток SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Это сертификат центра сертификации (ЦС) для [https://api.twilio.com][twilio_api_service] службы, который вызывается при использовании API Twilio. Сведения о том, как убедиться, что хранилище ключей **CACERTS** JDK содержит правильный сертификат ЦС, см. в разделе [Добавление сертификата в хранилище сертификатов ЦС Java][add_ca_cert].

Подробные инструкции по использованию клиентской библиотеки Twilio для Java см. в статье [как позвонить с помощью Twilio в приложении Java в Azure][howto_phonecall_java].

## <a id="configure_app"></a>Настройка приложения для использования библиотек Twilio
В коде можно добавить операторы **импорта** в верхней части исходных файлов для пакетов или классов Twilio, которые требуется использовать в приложении.

Для исходных файлов Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Для исходных файлов Java Server Page (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Операторы **импорта** могут отличаться в зависимости от того, какие пакеты или классы Twilio необходимо использовать.

## <a id="howto_make_call"></a>Практическое руководство. Осуществление исходящего вызова
Далее показано, как осуществлять исходящий вызов с использованием класса **Call**. Этот код также использует сайт из Twilio для выдачи ответа на языке разметки Twilio (TwiML). Замените значения телефонных номеров **From** (От) и **To** (Кому) и проверьте номер телефона **From** (От) для учетной записи Twilio до выполнения кода.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Дополнительные сведения о параметрах, передаваемых методу **Call. Creator** , см. [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]в разделе.

Как уже упоминалось, этот код также использует сайт из Twilio для выдачи ответа на языке TwiML. Можно использовать собственный веб-сайт для предоставления ответа TwiML; дополнительные сведения содержатся в разделе [Предоставление ответов TwiML в Java-приложении на платформе Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Практическое руководство. Отправка SMS-сообщения
Ниже показано, как отправить SMS-сообщение с использованием класса **Message**. С целью отправки SMS-сообщений для пробных учетных записей номер **From** (От), **4155992671**, предоставляется Twilio. Номер **To** (Кому) для учетной записи Twilio необходимо проверить перед выполнением кода.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Дополнительные сведения о параметрах, передаваемых методу **Message. Creator** , см. [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms]в разделе.

## <a id="howto_provide_twiml_responses"></a>Практическое руководство. Предоставление откликов TwiML с вашего веб-сайта
Когда приложение инициирует вызов API Twilio — например, с использованием метода **CallCreator.create** — Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. В приведенном выше примере используется URL-адрес [https://twimlets.com/message][twimlet_message_url], предоставленный Twilio. (Хотя TwiML предназначается для использования веб-службами, TwiML можно также просмотреть в браузере. Например, щелкните [https://twimlets.com/message][twimlet_message_url] , чтобы просмотреть [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] пустой **&lt;&gt;** **элементответа;вкачестведругогопримеращелкните,чтобыпросмотретьэлементответа,содержащийэлемент&lt;&gt;** **&lt; Скажем&gt;** , element.)

Вместо того чтобы использовать URL-адрес, предоставленный Twilio, можно создать собственный URL-адрес для возврата HTTP-ответов. Веб-сайт можно создавать на любом языке, который возвращает HTTP-ответы; в этом разделе предполагается, что URL-адрес будет размещаться на странице JSP.

Следующая страница JSP создает ответ TwiML **Hello World** на вызов.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Следующая страница JSP приведет к появлению ответа TwiML, в котором произносится определенный текст, присутствуют несколько пауз, а также сообщаются сведения о версии Twilio API и имя роли Azure.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

Параметр **ApiVersion** доступен в голосовых запросах Twilio (но не в SMS-запросах). Доступные параметры запроса для голосовых и SMS-запросов Twilio см. в статьях <https://www.twilio.com/docs/api/twiml/twilio_request> и <https://www.twilio.com/docs/api/twiml/sms/twilio_request> соответственно. Переменная среды **RoleName** доступна как часть развертывания Azure. (Если вы хотите добавить пользовательские переменные среды, чтобы они могли быть получены из **System. getenv**, см. раздел переменные среды в разделе [Прочие параметры конфигурации роли][misc_role_config_settings].)

После настройки JSP-страницы на предоставление ответов TwiML используйте URL страницы JSP как URL, передаваемый в метод **Call.creator**. Например, если у вас есть веб-приложение с именем MyTwiML, развернутое в размещенной службе Azure, а JSP-страница использует имя mytwiml.jsp, URL-адрес может быть передан в **Call.creator**, как показано далее.

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Другой вариант ответа TwiML — через класс **VoiceResponse**, который доступен в пакете **com.twilio.twiml**.

Дополнительные сведения об использовании Twilio в Azure с Java см. в статье [Создание телефонного звонка с помощью Twilio в приложении Java в Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Практическое руководство. Использование дополнительных служб Twilio
В дополнение к приведенным примерам, Twilio предлагает веб-интерфейсы API для использования дополнительных функций Twilio в вашем приложении Azure. Полные сведения см. в [документации по API Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Дальнейшие действия
Вы узнали основные сведения о службе Twilio. Для получения дополнительных сведений используйте следующие ссылки.

* [Рекомендации по безопасности Twilio][twilio_security_guidelines]
* [Twilio и пример кода][twilio_howtos]
* [Краткие руководства по Twilio][twilio_quickstarts]
* [Twilio на GitHub][twilio_on_github]
* [Обратитесь в службу поддержки Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
