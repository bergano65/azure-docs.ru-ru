---
title: Настройка взаимной проверки подлинности TLS в Службе приложений Azure
description: Узнайте, как настроить ваше приложение для использования проверки подлинности сертификата клиента на TLS.
services: app-service
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.custom: seodec18
ms.openlocfilehash: d441329bc3f279e95b2ee302db53d78f786c3470
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650403"
---
# <a name="how-to-configure-tls-mutual-authentication-for-azure-app-service"></a>Настройка взаимной проверки подлинности TLS в службе приложений Azure
## <a name="overview"></a>Обзор
Доступ к службе приложений Azure можно ограничить, используя разные способы проверки подлинности. Один из них — проверка подлинности с помощью сертификата клиента, если запрос поступает по протоколу TLS/SSL. Этот механизм называется взаимной проверкой подлинности TLS или проверкой подлинности сертификата клиента. В этой статье описано, как настроить приложение для использования проверки подлинности сертификата клиента.

> **Примечание.** Если для доступа к сайту используется протокол HTTP, а не HTTPS, вы не получите сертификат клиента. Поэтому, если ваше приложение требует клиентские сертификаты, не следует разрешать запросы к приложению по протоколу HTTP.
> 
> 

## <a name="configure-app-service-for-client-certificate-authentication"></a>Настройка службы приложений на проверку подлинности сертификатов клиента
Чтобы ваше приложение требовало сертификаты клиента, необходимо добавить параметр сайта clientCertEnabled для приложения и присвоить ему значение true. Этот параметр также можно настроить на портале Azure в колонке SSL-сертификатов.

Для быстрого создания вызова REST API можно воспользоваться [средством ARMClient](https://github.com/projectkudu/ARMClient) . После входа с помощью средства необходимо выполнить следующую команду:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

Замените все содержимое в скобках {} сведениями для своего приложения и создайте файл с именем enableclientcert.json со следующим содержимым JSON.

    {
        "location": "My App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Обязательно измените значение location на расположение своего приложения, например центрально-северная часть США или западная часть США и т. п.

Можно также использовать https://resources.azure.com, чтобы переключить свойство `clientCertEnabled` в значение `true`.

> **Примечание.** При запуске ARMClient из PowerShell необходимо экранировать знак \@ для JSON-файла с помощью грависа (`).
> 
> 

## <a name="accessing-the-client-certificate-from-app-service"></a>Доступ к сертификату клиента из службы приложений
Если используется ASP.NET, и приложение настроено для аутентификации на основе сертификата клиента, то сертификат будет доступен через свойство **HttpRequest.ClientCertificate** . Для других стеков приложений сертификат клиента в вашем приложении будет доступен через значение заголовка запроса X-ARR-ClientCert, закодированное в base64. Приложение может создать сертификат из этого значения и использовать его для проверки подлинности и авторизации.

## <a name="special-considerations-for-certificate-validation"></a>Дополнительные рекомендации для проверки сертификата
Сертификат клиента, который отправляется в приложение, не проходит проверку платформой службы приложений Azure. За проверку этого сертификата отвечает приложение. Ниже приведен пример кода ASP.NET, который проверяет свойства сертификата для проверки подлинности.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
