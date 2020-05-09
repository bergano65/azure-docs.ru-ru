---
title: 'Сетка событий Azure: безопасность и проверка подлинности'
description: В этой статье описываются различные способы проверки подлинности доступа к ресурсам сетки событий (веб-перехватчик, подписки, пользовательские разделы).
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 528c3613549ee49009f99d45e5bd9c2cf1745d78
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780000"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Проверка подлинности доступа к ресурсам службы "Сетка событий Azure"
Эта статья содержит сведения о следующих сценариях.  

- Проверка подлинности клиентов, публикующих события в службе "Сетка событий Azure" с помощью подписанного URL-адрес (SAS) или ключа. 
- Защитите конечную точку веб-перехватчика с помощью Azure Active Directory (Azure AD) для проверки подлинности сетки событий для **доставки** событий в конечную точку.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Проверка подлинности клиентов при публикации с помощью SAS или ключа
Для пользовательских разделов используется либо подписанный URL-адрес (SAS), либо аутентификация с помощью ключа. Рекомендуется использовать SAS, но проверка подлинности с использованием ключа обеспечивает простое программирование, а также совместима со множеством существующих издателей веб-перехватчиков.

Значение проверки подлинности следует включить в заголовок HTTP. Для SAS в качестве значения заголовка используйте **aeg-sas-token**. Для подлинности с использованием ключа в качестве значения заголовка следует использовать **aeg-sas-key**.

### <a name="key-authentication"></a>Проверка подлинности с использованием ключа

Проверка подлинности с использованием ключа — самая простая форма проверки подлинности. Используйте следующий формат: `aeg-sas-key: <your key>`

Например, для передачи ключа можно использовать следующую команду:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Маркеры SAS

Маркеры SAS для сетки события включают ресурс, срок его действия и подпись. Маркер SAS имеет следующий формат: `r={resource}&e={expiration}&s={signature}`.

Ресурс — это путь для раздела сетки событий, в который вы отправляете события. Например, допустимый путь к ресурсу: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Чтобы просмотреть все поддерживаемые версии API, см. раздел [типы ресурсов Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

Все события или данные, записываемые на диск службой "Сетка событий", шифруются с помощью ключа, управляемого корпорацией Майкрософт, что гарантирует их шифрование. Кроме того, максимальный период времени, в течение которого события или данные были сохранены, составляет 24 часа в соответствии с [политикой повтора сетки событий](delivery-and-retry.md). Сетка событий будет автоматически удалять все события или данные через 24 часа или срок жизни события в зависимости от того, какое значение меньше.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Проверка подлинности доставки событий в конечные точки веб-перехватчика
В следующих разделах описывается проверка подлинности доставки событий в конечные точки веб-перехватчика. Необходимо использовать механизм подтверждения проверки независимо от используемого метода. Дополнительные сведения см. в статье о [доставке событий веб-перехватчика](webhook-event-delivery.md) . 

### <a name="using-azure-active-directory-azure-ad"></a>Использование Azure Active Directory (Azure AD)
Вы можете защитить конечную точку веб-перехватчика с помощью Azure Active Directory (Azure AD) для проверки подлинности и авторизации службы "Сетка событий" для доставки событий в конечные точки. Вам потребуется создать приложение Azure AD, создать роль и субъект-службу в приложении, которое будет выполнять авторизацию в службе "Сетка событий", и настроить подписку на события для использования приложения Azure AD. [Узнайте, как настроить Azure Active Directory с помощью сетки событий](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Использование секрета клиента в качестве параметра запроса
Защитить конечную точку веб-перехватчика можно путем добавления параметров запроса для URL-адреса веб-перехватчика при создании подписки на события. Задайте один из этих параметров запроса как секрет клиента, например [маркер доступа](https://en.wikipedia.org/wiki/Access_token) или общий секрет. С его помощью веб-перехватчик сможет распознавать событие, поступающее из Сетки событий с допустимыми разрешениями. Сетка событий будет включать эти параметры в каждую доставку событий для веб-перехватчика. Если секрет клиента обновлен, необходимо также обновить подписку на события. Чтобы избежать ошибок доставки во время этого смены секрета, сделайте так, чтобы веб-перехватчик принимал как старые, так и новые секреты в течение ограниченного времени. 

Так как параметры запроса могут содержать секреты клиента, они обрабатываются с особой осторожностью. Они хранятся в виде зашифрованных и недоступных для операторов служб. Они не регистрируются как часть журналов или трассировок службы. При изменении подписки на событие, если в [интерфейсе командной строки](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) Azure не используется параметр [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show), параметры запроса не отображаются и не возвращаются.

Дополнительные сведения о доставке событий в веб-перехватчики см. в статье о [доставке событий веб-перехватчика](webhook-event-delivery.md) .

> [!IMPORTANT]
Служба "Сетка событий Azure" поддерживает только конечные точки веб-перехватчика **HTTPS** . 

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о сетке событий см. в статье [о сетке событий](overview.md) .
