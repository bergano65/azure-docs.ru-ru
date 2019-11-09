---
title: Телефонные звонки из Twilio (Java) | Документация Майкрософт
description: Узнайте, как выполнить телефонный звонок с веб-страницы с использованием Twilio в приложении Java в Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838557"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Осуществление телефонных звонков с использованием Twilio в Java-приложении Azure
В следующем примере показано, как выполнить звонок с веб-страницы, размещенной в Azure, с помощью службы Twilio. Полученное приложение запрашивает у пользователя значения телефонного звонка, как показано на следующем снимке экрана.

![Форма вызова Azure с помощью Twilio и Java][twilio_java]

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. Получение учетной записи Twilio и токена подтверждения подлинности. Чтобы приступить к работе с Twilio, оцените цены на [https://www.twilio.com/pricing][twilio_pricing]. Вы можете зарегистрироваться по адресу [https://www.twilio.com/try-twilio][try_twilio]. Дополнительные сведения об API, предоставляемом Twilio, см. в разделе [https://www.twilio.com/api][twilio_api].
2. Получите JAR-файл Twilio. На [https://github.com/twilio/twilio-java][twilio_java_github]можно скачать источники GitHub и создать свой собственный JAR-файл или скачать предварительно собранный JAR (с зависимостями или без них).
   В этом разделе приводится код, написанный с использованием стандартного JAR-файла TwilioJava-3.3.8-with-dependencies.
3. Добавьте JAR-файл в путь построения Java.
4. Если вы используете Eclipse для создания Java-приложения, включите JAR-файл Twilio в файл развертывания приложения (WAR) с помощью компонента сборки развертывания Eclipse. Если среда Eclipse не используется, включите JAR-файл Twilio в ту же роль Azure, которой принадлежит ваше Java-приложение, после чего добавьте в приложение путь к классу.
5. Убедитесь, что в хранилище ключей cacerts содержится сертификат Equifax Secure Certificate Authority с отпечатком MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (серийный номер 35:DE:F4:CF, отпечаток SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Это сертификат центра сертификации (ЦС) для службы [https://api.twilio.com][twilio_api_service] , который вызывается при использовании интерфейсов API Twilio. Сведения о добавлении этого сертификата ЦС в хранилище cacert JDK см. в статье [Добавление сертификата в хранилище сертификатов ЦС Java][add_ca_cert].

Кроме того, вы также знакомы со сведениями о [создании Hello Worldного приложения с помощью Azure Toolkit for Eclipse][azure_java_eclipse_hello_world], а также с другими методами размещения приложений Java в Azure, если вы не используете Eclipse, настоятельно рекомендуем.

## <a name="create-a-web-form-for-making-a-call"></a>Создание веб-формы для выполнения звонка
В следующем коде показано, как создать веб-форму, позволяющую извлечь данные пользователя для выполнения звонка. В этом примере создается динамический веб-проект **TwilioCloud**, в который в качестве JSP-файла добавляется файл **callform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Создание кода для выполнения звонка
Следующий код вызывается после заполнения формы пользователем, отображаемой файлом callform.jsp, создает сообщение звонка, а затем сам звонок. В этом примере JSP-файлу присваивается имя **makecall.jsp**, после чего он добавляется в проект **TwilioCloud**. (Вместо заполнителей **accountSID** и **authToken** в приведенном ниже коде следует указать вашу учетную запись Twilio и маркер проверки подлинности.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Помимо звонка makecall.jsp отображает конечную точку Twilio, версию API-интерфейса и сведения о состоянии звонка. Пример приведен на следующем снимке экрана:

![Ответ на звонок Azure с помощью Twilio и Java][twilio_java_response]

## <a name="run-the-application"></a>Выполнение приложения
Ниже приведены общие действия для запуска приложения. подробные сведения об этих действиях можно найти в разделе [создание Hello World приложения с помощью Azure Toolkit for Eclipse][azure_java_eclipse_hello_world].

1. Экспортируйте WAR-файл TwilioCloud в папку **approot** Azure. 
2. Измените файл **startup.cmd** , чтобы распаковать WAR-файл TwilioCloud.
3. Выполните компиляцию приложения для эмулятора среды выполнения приложений.
4. Запустите развертывание в эмуляторе среды выполнения приложений.
5. Откройте браузер и запустите `http://localhost:8080/TwilioCloud/callform.jsp`.
6. Введите в форму значения, нажмите **Выполнить звонок**и просмотрите результаты в файле makecall.jsp.

Завершив подготовку к развертыванию в Azure, выполните повторную компиляцию для развертывания в облаке, далее выполните развертывание в Azure и запустите http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp в браузере (замените атрибут *именем_вашего_размещенного_приложения*).

## <a name="next-steps"></a>Дальнейшие действия
В этом коде демонстрируются базовые функциональные возможности службы Twilio в Java в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например:

* Вместо использования веб-формы для хранения телефонных номеров и текста вызова можно применить большие двоичные объекты хранилища Azure или SQL Database. Дополнительные сведения об использовании больших двоичных объектов службы хранилища Azure в Java см. в разделе [Как использовать службу хранилища BLOB-объектов из Java][howto_blob_storage_java]. 
* Вы можете использовать **RoleEnvironment.getConfigurationSettings** для получения идентификатора учетной записи Twilio и маркера проверки подлинности из параметров конфигурации вашего развертывания вместо того, чтобы жестко программировать значения в файле makecall.jsp. Сведения о классе **RoleEnvironment** см. в разделе [Использование библиотеки среды выполнения службы Azure в JSP][azure_runtime_jsp].
* Код MAKECALL. jsp назначает URL-адресу, предоставленному Twilio, [https://twimlets.com/message][twimlet_message_url], переменной **URL-адреса** . Этот URL-адрес представляет собой ответ в формате языка разметки Twilio (TwiML), содержащий инструкции по обработке звонка в Twilio. Например, возвращаемый ответ TwiML может содержать команду **&lt;Say&gt;** , которая задает голосовое воспроизведение текста вызываемому абоненту. Вместо использования URL-адреса, предоставленного Twilio, можно создать собственную службу для реагирования на запрос Twilio; Дополнительные сведения см. [в статье Использование Twilio для работы с голосовыми функциями и SMS в Java][howto_twilio_voice_sms_java]. Дополнительные сведения о TwiML можно найти по адресу [https://www.twilio.com/docs/api/twiml][twiml], а дополнительные сведения о **&lt;, скажем&gt;** и других командах Twilio, можно найти на [https://www.twilio.com/docs/api/twiml/say][twilio_say].
* Ознакомьтесь с рекомендациями по безопасности Twilio на [https://www.twilio.com/docs/security][twilio_docs_security].

Дополнительные сведения о Twilio см. в разделе [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>См. также
* [Использование Twilio для поддержки голоса и SMS в Java][howto_twilio_voice_sms_java]
* [Добавление сертификата в хранилище сертификатов ЦС Java][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
