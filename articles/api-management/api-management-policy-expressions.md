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
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244008"
---
# <a name="api-management-policy-expressions"></a>Выражения политики в службе управления API
В этой статье рассматриваются синтаксисы выражений политики в C' 7. Каждому выражению предоставлен доступ к неявно заданной переменной [context](api-management-policy-expressions.md#ContextVariables) и допустимому [подмножеству](api-management-policy-expressions.md#CLRTypes) типов платформы .NET Framework.

Дополнительные сведения

- Способы передачи сведений о контексте во внутреннюю службу. Используйте политики [настройки параметра строки запроса](api-management-transformation-policies.md#SetQueryStringParameter) и [настройки HTTP-заголовка](api-management-transformation-policies.md#SetHTTPheader), чтобы передать эти сведения.
- Использование политики [проверки JWT](api-management-access-restriction-policies.md#ValidateJWT) для предварительной авторизации доступа к операциям на основе утверждений маркеров.
- Сведения об использовании трассировки с помощью [инспектора API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) для оценки политик, а также результаты этой оценки.
- Использование выражений с политиками [получения из кэша](api-management-caching-policies.md#GetFromCache) и [хранения в кэше](api-management-caching-policies.md#StoreToCache) для настройки кэширования ответа службы управления API. Установите такую же длительность, как и для кэширования ответа во внутренней службе, которая задается директивой `Cache-Control` на сервере.
- Сведения о том, как отфильтровать содержимое. Удалите элементы данных из ответа, полученного из внутренней службы с помощью политик [потока управления](api-management-advanced-policies.md#choose) и [задания текста](api-management-transformation-policies.md#SetBody).
- Чтобы загрузить заявления о [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) политике, см.


## <a name="syntax"></a><a name="Syntax"></a>Синтаксис
Выражения с одним оператором заключаются в элементы `@(expression)`, где `expression` — оператор выражения C# правильного формата.

Выражения с несколькими операторами заключаются в элементы `@{expression}`. Все ветви кода в выражениях с несколькими операторами должны заканчиваться оператором `return`.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>Примеры

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Использования
Выражения можно использовать в качестве значений атрибутов или текстовых значений в любой [политике](api-management-policies.md) службы управления API, если в справочнике по политике не указано иное.

> [!IMPORTANT]
> При использовании выражений политики выполняется только ограниченная проверка выражений политики во время ее определения. Выражения выполняет шлюз во время выполнения. Все исключения, создаваемые выражениями политики, приводят к ошибке среды выполнения.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> Типы .NET Framework, допустимые в выражениях политики
В следующей таблице перечислены типы .NET Framework и их члены, допустимые в выражениях политики.

|Тип|Поддерживаемые члены|
|--------------|-----------------------|
|Ньютонсофт.Json.Formatting|All|
|Ньютонсофт.Json.JsonПреобразованив|SerializeПредмет, DeserializeПредмет|
|Newtonsoft.Json.Linq.Extensions|All|
|Newtonsoft.Json.Linq.JArray|All|
|Newtonsoft.Json.Linq.JConstructor|All|
|Newtonsoft.Json.Linq.JContainer|All|
|Newtonsoft.Json.Linq.JObject|All|
|Newtonsoft.Json.Linq.JProperty|All|
|Newtonsoft.Json.Linq.JRaw|All|
|Newtonsoft.Json.Linq.JToken|All|
|Newtonsoft.Json.Linq.JTokenType|All|
|Newtonsoft.Json.Linq.JValue|All|
|System.Array|All|
|System.BitConverter|All|
|System.Boolean|All|
|System.Byte|All|
|System.Char|All|
|System.Collections.Generic.Dictionary<TKey, TValue>|All|
|System.Collections.Generic.HashSet\<T>|All|
|System.Collections.Generic.ICollection\<T>|All|
|System.Collections.Generic.IDictionary<TKey, TValue>|All|
|System.Collections.Generic.IEnumerable\<T>|All|
|System.Collections.Generic.IEnumerator\<T>|All|
|System.Collections.Generic.Ilist\<T>|All|
|System.Collections.Generic.IReadonlyCollection\<T>|All|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|All|
|System.Collections.Generic.Iset\<T>|All|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|All|
|System.Collections.Generic.List\<T>|All|
|System.Collections.Generic.Очередь\<T>|All|
|System.Collections.Generic.Stack\<T>|All|
|System.Convert|All|
|System.DateTime|(Конструктор), Добавить, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Дата, День, DayOfWeek, DayOfYear, DaysInMonth, час, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Месяц, Сейчас , Парс, Во-вторых, Вычитание, Клещи, TimeOfDay, Сегодня, ToString, UtcNow, Год|
|System.DateTimeKind|Время в формате UTC|
|System.DateTimeOffset|All|
|System.Decimal|All|
|System.Double|All|
|System.Exception|All|
|System.Guid|All|
|System.Int16|All|
|System.Int32|All|
|System.Int64|All|
|System.IO.StringReader|All|
|System.IO.StringWriter|All|
|System.Linq.Enumerable|All|
|System.Math|All|
|System.MidpointRounding|All|
|System.Net.WebUtility|All|
|System.Nullable|All|
|System.Random|All|
|System.SByte|All|
|System.Security.Cryptography.AsymmetricAlgorithm|All|
|System.Security.Cryptography.CipherMode|All|
|System.Security.Cryptography.HashAlgorithm|All|
|System.Security.Cryptography.HashAlgorithmName|All|
|System.Security.Cryptography.HMAC|All|
|System.Security.Cryptography.HMACMD5|All|
|System.Security.Cryptography.HMACSHA1|All|
|System.Security.Cryptography.HMACSHA256|All|
|System.Security.Cryptography.HMACSHA384|All|
|System.Security.Cryptography.HMACSHA512|All|
|System.Security.Cryptography.KeyedHashAlgorithm|All|
|System.Security.Cryptography.MD5|All|
|System.Security.Cryptography.Oid|All|
|System.Security.Cryptography.PaddingMode|All|
|System.Security.Cryptography.RNGCryptoServiceProvider|All|
|System.Security.Cryptography.RSA|All|
|System.Security.Cryptography.RSAEncryptionPadding|All|
|System.Security.Cryptography.RSASignaturePadding|All|
|System.Security.Cryptography.SHA1|All|
|System.Security.Cryptography.SHA1Managed|All|
|System.Security.Cryptography.SHA256|All|
|System.Security.Cryptography.SHA256Managed|All|
|System.Security.Cryptography.SHA384|All|
|System.Security.Cryptography.SHA384Managed|All|
|System.Security.Cryptography.SHA512|All|
|System.Security.Cryptography.SHA512Managed|All|
|System.Security.Cryptography.SymmetricAlgorithm|All|
|System.Security.Cryptography.X509Certificates.PublicKey|All|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|All|
|System.Security.Cryptography.X509Certificates.X500УважаемыеИмена|name|
|System.Security.Cryptography.X509Certificates.X509Certificate|All|
|System.Security.Cryptography.X509Certificates.X509Certificate2|All|
|System.Security.Cryptography.X509Certificates.X509ContentType|All|
|System.Security.Cryptography.X509Certificates.X509NameType|All|
|System.Single|All|
|System.String|All|
|System.StringComparer|All|
|System.StringComparison|All|
|System.StringSplitOptions|All|
|System.Text.Encoding|All|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Конструктор), IsMatch, Матч, Матчи, Замена, Неэвакуация, Сплит|
|System.Text.RegularExpressions.RegexOptions|All|
|System.Text.StringBuilder|All|
|System.TimeSpan|All|
|System.TimeZone|All|
|System.time'oneinfo.AdjustmentRule|All|
|System.time'oneInfo.TransitionTime|All|
|System.TimeZoneInfo|All|
|System.Tuple|All|
|System.UInt16|All|
|System.UInt32|All|
|System.UInt64|All|
|System.Uri|All|
|System.UriPartial|All|
|System.Xml.Linq.Extensions|All|
|System.Xml.Linq.XAttribute|All|
|System.Xml.Linq.XCData|All|
|System.Xml.Linq.XComment|All|
|System.Xml.Linq.XContainer|All|
|System.Xml.Linq.XDeclaration|All|
|System.Xml.Linq.XDocument|Все, кроме: Нагрузка|
|System.Xml.Linq.XDocumentType|All|
|System.Xml.Linq.XElement|All|
|System.Xml.Linq.XName|All|
|System.Xml.Linq.XNamespace|All|
|System.Xml.Linq.XNode|All|
|System.Xml.Linq.XNodeDocumentOrderComparer|All|
|System.Xml.Linq.XNodeEqualityComparer|All|
|System.Xml.Linq.XObject|All|
|System.Xml.Linq.XProcessingInstruction|All|
|System.Xml.Linq.XText|All|
|System.Xml.XmlNodeType|All|

## <a name="context-variable"></a><a name="ContextVariables"></a> Переменная контекста
Переменная с именем `context` неявно доступна в каждом [выражении](api-management-policy-expressions.md#Syntax) политики. Ее члены предоставляют сведения, относящиеся к переменной `\request`. Все члены `context` доступны только для чтения.

|Переменная контекста|Допустимые методы, свойства и значения параметров|
|----------------------|-------------------------------------------------------|
|контекст|[Апи](#ref-context-api): [Иапи](#ref-iapi)<br /><br /> [Развертывание](#ref-context-deployment)<br /><br /> Elapsed: TimeSpan — интервал времени между значением Timestamp и текущим временем<br /><br /> [Последняяошибка](#ref-context-lasterror)<br /><br /> [Операции](#ref-context-operation)<br /><br /> [Продукт](#ref-context-product)<br /><br /> [Запрос](#ref-context-request)<br /><br /> RequestId: Guid — уникальный идентификатор запроса<br /><br /> [Ответ](#ref-context-response)<br /><br /> [Подписка](#ref-context-subscription)<br /><br /> Timestamp: DateTime — время получения запроса<br /><br /> Tracing: логическое значение — указывает, включена ли трассировка <br /><br /> [Пользователь](#ref-context-user)<br /><br /> [Переменные](#ref-context-variables): IReadOnlyDictionary<строка, объект><br /><br /> void Trace(message: строка)|
|<a id="ref-context-api"></a>context.Api|Id: строка<br /><br /> IsCurrentRevision: bool<br /><br />  Name: строка<br /><br /> Path: строка<br /><br /> Revision: строка<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: строка |
|<a id="ref-context-deployment"></a>context.Deployment|Region: строка<br /><br /> ServiceName: строка<br /><br /> Certificates: IReadOnlyDictionary<строка, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: строка<br /><br /> Reason: строка<br /><br /> Message: строка<br /><br /> Scope: строка<br /><br /> Section: строка<br /><br /> Path: строка<br /><br /> PolicyId: строка<br /><br /> Дополнительные сведения о переменной context.LastError см. в разделе [Error handling](api-management-error-handling-policies.md) (Обработка ошибок).|
|<a id="ref-context-operation"></a>context.Operation|Id: строка<br /><br /> Method: строка<br /><br /> Name: строка<br /><br /> UrlTemplate: строка|
|<a id="ref-context-product"></a>context.Product|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: логическое значение<br /><br /> Группы: Неисчислимые<[IGroup](#ref-igroup)\><br /><br /> Id: строка<br /><br /> Name: строка<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: целое число?<br /><br /> SubscriptionRequired: логическое значение|
|<a id="ref-context-request"></a>context.Request|Тело: [IMessageBody](#ref-imessagebody) или `null` если запрос не имеет тела.<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Заголовки](#ref-context-request-headers): IReadOnlyDictionary<строки, строки ><br /><br /> IpAddress: строка<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: строка<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: строка, defaultValue: строка)|headerName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения заголовков запросов, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|
|<a id="ref-context-response"></a>context.Response|Тело: [IMessageBody](#ref-imessagebody)<br /><br /> [Заголовки](#ref-context-response-headers): IReadOnlyDictionary<строки, строки ><br /><br /> StatusCode: целое число<br /><br /> StatusReason: строка|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: строка, defaultValue: строка)|headerName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения заголовков ответов, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedTime: дата и время<br /><br /> EndDate: дата и время?<br /><br /> Id: строка<br /><br /> Key: строка<br /><br /> Name: строка<br /><br /> PrimaryKey: строка<br /><br /> SecondaryKey: строка<br /><br /> StartDate: дата и время?|
|<a id="ref-context-user"></a>context.User|Email: строка<br /><br /> FirstName: строка<br /><br /> Группы: Неисчислимые<[IGroup](#ref-igroup)\><br /><br /> Id: строка<br /><br /> Удостоверения: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: строка<br /><br /> Note: строка<br /><br /> RegistrationDate: дата и время|
|<a id="ref-iapi"></a>IApi|Id: строка<br /><br /> Name: строка<br /><br /> Path: строка<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Подписныеключевые имена: [ISubscriptionKeyParameternamesnames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: строка<br /><br /> Name: строка|
|<a id="ref-imessagebody"></a>IMessageBody|Как<\>T (preserveContent: bool - ложный): Где T: строка, байт,JObject, JToken, JArray, XNode, XElement, XDocument, XDocument<br /><br /> Методы `context.Request.Body.As<T>` и `context.Response.Body.As<T>` используются для чтения текста сообщения запроса или ответа в одном из заданных форматов `T`. По умолчанию метод использует исходный поток текста сообщения и делает его недоступным после возвращения. Чтобы избежать этого, задайте для параметра `preserveContent` значение `true`, чтобы метод работал с копией потока текста. Перейдите по [этой](api-management-transformation-policies.md#SetBody) ссылке, чтобы увидеть пример.|
|<a id="ref-iurl"></a>IUrl|Host: строка<br /><br /> Path: строка<br /><br /> Port: целое число<br /><br /> [Запрос](#ref-iurl-query): IReadOnlyDictionary<строка, строка ><br /><br /> QueryString: строка<br /><br /> Scheme: строка|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: строка<br /><br /> Provider: строка|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: строка<br /><br /> Query: строка|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: строка, defaultValue: строка)|queryParameterName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает разделенные запятыми значения параметров запроса или значение `defaultValue`, если параметр не найден.|
|<a id="ref-context-variables"></a>T контекст. Variables.GetValueOrDefault<\>T (variableName: строка, defaultValue: T)|variableName: строка<br /><br /> defaultValue: T<br /><br /> Возвращает значение переменной, приведенное к типу `T` или `defaultValue`, если переменная не найдена.<br /><br /> Этот метод выдает исключение, если указанный тип не соответствует фактическому типу возвращаемой переменной.|
|BasicAuthCredentials AsBasic(input: this string)|input: строка<br /><br /> Если входной параметр содержит допустимое значение заголовка запроса авторизации для обычной проверки подлинности HTTP, метод возвращает объект типа `BasicAuthCredentials`; в противном случае метод возвращает значение NULL.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: строка<br /><br /> result: выходное значение BasicAuthCredentials<br /><br /> Если входной параметр содержит допустимое значение заголовка запроса авторизации для обычной проверки подлинности и HTTP, метод возвращает значение `true`, а параметр результата содержит значение типа `BasicAuthCredentials`. В противном случае метод возвращает значение `false`.|
|BasicAuthCredentials|Password: строка<br /><br /> UserId: строка|
|Jwt AsJwt(input: this string)|input: строка<br /><br /> Если входной параметр содержит допустимое значение маркера JWT, метод возвращает объект типа `Jwt`; в противном случае метод возвращает значение `null`.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: строка<br /><br /> result: выходное значение типа Jwt<br /><br /> Если входной параметр содержит допустимое значение маркера JWT, метод возвращает значение `true`, а параметр результата содержит значение типа `Jwt`; в противном случае метод возвращает значение `false`.|
|Jwt|Algorithm: строка<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: дата и время?<br /><br /> Id: строка<br /><br /> Issuer: строка<br /><br /> Опубликовано: Время свиданий?<br /><br /> NotBefore: дата и время?<br /><br /> Subject: строка<br /><br /> Type: строка|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: строка<br /><br /> defaultValue: строка<br /><br /> Возвращает значения утверждений, разделенные запятыми, или значение `defaultValue`, если заголовок не найден.|
|byte[] Encrypt(input: this byte[], alg: строка, key:byte[], iv:byte[])|input — открытый текст, который нужно зашифровать<br /><br />alg — имя алгоритма симметричного шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input — открытый текст, который нужно зашифровать<br /><br />alg — алгоритм шифрования<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input — открытый текст, который нужно зашифровать<br /><br />alg — алгоритм шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст в зашифрованном виде.|
|byte[] Decrypt(input: this byte[], alg: строка, key:byte[], iv:byte[])|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — имя алгоритма симметричного шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — алгоритм шифрования<br /><br />Возвращает открытый текст.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input — зашифрованный текст, который нужно расшифровать<br /><br />alg — алгоритм шифрования<br /><br />key — ключ шифрования<br /><br />iv — вектор инициализации<br /><br />Возвращает открытый текст.|
|bool VerifyNoRevocation (вход: эта система.Security.Cryptography.X509Certificates.X509Certificate2)|Выполняет проверку цепи X.509 без проверки статуса отзыва сертификата.<br /><br />ввод - объект сертификата<br /><br />Возвращает, `true` если проверка удается; `false` если проверка не удается.|


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
+ [Примеры политик](policy-samples.md).
