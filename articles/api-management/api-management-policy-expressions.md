---
title: Выражения политики в службе управления API Azure | Документация Майкрософт
description: Сведения о выражениях политики в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: fa5e84ba62896969458b84cf014e2b35ee869df7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072177"
---
# <a name="api-management-policy-expressions"></a>Выражения политики в службе управления API
В этой статье описывается синтаксис выражений политики C# 7. У каждого выражения есть доступ к неявно заданной переменной [контекста](api-management-policy-expressions.md#ContextVariables) и разрешенному [подмножеству](api-management-policy-expressions.md#CLRTypes) типов .NET Framework.

Дополнительные сведения

- Способы передачи сведений о контексте во внутреннюю службу. Используйте политики [настройки параметра строки запроса](api-management-transformation-policies.md#SetQueryStringParameter) и [настройки HTTP-заголовка](api-management-transformation-policies.md#SetHTTPheader), чтобы передать эти сведения.
- Использование политики [проверки JWT](api-management-access-restriction-policies.md#ValidateJWT) для предварительной авторизации доступа к операциям на основе утверждений маркеров.
- Сведения об использовании трассировки с помощью [инспектора API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) для оценки политик, а также результаты этой оценки.
- Использование выражений с политиками [получения из кэша](api-management-caching-policies.md#GetFromCache) и [хранения в кэше](api-management-caching-policies.md#StoreToCache) для настройки кэширования ответа службы управления API. Установите такую же длительность, как и для кэширования ответа во внутренней службе, которая задается директивой `Cache-Control` на сервере.
- Сведения о том, как отфильтровать содержимое. Удалите элементы данных из ответа, полученного из внутренней службы с помощью политик [потока управления](api-management-advanced-policies.md#choose) и [задания текста](api-management-transformation-policies.md#SetBody).
- Чтобы скачать инструкции политики, перейдите в репозиторий GitHub [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).


## <a name="Syntax"></a> Синтаксис
Выражения с одним оператором заключаются в элементы `@(expression)`, где `expression` — оператор выражения C# правильного формата.

Выражения с несколькими операторами заключаются в элементы `@{expression}`. Все ветви кода в выражениях с несколькими операторами должны заканчиваться оператором `return`.

## <a name="PolicyExpressionsExamples"></a> Примеры

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));
  }
  else
  {
    return null;
  }
}
```

## <a name="PolicyExpressionsUsage"></a>Использование
Выражения можно использовать в качестве значений атрибутов или текстовых значений в любой [политике](api-management-policies.md) службы управления API, если в справочнике по политике не указано иное.

> [!IMPORTANT]
> При использовании выражений политики выполняется только ограниченная проверка выражений политики во время ее определения. Выражения выполняет шлюз во время выполнения. Все исключения, создаваемые выражениями политики, приводят к ошибке среды выполнения.

## <a name="CLRTypes"></a> Типы .NET Framework, допустимые в выражениях политики
В следующей таблице перечислены типы .NET Framework и их члены, допустимые в выражениях политики.

|Type|Поддерживаемые члены|
|--------------|-----------------------|
|Newtonsoft. JSON. форматирование|Все|
|Newtonsoft. JSON. JsonConvert|Для SerializeObject, методов DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Все|
|Newtonsoft.Json.Linq.JArray|Все|
|Newtonsoft.Json.Linq.JConstructor|Все|
|Newtonsoft.Json.Linq.JContainer|Все|
|Newtonsoft.Json.Linq.JObject|Все|
|Newtonsoft.Json.Linq.JProperty|Все|
|Newtonsoft.Json.Linq.JRaw|Все|
|Newtonsoft.Json.Linq.JToken|Все|
|Newtonsoft.Json.Linq.JTokenType|Все|
|Newtonsoft.Json.Linq.JValue|Все|
|System.Array|Все|
|System. BitConverter|Все|
|System.Boolean|Все|
|System.Byte|Все|
|System.Char|Все|
|System. Collections. Generic. Dictionary < TKey, TValue >|Все|
|System. Collections. Generic. hashing\<T >|Все|
|System. Collections. Generic.\<ICollection T >|Все|
|System. Collections. Generic. IDictionary < TKey, TValue >|Все|
|System. Collections. Generic.\<IEnumerable T >|Все|
|System. Collections. Generic.\<IEnumerator T >|Все|
|System. Collections. Generic.\<IList T >|Все|
|System. Collections. Generic.\<IReadOnlyCollection T >|Все|
|System. Collections. Generic. IReadOnlyDictionary < TKey, TValue >|Все|
|System. Collections. Generic.\<ISET T >|Все|
|System. Collections. Generic. KeyValuePair < TKey, TValue >|Все|
|System. Collections. Generic.\<List T >|Все|
|System. Collections. Generic.\<Queue T >|Все|
|> System. Collections. Generic\<. Stack T|Все|
|System.Convert|Все|
|System.DateTime|(Конструктор), Add, AddDays, AddHours, Аддмиллисекондс, Аддминутес, Аддмонсс, Аддсекондс, Аддтиккс, Аддеарс, Дата, день, DayOfWeek, DayOfYear, Дайсинмонс, Hour, Исдайлигхтсавингтиме, IsLeapYear, MaxValue, миллисекунда, минута, MinValue, month, Now , Синтаксический анализ, второй, вычитание, такты, TimeOfDay, Today, ToString, UtcNow, Year|
|System.DateTimeKind|Время в формате UTC|
|System.DateTimeOffset|Все|
|System.Decimal|Все|
|System.Double|Все|
|System. Exception|Все|
|System.Guid|Все|
|System.Int16|Все|
|System.Int32|Все|
|System.Int64|Все|
|System. IO. StringReader|Все|
|System. IO. Стрингвритер|Все|
|System. LINQ. Enumerable|Все|
|System.Math|Все|
|System.MidpointRounding|Все|
|System.Net.WebUtility|Все|
|System. Nullable|Все|
|System.Random|Все|
|System.SByte|Все|
|System. Security. Cryptography. AsymmetricAlgorithm|Все|
|System. Security. Cryptography. CipherMode|Все|
|System.Security.Cryptography.HashAlgorithm|Все|
|System. Security. Cryptography. Хашалгорисмнаме|Все|
|System.Security.Cryptography.HMAC|Все|
|System.Security.Cryptography.HMACMD5|Все|
|System.Security.Cryptography.HMACSHA1|Все|
|System.Security.Cryptography.HMACSHA256|Все|
|System. Security. Cryptography. HMACSHA384|Все|
|System. Security. Cryptography. HMACSHA512|Все|
|System.Security.Cryptography.KeyedHashAlgorithm|Все|
|System.Security.Cryptography.MD5|Все|
|System. Security. Cryptography. OID|Все|
|System. Security. Cryptography. PaddingMode|Все|
|System.Security.Cryptography.RNGCryptoServiceProvider|Все|
|System. Security. Cryptography. RSA|Все|
|System. Security. Cryptography. Рсаенкриптионпаддинг|Все|
|System. Security. Cryptography. Рсасигнатурепаддинг|Все|
|System.Security.Cryptography.SHA1|Все|
|System.Security.Cryptography.SHA1Managed|Все|
|System.Security.Cryptography.SHA256|Все|
|System.Security.Cryptography.SHA256Managed|Все|
|System.Security.Cryptography.SHA384|Все|
|System.Security.Cryptography.SHA384Managed|Все|
|System.Security.Cryptography.SHA512|Все|
|System.Security.Cryptography.SHA512Managed|Все|
|System. Security. Cryptography. SymmetricAlgorithm|Все|
|System. Security. Cryptography. X509Certificates. PublicKey|Все|
|System. Security. Cryptography. X509Certificates. Рсацертификатикстенсионс|Все|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Название|
|System. Security. Cryptography. X509Certificates. X509Certificate|Все|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Все|
|System. Security. Cryptography. X509Certificates. X509ContentType|Все|
|System. Security. Cryptography. X509Certificates. X509NameType|Все|
|System.Single|Все|
|System.String|Все|
|System. StringComparer|Все|
|System. StringComparison|Все|
|System.StringSplitOptions|Все|
|System.Text.Encoding|Все|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Конструктор), Match, Match, Matches, Replace, Unescape, разбиение|
|System.Text.RegularExpressions.RegexOptions|Все|
|System. Text. StringBuilder|Все|
|System.TimeSpan|Все|
|System. TimeZone|Все|
|System. TimeZoneInfo. массива AdjustmentRule|Все|
|System. TimeZoneInfo. Транситионтиме|Все|
|System. TimeZoneInfo|Все|
|System.Tuple|Все|
|System.UInt16|Все|
|System.UInt32|Все|
|System.UInt64|Все|
|System.Uri|Все|
|System. Урипартиал|Все|
|System.Xml.Linq.Extensions|Все|
|System.Xml.Linq.XAttribute|Все|
|System.Xml.Linq.XCData|Все|
|System.Xml.Linq.XComment|Все|
|System.Xml.Linq.XContainer|Все|
|System.Xml.Linq.XDeclaration|Все|
|System.Xml.Linq.XDocument|ALL, за исключением: Загрузить|
|System.Xml.Linq.XDocumentType|Все|
|System.Xml.Linq.XElement|Все|
|System.Xml.Linq.XName|Все|
|System.Xml.Linq.XNamespace|Все|
|System.Xml.Linq.XNode|Все|
|System.Xml.Linq.XNodeDocumentOrderComparer|Все|
|System.Xml.Linq.XNodeEqualityComparer|Все|
|System.Xml.Linq.XObject|Все|
|System.Xml.Linq.XProcessingInstruction|Все|
|System.Xml.Linq.XText|Все|
|System.Xml.XmlNodeType|Все|

## <a name="ContextVariables"></a> Переменная контекста
Переменная с именем `context` неявно доступна в каждом [выражении](api-management-policy-expressions.md#Syntax) политики. Ее члены предоставляют сведения, относящиеся к переменной `\request`. Все члены `context` доступны только для чтения.

|Переменная контекста|Допустимые методы, свойства и значения параметров|
|----------------------|-------------------------------------------------------|
|context|[API](#ref-context-api): [иапи](#ref-iapi)<br /><br /> [Развертывание](#ref-context-deployment)<br /><br /> Elapsed: TimeSpan — интервал времени между значением Timestamp и текущим временем<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Операция](#ref-context-operation)<br /><br /> [Продукт](#ref-context-product)<br /><br /> [Запрос](#ref-context-request)<br /><br /> RequestId: Guid — уникальный идентификатор запроса<br /><br /> [Ответ](#ref-context-response)<br /><br /> [Подписка](#ref-context-subscription)<br /><br /> Timestamp: DateTime — время получения запроса<br /><br /> Tracing: логическое значение — указывает, включена ли трассировка <br /><br /> [User](#ref-context-user)<br /><br /> [Переменные](#ref-context-variables). IReadOnlyDictionary<строка, объект><br /><br /> void Trace(message: строка)|
|<a id="ref-context-api"></a>локального. API|Id: строка<br /><br /> IsCurrentRevision: bool<br /><br />  Name: строка<br /><br /> Path: строка<br /><br /> Revision: строка<br /><br /> ServiceUrl: [иурл](#ref-iurl)<br /><br /> Version: строка |
|<a id="ref-context-deployment"></a>локального. Развертывания|Region: строка<br /><br /> ServiceName: строка<br /><br /> Certificates: IReadOnlyDictionary<строка, X509Certificate2>|
|<a id="ref-context-lasterror"></a>локального. LastError|Source: строка<br /><br /> Reason: строка<br /><br /> Message: строка<br /><br /> Scope: строка<br /><br /> Section: строка<br /><br /> Path: строка<br /><br /> PolicyId: строка<br /><br /> Дополнительные сведения о переменной context.LastError см. в разделе [Error handling](api-management-error-handling-policies.md) (Обработка ошибок).|
|<a id="ref-context-operation"></a>локального. Операцию|Id: строка<br /><br /> Method: строка<br /><br /> Name: строка<br /><br /> UrlTemplate: строка|
|<a id="ref-context-product"></a>локального. Продукта|Apis: IEnumerable <[иапи](#ref-iapi)\><br /><br /> ApprovalRequired: логическое значение<br /><br /> Groups: IEnumerable <[играуп](#ref-igroup)\><br /><br /> Id: строка<br /><br /> Name: строка<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: целое число?<br /><br /> SubscriptionRequired: логическое значение|
|<a id="ref-context-request"></a>локального. Получения|Текст: [Имессажебоди](#ref-imessagebody) или `null` , если запрос не имеет текста.<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Заголовки](#ref-context-request-headers): IReadOnlyDictionary<строка, строка[]><br /><br /> IpAddress: строка<br /><br /> MatchedParameters: IReadOnlyDictionary<строка, строка><br /><br /> Method: строка<br /><br /> Оригиналурл: [иурл](#ref-iurl)<br /><br /> Url: [иурл](#ref-iurl)|
|<a id="ref-context-request-headers"></a>контекст строки. Request. Headers. GetValueOrDefault (Хеадернаме: строка, defaultValue: строка)|headerName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения заголовков запросов, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|
|<a id="ref-context-response"></a>локального. Ответ|Текст: [имессажебоди](#ref-imessagebody)<br /><br /> [Заголовки](#ref-context-response-headers): IReadOnlyDictionary<строка, строка[]><br /><br /> StatusCode: целое число<br /><br /> StatusReason: строка|
|<a id="ref-context-response-headers"></a>контекст строки. Response. Headers. GetValueOrDefault (Хеадернаме: строка, defaultValue: String)|headerName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения заголовков ответов, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|
|<a id="ref-context-subscription"></a>локального. Подписчик|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: строка<br /><br /> Key: строка<br /><br /> Name: строка<br /><br /> PrimaryKey: строка<br /><br /> SecondaryKey: строка<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>локального. Нажат|Email: строка<br /><br /> FirstName: строка<br /><br /> Groups: IEnumerable <[играуп](#ref-igroup)\><br /><br /> Id: строка<br /><br /> Identities: IEnumerable <[иусеридентити](#ref-iuseridentity)\><br /><br /> LastName: строка<br /><br /> Note: строка<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>иапи|Id: строка<br /><br /> Name: строка<br /><br /> Path: строка<br /><br /> Protocols: IEnumerable<строка\><br /><br /> ServiceUrl: [иурл](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [исубскриптионкэйпараметернамес](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>играуп|Id: строка<br /><br /> Name: строка|
|<a id="ref-imessagebody"></a>имессажебоди|As<T\>(preserveContent: bool = false): Где T: String, Byte [], JObject, JToken, Жаррай, XNode, XElement, XDocument<br /><br /> Методы `context.Request.Body.As<T>` и `context.Response.Body.As<T>` используются для чтения текста сообщения запроса или ответа в одном из заданных форматов `T`. По умолчанию метод использует исходный поток текста сообщения и делает его недоступным после возвращения. Чтобы избежать этого, задайте для параметра `preserveContent` значение `true`, чтобы метод работал с копией потока текста. Перейдите по [этой](api-management-transformation-policies.md#SetBody) ссылке, чтобы увидеть пример.|
|<a id="ref-iurl"></a>иурл|Host: строка<br /><br /> Path: строка<br /><br /> Port: целое число<br /><br /> [Запрос.](#ref-iurl-query) IReadOnlyDictionary<строка, строка[]><br /><br /> QueryString: строка<br /><br /> Scheme: строка|
|<a id="ref-iuseridentity"></a>иусеридентити|Id: строка<br /><br /> Provider: строка|
|<a id="ref-isubscriptionkeyparameternames"></a>исубскриптионкэйпараметернамес|Header: строка<br /><br /> Query: строка|
|<a id="ref-iurl-query"></a>Строка Иурл. Query. GetValueOrDefault (queryParameterName: строка, defaultValue: String)|queryParameterName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает разделенные запятыми значения параметров запроса или значение `defaultValue`, если параметр не найден.|
|<a id="ref-context-variables"></a>T контекст. Variables. GetValueOrDefault <\>T (variablename: String, DefaultValue: T)|variableName: строка<br /><br /> defaultValue: T<br /><br /> Возвращает значение переменной, приведенное к типу `T` или `defaultValue`, если переменная не найдена.<br /><br /> Этот метод выдает исключение, если указанный тип не соответствует фактическому типу возвращаемой переменной.|
|BasicAuthCredentials AsBasic(input: this string)|input: строка<br /><br /> Если входной параметр содержит допустимое значение заголовка запроса авторизации для обычной проверки подлинности HTTP, метод возвращает объект типа `BasicAuthCredentials`; в противном случае метод возвращает значение NULL.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: строка<br /><br /> result: выходное значение BasicAuthCredentials<br /><br /> Если входной параметр содержит допустимое значение заголовка запроса авторизации для обычной проверки подлинности и HTTP, метод возвращает значение `true`, а параметр результата содержит значение типа `BasicAuthCredentials`. В противном случае метод возвращает значение `false`.|
|BasicAuthCredentials|Password: строка<br /><br /> UserId: строка|
|Jwt AsJwt(input: this string)|input: строка<br /><br /> Если входной параметр содержит допустимое значение маркера JWT, метод возвращает объект типа `Jwt`; в противном случае метод возвращает значение `null`.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: строка<br /><br /> result: выходное значение типа Jwt<br /><br /> Если входной параметр содержит допустимое значение маркера JWT, метод возвращает значение `true`, а параметр результата содержит значение типа `Jwt`; в противном случае метод возвращает значение `false`.|
|Jwt|Algorithm: строка<br /><br /> Audience: IEnumerable<строка\><br /><br /> Claims: IReadOnlyDictionary<строка, строка[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: строка<br /><br /> Issuer: строка<br /><br /> Иссуедат: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: строка<br /><br /> Type: строка|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения утверждений, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|
|byte[] Encrypt(input: this byte[], alg: строка, key:byte[], iv:byte[])|input — открытый текст, который нужно зашифровать<br /><br />alg — имя алгоритма симметричного шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input — открытый текст, который нужно зашифровать<br /><br />alg — алгоритм шифрования<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input — открытый текст, который нужно зашифровать<br /><br />alg — алгоритм шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Decrypt(input: this byte[], alg: строка, key:byte[], iv:byte[])|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — имя алгоритма симметричного шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — алгоритм шифрования<br /><br />Возвращает открытый текст.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — алгоритм шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст.|
|bool Верифиноревокатион (входные данные: Этот системный. Security. Cryptography. X509Certificates. X509Certificate2)|Выполняет проверку цепочки X. 509 без проверки состояния отзыва сертификата.<br /><br />input-Certificate, объект<br /><br />Возвращает `true` значение, если проверка прошла. `false` значение, если проверка завершается неудачно.|


## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).
