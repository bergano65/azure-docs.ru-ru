---
title: Утверждения клиента в библиотеке проверки подлинности Microsoft для .NET | Службы
description: Сведения о поддержке подписанных утверждений клиентов для конфиденциальных клиентских приложений в библиотеке проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ea75499334f3f6eb2f5d3c15526067fcef4eb8
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442505"
---
# <a name="confidential-client-assertions"></a>Конфиденциальные утверждения клиентов
Чтобы доказать свою личность, конфиденциальные клиентские приложения обмениваются секретом с Azure AD. Секрет может быть следующим:
- Секрет клиента (пароль приложения).
- Сертификат, который используется для создания подписанного утверждения, содержащего стандартные утверждения.

Этот секрет также может быть подписанным утверждением напрямую.

MSAL.NET состоит из четырех методов предоставления учетных данных или утверждений для конфиденциального клиентского приложения:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

### <a name="signed-assertions"></a>Подписанные утверждения

Подписанное утверждение клиента принимает форму подписанного JWT с полезной нагрузкой, содержащей необходимые утверждения проверки подлинности, заданные в Azure AD, в кодировке Base64. Чтобы ее использовать, сделайте следующее:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Утверждения, ожидаемые Azure AD:

Тип утверждения | Значение | Описание
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | Утверждение "AUD" (аудитория) определяет получателей, для которых предназначено JWT (здесь Azure AD), см. [RFC 7519, раздел 4.1.3]
exp | Четверг Июн 27 2019 15:04:17 GMT + 0200 (Романское время (лето)) | Утверждение "exp" (время окончания срока действия) указывает время окончания срока действия или время, после которого маркер JWT НЕ ДОЛЖЕН приниматься в обработку. См. [RFC 7519, раздел 4.1.4]
iss | ClientID | Утверждение ISS (Issuer) идентифицирует участника, который выдал JWT. Обработка этого утверждения зависит от конкретного приложения. Значение ISS — это строка с учетом регистра, содержащая значение Стрингорури. [RFC 7519, раздел 4.1.1]
jti | (GUID) | Утверждение "ЖТИ" (идентификатор JWT) предоставляет уникальный идентификатор для JWT. Значение идентификатора должно быть назначено способом, гарантирующим незначительную вероятность того, что одно и то же значение будет случайно назначено другому объекту данных. Если приложение использует несколько издателей, то конфликты между значениями, создаваемыми разными издателями, должны быть предотвращены. Утверждение "ЖТИ" можно использовать для предотвращения воспроизведения JWT. Значение "ЖТИ" является строкой с учетом регистра. [RFC 7519, раздел 4.1.7]
nbf | Четверг Июн 27 2019 14:54:17 GMT + 0200 (Романское время (лето)) | Утверждение "nbf" (не ранее) определяет время, до которого маркер JWT НЕ ДОЛЖЕН приниматься в обработку. [RFC 7519, раздел 4.1.5]
sub | ClientID | Утверждение "подсистема" (тема) определяет тему JWT. Утверждения в JWT обычно являются инструкциями о субъекте. Значение субъекта должно быть либо в области локально уникальным в контексте издателя, либо быть глобально уникальным. См. [RFC 7519, раздел 4.1.2]

Ниже приведен пример создания этих утверждений.

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
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

Вот как можно создавать подписанное утверждение клиента:

```CSharp
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

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

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
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>висклиентклаимс

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`по умолчанию будет создаваться подписанное утверждение, содержащее утверждения, ожидаемые Azure AD, и дополнительные клиентские утверждения, которые требуется отправить. Ниже приведен фрагмент кода для этого.

```CSharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Если одно из утверждений в словаре, которое вы передали, совпадает с одним из обязательных утверждений, учитывается значение дополнительного утверждения. Он переопределит утверждения, вычисленные с помощью MSAL.NET.

Если вы хотите предоставить собственные утверждения, включая обязательные утверждения, ожидаемые Azure AD, передайте `false` `mergeWithDefaultClaims` параметр.
