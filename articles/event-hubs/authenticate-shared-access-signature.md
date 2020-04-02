---
title: Authenticate доступ к концентрам событий Azure с общими подписями доступа
description: В этой статье показано, как проверить подлинность доступа к ресурсам Event Hubs с помощью общих подписей доступа.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: 82773ab6decfe15ee1a9d839a1f10a158ae72c42
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521322"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Аутентифицировать доступ к ресурсам event Hubs с помощью общих подписей доступа (SAS)
Общая подпись доступа (SAS) дает вам детальный контроль над типом доступа, который предоставляется клиентам, имеющим общую подпись доступа. Вот некоторые элементы управления, которые можно установить в SAS: 

- Интервал, в течение которого sAS действителен, включая время начала и время истечения срока действия.
- Разрешения, предоставляемые с помощью SAS. Например, SAS для пространства имен Event Hubs может предоставить разрешение на прослушивание, но не разрешение на отправку.
- Только клиенты, предоставляющие действительные учетные данные, могут передать данные в концентратор событий.
- Клиент не может выдавать себя за другого клиента.
- Клиент rouge может быть заблокирован от отправки данных в концентратор событий.

Эта статья охватывает проверку подлинности доступа к ресурсам Event Hubs с помощью SAS. Чтобы узнать об **авторизации** доступа к ресурсам Event Hubs с помощью SAS, смотрите [эту статью](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Корпорация Майкрософт рекомендует использовать по возможности учетные данные Azure AD в качестве наилучшей практики безопасности, а не использовать общие подписи доступа, которые могут быть более легко скомпрометированы. В то время как вы можете продолжать использовать подписи общего доступа (SAS) для предоставления мелкозернистый доступ к ресурсам концентраторов событий, Azure AD предлагает аналогичные возможности без необходимости управлять токенами SAS или беспокоиться об отзыве скомпрометированного SAS.
> 
> Для получения дополнительной информации об интеграции Azure [Authorize access to Event Hubs using Azure AD](authorize-access-azure-active-directory.md)AD в концентрах событий Azure см. 


## <a name="configuring-for-sas-authentication"></a>Настройка для проверки подлинности SAS
Можно настроить правило авторизации доступа EventHubs в пространстве имен концентратора событий или объект (экземпляр концентратора событий или тема Kafka в концентраторе событий). Настройка правила авторизации общего доступа в группе потребителей в настоящее время не поддерживается, но можно использовать правила, настроенные на пространство имен или сущность, чтобы обеспечить доступ к группе потребителей. 

На следующем изображении показано, как правила авторизации применяются к образу сущностей. 

![Правило настройки авторизации](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

В этом примере в пространстве имен центров событий (ExampleNamespace) есть два сущности: eh1 и topic1. Правила авторизации определяются как на уровне сущности, так и на уровне пространства имен.  

Правила авторизации и прослушивания ruleNS и прослушиваниябудут применяются как к экземпляру концентратора событий eh1, так и к теме t1. Правила авторизации listenRule-eh и sendRule-eh применяются только к экземпляру концентратора событий eh1 и правилу авторизации sendRuleT применяется только к теме topic1. 

При использовании правила авторизации sendRuleNS клиентские приложения могут отправляться как на eh1, так и на topic1. При использовании правила авторизации sendRuleT обеспечивает сярточное доступ только к topic1, и, следовательно, клиентские приложения, использующие это правило для доступа, теперь не могут отправить eh1, но только по теме1.

## <a name="generate-a-shared-access-signature-token"></a>Создание маркера подписанного URL-адреса 
Каждый клиент, который имеет доступ к имени правила авторизации и одному из его ключей подписи, может создать маркер SAS. Маркер создается путем составления строки в следующем формате:

- `se`- Момент истечения срока действия токенов. Секунды отражения с момента эпохи 00:00:00 UTC 1 января 1970 года (эпоха UNIX), когда срок действия токена истекает
- `skn`- Название правила авторизации, то есть ключевое имя SAS.
- `sr`– URI доступа к ресурсу.
- `sig`- Подпись.

Строка подписи — это хэш SHA-256, вычисленный на ресурс URI (область, описанная в предыдущем разделе) и репрезентативное представление момента истечения срока действия токенов, разделенное CRLF.

Вычисление хэша напоминает следующий псевдокод и возвращает 256-битное (32-байтное) значение хэша. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Маркер содержит нехэшируемые значения, что позволяет получателю повторно вычислить хэш с теми же параметрами и проверить, что издатель использует допустимый ключ подписи.

URI ресурса — это полный URI ресурса служебной шины, к которому запрашивается доступ. Например,<namespace>http://<entityPath> .servicebus.windows.net/ `sb://<namespace>.servicebus.windows.net/<entityPath>;` или `http://contoso.servicebus.windows.net/eventhubs/eh1`то есть, .

URI должен быть закодирован с помощью знака процента.

Правило авторизации общего доступа, используемое для подписи, необходимо настроить для сущности, указанной данным URI или одной из ее иерархических родительских сущностей. Например, в предыдущем примере это — `http://contoso.servicebus.windows.net/eventhubs/eh1` или `http://contoso.servicebus.windows.net`.

Токен SAS действителен для всех ресурсов, закрепленных <resourceURI> в строке подписи.

> [!NOTE]
> Вы создаете маркер доступа для концентров событий с помощью общей политики доступа. Для получения дополнительной информации смотрите [политику авторизации общего доступа.](authorize-access-shared-access-signature.md#shared-access-authorization-policies)

### <a name="generating-a-signaturetoken-from-a-policy"></a>Создание подписи (токен) из политики 
Ниже показано, что создается токен SAS с использованием политик общей подписи доступа,

#### <a name="nodejs"></a>Node.js

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Проверка подлинности издателей событий ныхцентров с помощью SAS 
Издатель событий определяет виртуальную конечную точку для концентратора событий. Издатель может использоваться только для отправки сообщений в концентратор событий и не получать сообщения.

Как правило, концентратор событий использует один издатель на клиент. Все сообщения, отправленные любому из издателей концентратора событий, добавляются в очередь этого концентратора событий. Издатели обеспечивают мелкозернистый контроль доступа.

Каждому клиенту Центров событий назначается уникальный маркер, который передается в клиент. Токены производятся таким образом, что каждый уникальный токен предоставляет доступ к различным уникальным издателям. Клиент, владеющий токеном, может отправить только одному издателю и другому издателю. Если несколько клиентов имеют один и тот же маркер, то каждый из них разделяет издателя.

Все токены присваиваются ключами SAS. Как правило, все маркеры должны быть подписаны тем же ключом. Клиенты не знают о ключе, который мешает клиентам производить токены. Клиенты работают на тех же токенах до истечения срока их действия.

Например, для определения правил авторизации, применяемых только к отправке/публикации в концентраторах событий, необходимо определить правило авторизации отправки. Это может быть сделано на уровне пространства имен или дать более детальный объем для конкретной сущности (экземпляр концентратов событий или темы). Называется клиент или приложение, которое имеет такой гранулированный доступ, издатель Event Hubs. Для этого выполните следующие действия.

1. Создайте ключ SAS на объекте, который вы хотите опубликовать, чтобы назначить область **отправки** на нем. Для получения дополнительной информации смотрите [политики авторизации общего доступа.](authorize-access-shared-access-signature.md#shared-access-authorization-policies)
2. Создайте токен SAS со сроком действия конкретного издателя, используя ключ, созданный в шаге1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Предоставьте токен клиенту издателя, который может отправлять только сущности и издателю, к которому токен предоставляет доступ.

    После истечения срока действия токена клиент теряет доступ к отправке/публикации объекту. 


> [!NOTE]
> Хотя это и не рекомендуется, можно оснастить устройства токенами, которые предоставляют доступ к концентратору событий или пространству имен. Любое устройство, которое содержит этот маркер, может отправлять сообщения непосредственно в концентратор событий. Кроме того, устройство нельзя внести в черный список, чтобы запретить отправку данных в концентратор событий.
> 
> Всегда рекомендуется давать конкретные и детализированные области.

> [!IMPORTANT]
> После создания маркеров каждому клиенту присваивается собственный уникальный маркер.
>
> Когда клиент отправляет данные в концентратор событий, он отмечает свой запрос с токеном. Во избежание перехвата и кражи маркера злоумышленником связь между клиентом и концентратором событий должна выполняться по зашифрованному каналу.
> 
> Если маркер украден злоумышленником, злоумышленник может действовать от имени клиента, маркер которого был украден. Черный список издателя делает его клиентоне непригодным для использования до тех пор, пока он не получит новый маркер, в ключа которым используется другой издатель.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Проверка подлинности потребителей концентраторов событий с помощью SAS 
Для проверки подлинности бэк-энд-приложений, потребляемых из данных, генерируемых производителями Event Hubs, аутенконная проверка маркеров Event Hubs требует от своих клиентов либо иметь права **управления,** либо привилегии **прослушивания,** присвоенные пространству имен Event Hubs или экземпляру центра событий или теме. Данные потребляются из концентраторов событий с использованием групп потребителей. В то время как политика SAS дает вам детальную область, эта область определяется только на уровне сущности, а не на уровне потребителя. Это означает, что привилегии, определенные на уровне пространства имен или экземпляре концентратора событий или уровне темы, будут применяться к группам потребителей этой сущности.

## <a name="next-steps"></a>Дальнейшие действия
См. следующие статьи:

- [Разрешить использование SAS](authenticate-shared-access-signature.md)
- [Разрешить использование управления доступом к ролевой базе (RBAC)](authenticate-shared-access-signature.md)
- [Узнайте больше о концентрах событий](event-hubs-about.md)

Смотрите следующие статьи:

- [Authenticate requests to Azure Event Hubs из приложения с использованием active-каталога Azure](authenticate-application.md)
- [Проверка управляемого итогового данных с помощью Active Directory Azure для доступа к ресурсам концентратов событий](authenticate-managed-identity.md)
- [Разрешить доступ к ресурсам концентраторов событий с помощью активного каталога Azure](authorize-access-azure-active-directory.md)
- [Разрешить доступ к ресурсам концентраторов событий с помощью общих подписей доступа](authorize-access-shared-access-signature.md)