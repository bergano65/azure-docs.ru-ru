---
title: Проверка подлинности клиентов Публикация событий в пользовательских разделах или доменах сетки событий
description: В этой статье описываются различные способы проверки подлинности клиентов при публикации событий в пользовательских разделах сетки событий.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1e147830a4b37a8603df8e4ce29953acab2345bd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115882"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Проверка подлинности клиентов публикации (сетка событий Azure)
Эта статья содержит сведения о проверке подлинности клиентов, публикующих события в разделах или доменах службы "Сетка событий Azure" с помощью **ключа доступа** или токена **подписанного URL-адрес (SAS)** . Рекомендуется использовать маркер SAS, но ключ проверки подлинности обеспечивает простое программирование и совместим с множеством существующих издателей веб-перехватчиков.  

## <a name="authenticate-using-an-access-key"></a>Проверка подлинности с помощью ключа доступа
Проверка подлинности с помощью ключа доступа — это простейшая форма проверки подлинности. Можно передать ключ доступа в качестве заголовка HTTP или параметра запроса URL-адреса. 

### <a name="access-key-in-a-http-header"></a>Ключ доступа в заголовке HTTP
Передайте ключ доступа в качестве значения заголовка HTTP: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Ключ доступа в качестве параметра запроса
Можно также указать `aeg-sas-key` в качестве параметра запроса. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Инструкции по получению ключей доступа для раздела или домена см. в разделе [получение ключей доступа](get-access-keys.md).

## <a name="authenticate-using-a-sas-token"></a>Проверка подлинности с помощью маркера SAS
Маркеры SAS для ресурса сетки событий включают ресурс, время окончания срока действия и подпись. Маркер SAS имеет следующий формат: `r={resource}&e={expiration}&s={signature}`.

Ресурс — это путь для раздела сетки событий, в который вы отправляете события. Вот пример допустимого пути к ресурсу: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Сведения о поддерживаемых версиях API см. в статье [Типы ресурсов Microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Сначала программно создайте маркер SAS, а затем используйте `aeg-sas-token` заголовок или `Authorization SharedAccessSignature` заголовок для проверки подлинности с помощью сетки событий. 

### <a name="generate-sas-token-programmatically"></a>Создание маркера SAS программным способом
В следующем примере создается маркер SAS для использования с сеткой событий:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="using-aeg-sas-token-header"></a>Использование заголовка AEG-SAS-Token
Ниже приведен пример передачи маркера SAS в качестве значения `aeg-sas-toke` заголовка. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Использование заголовка авторизации
Ниже приведен пример передачи маркера SAS в качестве значения `Authorization` заголовка. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Дальнейшие действия
Сведения о проверке подлинности с помощью обработчиков событий для доставки событий см. в разделе [Проверка подлинности доставки событий](security-authentication.md) . 