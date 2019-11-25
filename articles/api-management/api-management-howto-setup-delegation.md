---
title: Делегирование пользователю регистрации и подписки на продукт
description: Узнайте, как делегировать регистрации пользователей и подписки на продукты третьим лицам в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454357"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Делегирование пользователю регистрации и подписки на продукт

Delegation allows you to use your existing website for handling developer sign in/sign up and subscription to products, as opposed to using the built-in functionality in the developer portal. It enables your website to own the user data and perform the validation of these steps in a custom way.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Делегирование входа и регистрации разработчика

To delegate developer, sign in and sign up to your existing website, you'll need to create a special delegation endpoint on your site. It needs to act as the entry-point for any such request initiated from the API Management developer portal.

Итоговый рабочий процесс будет иметь следующий вид:

1. Developer clicks on the sign in or sign up link at the API Management developer portal
2. Браузер перенаправляется в конечную точку делегирования
3. Delegation endpoint in return redirects to or presents UI asking user to sign in or sign up
4. При успешном выполнении данной операции пользователь перенаправляется обратно на ту страницу портала разработчика API Management, с которой он начал данный процесс

Сначала следует настроить службу управления API так, чтобы он направлял запросы через вашу конечную точку делегирования. In the Azure portal, search for **Security** in your API Management resource and then click the **Delegation** item. Click the checkbox to enable 'Delegate sign in & sign up'.

![Страница "Делегирование"][api-management-delegation-signin-up]

* Выберите для себя нужный URL-адрес своей специальной конечной точки делегирования и введите его в поле **URL-адрес конечной точки делегирования** . 
* В поле "Ключ проверки подлинности делегирования" введите секретный ключ, который будет использоваться для вычисления сигнатуры, благодаря которой будет можно убедиться, что запрос действительно поступает из службы управления Azure API. Вы можете нажать кнопку **Создать**, и служба управления API сгенерирует для вас случайный ключ.

Теперь необходимо создать **конечную точку делегирования**. Для этого следует выполнить несколько действий:

1. Получите запрос в следующей форме:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Query parameters for the sign in / sign up case:
   
   * **operation**: идентифицирует тип запроса на делегирование (в данном случае может быть только **SignIn**).
   * **returnUrl**: the URL of the page where the user clicked on a sign in or sign up link
   * **salt**– специальная строка случайных данных, используемая для вычисления хэша безопасности.
   * **sig**– вычисленный хэш безопасности, который будет сравниваться с вашим вычисленным хэшем.
2. Убедитесь, что запрос поступает из службы управления Azure API (это необязательный шаг, но мы настоятельно рекомендуем его выполнять для обеспечения безопасности)
   
   * Вычислите хэш HMAC-SHA512 строки на основе параметров запроса **returnUrl** и **salt** ([на приведенном ниже примере кода]).
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Сравните вычисленный выше хэш со значением параметра запроса **sig**. Если два хэша совпадают друг с другом, перейдите к следующему шагу. Если нет, отклоните запрос.
3. Verify that you are receiving a request for sign in/sign up: the **operation** query parameter will be set to "**SignIn**".
4. Present the user with UI to sign in or sign up
5. Если пользователь регистрируется, для него необходимо создать соответствующую учетную запись в API Management. [Создание пользователя] с помощью интерфейса API Management REST API. When doing so, ensure that you set the user ID to the same value as in your user store or to an ID that you can keep track of.
6. После успешной проверки подлинности пользователя:
   
   * [запросите маркер единого входа (SSO)] через API Management REST API
   * добавьте параметр запроса returnUrl в URL-адреса SSO, который вы получили из вызова API, указанного выше:
     
     > Например: https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * перенаправьте пользователя на вышеупомянутый URL-адрес

Помимо использования операции **SignIn**, вы можете управлять учетными записями, выполнив указанные выше действия и одну из следующих операций.

* **ChangePassword;**
* **ChangeProfile;**
* **CloseAccount.**

Для операций управления учетными записями необходимо передать следующие параметры запроса.

* **operation**– определяет тип запроса на делегирование (ChangePassword, ChangeProfile или CloseAccount).
* **userId**: the user ID of the account to manage
* **salt**– специальная строка случайных данных, используемая для вычисления хэша безопасности.
* **sig**– вычисленный хэш безопасности, который будет сравниваться с вашим вычисленным хэшем.

## <a name="delegate-product-subscription"> </a>Делегирование подписки на продукт
Delegating product subscription works similarly to delegating user sign in/-up. Итоговый рабочий процесс будет иметь следующий вид:

1. Developer selects a product in the API Management developer portal and clicks on the Subscribe button.
2. Browser is redirected to the delegation endpoint.
3. Delegation endpoint performs required product subscription steps. It's up to you to design the steps. They may include redirecting to another page to request billing information, asking additional questions, or simply storing the information and not requiring any user action.

Чтобы включить функцию, на странице **Делегирование** щелкните **Делегировать подписку на продукт**.

Next, ensure the delegation endpoint does the following actions:

1. Получите запрос в следующей форме:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Параметры запроса для подписки на продукт:
   
   * **operation**: идентифицирует тип запроса на делегирование. Для запросов подписки на продукт допустимыми являются следующие параметры:
     * "Subscribe": запрос на подписку пользователя на заданный продукт с предоставленным идентификатором (см. ниже).
     * "Unsubscribe": запрос на отказ от подписки пользователя на продукт;
     * "Renew": запрос на обновление подписки (например, в случае истечения срока действия).
   * **productId**: идентификатор продукта, подписку на который запрашивает пользователь.
   * **subscriptionId**: on *Unsubscribe* and *Renew* - the ID of the product subscription
   * **userId**: the ID of the user the request is made for
   * **salt**– специальная строка случайных данных, используемая для вычисления хэша безопасности.
   * **sig**– вычисленный хэш безопасности, который будет сравниваться с вашим вычисленным хэшем.

2. Убедитесь, что запрос поступает из службы управления Azure API (это необязательный шаг, но мы настоятельно рекомендуем его выполнять для обеспечения безопасности)
   
   * Compute an HMAC-SHA512 of a string based on the **productId**, **userId**, and **salt** query parameters:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Сравните вычисленный выше хэш со значением параметра запроса **sig**. Если два хэша совпадают друг с другом, перейдите к следующему шагу. Если нет, отклоните запрос.
3. Process product subscription based on the type of operation requested in **operation** - for example, billing, further questions, etc.
4. On successfully subscribing the user to the product on your side, subscribe the user to the API Management product by [calling the REST API for subscriptions].

## <a name="delegate-example-code"> </a> Пример кода

These code samples show how to:

* Take the *delegation validation key*, which is set in the Delegation screen of the publisher portal
* Create an HMAC, which is then used to validate the signature, proving the validity of the passed returnUrl.

Тот же код с незначительными изменениями подходит для productId и userId.

**Код C# для создания хэша returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Код NodeJS для создания хэша returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> You need to [republish the developer portal](api-management-howto-developer-portal-customize.md#publish) for the delegation changes to take effect.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию о делегировании см. в следующем видео.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[запросите маркер единого входа (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[Создание пользователя]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[calling the REST API for subscriptions]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[на приведенном ниже примере кода]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
