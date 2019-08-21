---
title: Телефонные звонки из Twilio (PHP) | Документация Майкрософт
description: Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры, для приложения PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637327"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Как в Azure выполнить телефонный звонок с помощью Twilio в PHP-приложении
В следующем примере показано, как выполнить звонок с веб-страницы PHP, размещенной в Azure, с помощью службы Twilio. Полученное приложение запрашивает у пользователя значения телефонного звонка, как показано на следующем снимке экрана.

![Форма звонка Azure с использованием службы Twilio и PHP][twilio_php]

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. Получите учетную запись Twilio и маркер проверки подлинности из [консоли Twilio][twilio_console]. Чтобы приступить к работе с Twilio, оцените [https://www.twilio.com/pricing][twilio_pricing]цены по адресу. Вы можете зарегистрироваться для получения пробной учетной [https://www.twilio.com/try-twilio][try_twilio]записи по адресу.
2. Получите [библиотеку Twilio для PHP](https://github.com/twilio/twilio-php) или установите ее в виде пакета PEAR. Дополнительные сведения см. в [файле сведений](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Установите Azure SDK для PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Создание веб-формы для выполнения звонка
В следующем HTML-коде показано, как построить веб-страницу (**callform.html**), позволяющую извлечь данные пользователя для выполнения звонка:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Создание кода для выполнения звонка
Следующий код призван показать процесс создания страницы **makecall.php**, которая вызывается, когда пользователь отправляет форму с **callform.html**. Показанный ниже код создает сообщение звонка и выполняет его. Кроме того, обязательно используйте учетную запись Twilio и токен проверки подлинности из [консоли Twilio][twilio_console] , а не значения заполнителя, назначенные **$SID** и **$Token** в приведенном ниже коде.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Помимо выполнения вызова, на странице **makecall.php** отображаются некоторые метаданные о нем (см. изображение ниже). Дополнительные сведения о метаданных вызовов см. в [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]разделе.

![Ответ на звонок Azure с использованием службы Twilio и PHP][twilio_php_response]

## <a name="run-the-application"></a>Выполнение приложения
Следующим шагом является [развертывание приложения в веб-приложениях Azure с Git](app-service/app-service-web-get-started-php.md) (хотя не все сведения в статье по ссылке применимы). 

## <a name="next-steps"></a>Следующие шаги
Данный код демонстрирует базовую функциональность, доступную через PHP-библиотеку Twillio в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Пример:

* Вместо использования веб-формы для хранения телефонных номеров и текста вызова можно применить большие двоичные объекты хранилища Azure или SQL Database. Сведения об использовании больших двоичных объектов службы хранилища Azure в PHP см. в статье [Использование службы хранилища Azure с приложениями PHP][howto_blob_storage_php]. Сведения об использовании базы данных SQL в PHP см. в статье [использование базы данных SQL с приложениями PHP][howto_sql_azure_php].
* В коде **MAKECALL. php** используется предоставленный Twilio URL-[https://twimlets.com/message][twimlet_message_url]адрес () для предоставления ответа на язык разметки Twilio (TwiML), который информирует Twilio о том, как продолжить вызов. Например, возвращаемый ответ TwiML может содержать команду `<Say>` , которая задает голосовое воспроизведение текста вызываемому абоненту. Вместо использования URL-адреса, предоставленного Twilio, можно создать собственную службу для реагирования на запрос Twilio; Дополнительные сведения см. в статье [Использование Twilio для работы с голосовыми функциями и SMS в PHP][howto_twilio_voice_sms_php]. Дополнительные сведения о TwiML можно найти по адресу [https://www.twilio.com/docs/api/twiml][twiml], а дополнительные сведения о `<Say>` и других командах Twilio можно найти по [https://www.twilio.com/docs/api/twiml/say][twilio_say]адресу.
* Ознакомьтесь с рекомендациями по безопасности [https://www.twilio.com/docs/security][twilio_docs_security]Twilio по адресу.

Дополнительные сведения о Twilio см. в [https://www.twilio.com/docs][twilio_docs]разделе.

## <a name="see-also"></a>См. также
* [Использование Twilio для поддержки голосовых возможностей и SMS в PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
