---
title: Утверждения клиентов (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о подписанной поддержке утверждений клиентов для конфиденциальных клиентских приложений в Библиотеке подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050294"
---
# <a name="confidential-client-assertions"></a>Конфиденциальные утверждения клиентов

Чтобы доказать свою личность, конфиденциальные клиентские приложения обмениваются секретом с Azure AD. Секрет может быть:
- Секрет клиента (пароль приложения).
- Сертификат, который используется для построения подписанного утверждения, содержащего стандартные требования.

Этот секрет также может быть подписано утверждение непосредственно.

MSAL.NET имеет четыре способа предоставления учетных данных или утверждений конфиденциальному клиенту:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Хотя можно использовать `WithClientAssertion()` API для приобретения токенов для конфиденциального клиента, мы не рекомендуем использовать его по умолчанию, поскольку он более продвинутый и предназначен для обработки очень конкретных сценариев, которые не являются общими. Использование `.WithCertificate()` API позволит MSAL.NET справиться с этим для вас. Этот api предлагает вам возможность настроить запрос на проверку подлинности, если это необходимо, но утверждение по умолчанию, созданное `.WithCertificate()` будет достаточно для большинства сценариев проверки подлинности. Этот API также может быть использован в качестве обходного пути в некоторых сценариях, где MSAL.NET не выполняет операцию подписания внутренне.

### <a name="signed-assertions"></a>Подписанные утверждения

Подписанное утверждение клиента принимает форму подписанного JWT с полезной нагрузкой, содержащей требуемые требования аутентификации, утвержденные Azure AD, Base64 закодирован. Чтобы ее использовать, сделайте следующее:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Претензии, ожидаемые Azure AD:

Тип утверждения | Значение | Описание
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Претензия "aud" (аудитория) определяет получателей, для которого предназначен JWT (здесь Azure AD) См.
exp | Чт Июнь 27 2019 15:04:17 GMT-0200 (Романтическое дневное время) | Утверждение "exp" (время окончания срока действия) указывает время окончания срока действия или время, после которого маркер JWT НЕ ДОЛЖЕН приниматься в обработку. См.
iss | (Клиентида) | Претензия "iss" (эмитент) определяет основной, выдав jWT. Обработка этой претензии является специфической для применения. Значение "iss" представляет собой чувствительную строку, содержащую значение StringOrURI. (RFC 7519, раздел 4.1.1)
jti | (Гид) | Претензия "jti" (JWT ID) является уникальным идентификатором для JWT. Значение идентификатора должно быть присвоено таким образом, чтобы гарантировать, что существует незначительная вероятность того, что одно и то же значение будет случайно присвоено другому объекту данных; если приложение использует несколько эмитентов, столкновения должны быть предотвращены среди значений, производимых различными эмитентами, а также. Претензия "jti" может быть использована для предотвращения воспроизведения JWT. Значение "jti" является чувствительной к случаям строкой. (RFC 7519, раздел 4.1.7)
nbf | Чт Июнь 27 2019 14:54:17 GMT-0200 (Романтическое дневное время) | Утверждение "nbf" (не ранее) определяет время, до которого маркер JWT НЕ ДОЛЖЕН приниматься в обработку. (RFC 7519, раздел 4.1.5)
sub | (Клиентида) | Претензия "суб" (субъект) определяет предмет JWT. Претензии в JWT, как правило, заявления по этому вопросу. Тема значение должно быть либо прим. См. (RFC 7519, раздел 4.1.2)

Вот пример того, как выработать эти претензии:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Вот как создать подписанное утверждение клиента:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Альтернативный метод

У вас также есть возможность использовать [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) для создания утверждения для вас. Код будет более элегантным, как показано на примере ниже:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

После того как у вас есть подписанное утверждение клиента, вы можете использовать его с MSAL apis, как показано ниже.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>Склиентам-претензиями

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`по умолчанию будет производитьподписанное утверждение, содержащее требования, ожидаемые Azure AD, а также дополнительные клиентские претензии, которые вы хотите отправить. Вот фрагмент кода о том, как это сделать.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Если одна из претензий в словаре, в которую вы проходите, совпадает с одной из обязательных претензий, будет учтена стоимость дополнительной претензии. Он будет переопределять претензии, вычисленные MSAL.NET.

Если вы хотите предоставить свои собственные претензии, включая обязательные `false` требования, ожидаемые Azure AD, пройдите по параметру. `mergeWithDefaultClaims`
