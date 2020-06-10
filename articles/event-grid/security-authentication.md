---
title: 'Сетка событий Azure: безопасность и проверка подлинности'
description: В этой статье описаны разные способы проверки подлинности для доступа к ресурсам службы "Сетка событий" (веб-перехватчик, подписки, пользовательские разделы).
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: bca450022322db7a7569fa1dc7ce80ec75a9ce69
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774309"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Проверка подлинности для доступа к ресурсам службы "Сетка событий Azure"
В этой статье приведены сведения о следующих сценариях:  

- проверка подлинности клиентов, публикующих события в службе "Сетка событий Azure" с помощью подписанного URL-адреса (SAS) или ключа; 
- Защитите конечную точку веб-перехватчика, которая используется для получения событий из службы "Сетка событий Azure" с помощью Azure Active Directory (Azure AD) или общего секрета.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Проверка подлинности клиентов при публикации с помощью SAS или ключа
Для пользовательских разделов используется либо подписанный URL-адрес (SAS), либо аутентификация с помощью ключа. Рекомендуется использовать SAS, но проверка подлинности с использованием ключа обеспечивает простое программирование, а также совместима со множеством существующих издателей веб-перехватчиков.

Значение проверки подлинности следует включить в заголовок HTTP. Для SAS в качестве значения заголовка используйте **aeg-sas-token**. Для подлинности с использованием ключа в качестве значения заголовка следует использовать **aeg-sas-key**.

### <a name="key-authentication"></a>Проверка подлинности с использованием ключа

Проверка подлинности с использованием ключа — самая простая форма проверки подлинности. Используйте формат: `aeg-sas-key: <your key>` в заголовке сообщения.

Например, для передачи ключа можно использовать следующую команду:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Можно также указать `aeg-sas-key` в качестве параметра запроса. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Маркеры SAS

Маркеры SAS для сетки события включают ресурс, срок его действия и подпись. Маркер SAS имеет следующий формат: `r={resource}&e={expiration}&s={signature}`.

Ресурс — это путь для раздела сетки событий, в который вы отправляете события. Вот пример допустимого пути к ресурсу: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Сведения о поддерживаемых версиях API см. в статье [Типы ресурсов Microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Вы создаете подпись на основе ключа.

Например, допустимое значение **aeg-sas-token** выглядит следующим образом:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

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

### <a name="encryption-at-rest"></a>Шифрование при хранении

События и данные, записываемые на диск службой "Сетка событий", шифруются при хранении с помощью ключа, которым управляет корпорация Майкрософт. Кроме того, максимальный период, в течение которого хранятся события или данные, составляет 24 часа согласно [политике повтора службы "Сетка событий"](delivery-and-retry.md). Служба "Сетка событий" автоматически удалит все события или данные через 24 часа или по прошествии срока жизни события в зависимости того, что наступит раньше.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Проверка подлинности для доставки событий в конечные точки веб-перехватчика
В следующих разделах описана проверка подлинности для доставки событий в конечные точки веб-перехватчика. Вам нужно использовать механизм подтверждения для проверки независимо от используемого метода. Дополнительные сведения см. в статье [Доставка событий веб-перехватчика](webhook-event-delivery.md). 

### <a name="using-azure-active-directory-azure-ad"></a>Использование Azure Active Directory (Azure AD)
Вы можете защитить конечную точку веб-перехватчика, которая используется для получения событий из службы "Сетка событий Azure", с помощью Azure AD. Для использования приложения Azure AD вам нужно создать приложение Azure AD, а также роль и субъект-службу в приложении, которое будет выполнять авторизацию службы "Сетка событий". Затем потребуется настроить подписку на события. Узнайте, как [Настроить Azure Active Directory для работы со службой "Сетка событий"](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Использование секрета клиента в качестве параметра запроса
Защитить конечную точку веб-перехватчика можно также путем добавления параметров запроса для URL-адреса назначения веб-перехватчика, указанного в рамках создания подписки на события. Задайте один из параметров запроса в качестве секрета клиента, например [маркер доступа](https://en.wikipedia.org/wiki/Access_token) или общий секрет. Служба "Сетка событий" включает все параметры запроса в каждом запросе на доставку событий в веб-перехватчик. Служба веб-перехватчика может извлекать и проверять секрет. Если секрет клиента обновлен, необходимо также обновить подписку на события. Чтобы избежать ошибок доставки во время этой смены секрета, сделайте так, чтобы веб-перехватчик принимал как старые, так и новые секреты в течение ограниченного периода, прежде чем обновлять подписку на события с новым секретом. 

Так как параметры запроса могут содержать секреты клиента, они обрабатываются с особой осторожностью. Они хранятся в зашифрованном и недоступном для операторов службы виде. Они не регистрируются в качестве части журналов или трассировок службы. При получении свойств подписки на события параметры запроса назначения не возвращаются по умолчанию. Например: параметр [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) используется в Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Дополнительные сведения о доставке событий в веб-перехватчики см. в [этой статье](webhook-event-delivery.md).

> [!IMPORTANT]
Служба "Сетка событий Azure" поддерживает только конечные точки веб-перехватчиков **HTTPS**. 

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о сетке событий см. в статье [Сведения о сетке событий](overview.md)
