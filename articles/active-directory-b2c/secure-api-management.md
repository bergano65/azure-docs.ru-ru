---
title: Защищайте API-аИП Управления API Azure с помощью Active Directory B2C
description: Узнайте, как использовать токены доступа, выпущенные B2C Active Directory, для обеспечения безопасности апойнта API API API.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186936"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Защищайте API-иНОУправления API Azure с помощью Azure AD B2C

Узнайте, как ограничить доступ к API-адресу Azure API Management (APIM) клиентам, удостоверившиеподлинность с помощью Azure Active Directory B2C (Azure AD B2C). Выполните действия в этой статье, чтобы создать и протестировать входящий политики в APIM, который ограничивает доступ только к тем запросам, которые включают действительный маркер доступа Azure AD B2C.

## <a name="prerequisites"></a>Предварительные требования

Вам нужны следующие ресурсы на месте, прежде чем продолжить шаги в этой статье:

* [Клиент Azure AD B2C](tutorial-create-tenant.md)
* [приложение, зарегистрированное](tutorial-register-applications.md) в клиенте;
* [созданные маршруты пользователей](tutorial-create-user-flows.md) в клиенте.
* [Опубликовано API](../api-management/import-and-publish.md) в управлении API Azure
* [Почтальон](https://www.getpostman.com/) для проверки защищенного доступа (необязательно)

## <a name="get-azure-ad-b2c-application-id"></a>Получение идентификатора приложения Azure AD B2C

При обеспечении безопасности API в управлении API Azure с помощью Azure AD B2C необходимо несколько значений для [входящих политики,](../api-management/api-management-howto-policies.md) создаваемых в APIM. Во-первых, запишите идентификатор приложения, созданное ранее в вашем арендаторе Azure AD B2C. Если вы используете приложение, созданное в предварительных условиях, используйте идентификатор приложения для *webbapp1.*

Вы можете использовать текущий опыт **приложений** или наш новый унифицированный опыт **регистрации приложений (Preview)** для получения идентификатора приложения. [См. дополнительные сведения о новом интерфейсе](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Приложения](#tab/applications/)

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы**, а затем найдите и выберите **Azure AD B2C**
1. Под **управлением**, выберите **приложения**.
1. Запись значения в колонке **APPLICATION ID** для *webapp1* или другого приложения, которое вы ранее создали.

#### <a name="app-registrations-preview"></a>[Регистрация приложений (предварительная версия)](#tab/app-reg-preview/)

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **Каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Либо щелкните **Все службы**, а затем найдите и выберите **Azure AD B2C**
1. Выберите **регистрацию приложений (Preview),** затем выберите вкладку **Принадлежащих приложений.**
1. Запись значения в колонке **идентификатора приложения (клиента)** для *webapp1* или другого приложения, которое вы создали ранее.

* * *

## <a name="get-token-issuer-endpoint"></a>Получить конечную точку эмитента токенов

Затем получите известный URL-адрес конфигурации для одного из потоков пользователей Azure AD B2C. Вам также нужна конечная точка uri эмитента токенов, которая требуется поддержать в Azure API Management.

1. На [портале Azure](https://portal.azure.com)AD B2C можно ознакомиться с клиентом Azure AD B2C.
1. В **соответствии**с политиками выберите **потоки пользователей (политики).**
1. Выберите существующую политику, например *B2C_1_signupsignin1,* затем выберите **поток пользователей Run.**
1. Запись URL-адреса в гиперссылке, отображаемых под **потоком пользователя Run,** направляющегося в верхней части страницы. Этот URL-адрес является известной конечней точкой обнаружения OpenID Connect для потока пользователей, и вы используете его в следующем разделе при настройке входящего отдела политики управления API.

    ![Известная гиперссылка URI на странице портала Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Выберите гиперссылку для просмотра на известной странице конфигурации OpenID Connect.
1. На странице, которая открывается в `issuer` вашем браузере, запишите значение, например:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Это значение используется в следующем разделе при настройке API в Azure API Management.

Теперь в следующем разделе должны быть записаны два URL-адреса: OpenID Connect, известный URL-адрес конфигурации, и URL-адрес эмитента URI. Пример:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Настройка входящих политик в управлении API Azure

Теперь вы готовы добавить входящее в управление API API, которое проверяет вызовы API. Добавив политику [проверки JWT,](../api-management/api-management-access-restriction-policies.md#ValidateJWT) которая проверяет аудиторию и эмитента в токене доступа, можно гарантировать, что принимаются только вызовы API с действительным токеном.

1. Перейдите к экземпляру управления API Azure на [портале Azure](https://portal.azure.com).
1. Выберите **Интерфейсы API**.
1. Выберите API, который необходимо закрепить с помощью Azure AD B2C.
1. Выберите вкладку **Конструктор**.
1. При **внедней обработке**выберите, ** \< / ** чтобы открыть редактор кода политики.
1. Поместите `<validate-jwt>` следующий `<inbound>` тег внутри политики.

    1. Обновление `url` значения элемента `<openid-config>` с помощью известного URL-адреса конфигурации вашей политики.
    1. Обновление `<audience>` элемента с идентификатором приложения, созданного ранее в вашем арендаторе B2C (например, *webapp1).*
    1. Обновление `<issuer>` элемента с конечным пунктом маркера эмитента, записанной ранее.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Проверка безопасного доступа API

Чтобы обеспечить доступ к API только аутентифицированным абонентам, можно проверить конфигурацию управления API Azure, позвонив в API с [Postman.](https://www.getpostman.com/)

Для вызова API необходимо как кектонут доступа, выданном у Azure AD B2C, так и ключ подписки APIM.

### <a name="get-an-access-token"></a>Получение маркера доступа

Сначала для использования в `Authorization` заголовке в Postman необходимо токен, выпущенный Azure AD B2C. Вы можете получить один с помощью функции **Run теперь** регистрации / регистрации в потоке пользователей вы должны были создать в качестве одного из предварительных условий.

1. На [портале Azure](https://portal.azure.com)AD B2C можно ознакомиться с клиентом Azure AD B2C.
1. В **соответствии**с политиками выберите **потоки пользователей (политики).**
1. Выберите существующий поток пользователей регистрации/ввоза, например, *B2C_1_signupsignin1.*
1. Для **приложения,** выберите *webapp1*.
1. Для **URL-адреса ответа**выберите `https://jwt.ms`.
1. Выберите **поток пользователя Run.**

    ![Запустите страницу потока пользователей для регистрации в потоке пользователей на портале Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Завершите процесс входа в систему. Вы должны быть `https://jwt.ms`перенаправлены на .
1. Запись закодированного значения маркера, отображаемого в вашем браузере. Это значение маркера используется для заголовка авторизации в Postman.

    ![Закодированное значение маркера отображается на jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Получите ключ подписки API

Клиентское приложение (в данном случае Postman), которое вызывает опубликованный API, должно включать действительный ключ подписки API Management в свои запросы HTTP к API. Чтобы получить ключ подписки, чтобы включить в ваш запрос Почтальон HTTP:

1. На [портале Azure](https://portal.azure.com)посетите экземпляр службы управления API Azure.
1. Выберите **Подписку**.
1. Выберите эллипсис для **продукта: Неограниченный,** затем выберите **клавиши Show/hide.**
1. Запишите **PRIMARY KEY** для продукта. Этот ключ используется `Ocp-Apim-Subscription-Key` для заголовка в запросе HTTP в Postman.

![Ключевая страница подписки с ключами Show/hide, выбранными на портале Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Протестировать безопасный вызов API

С записью ключа доступа к маркеру и подписки APIM вы готовы проверить, правильно ли вы настроены на безопасный доступ к API.

1. Создайте `GET` новый запрос в [Postman](https://www.getpostman.com/). Для URL-адреса запроса укажите конечную точку списка API, опубликованную вами в качестве одного из предварительных условий. Пример:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Далее добавьте следующие заголовки:

    | Ключ | Значение |
    | --- | ----- |
    | `Authorization` | Закодированное значение маркера, записанное ранее, с префиксированным `Bearer ` (включите пространство после "Bearer") |
    | `Ocp-Apim-Subscription-Key` | Ключ подписки APIM, который вы записали ранее |

    URL-адрес **ЗАПРОСа GET** и **заголовки** должны отображаться похожи на:

    ![Почтовый uI, показывающий URL-адрес запроса GET и заголовки](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Выберите кнопку **Отправить** в Postman для выполнения запроса. Если вы все правильно настроили, вам следует представить ответ JSON с коллекцией докладчиков на конференции (показано здесь усеченным):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Протестировать небезопасный вызов API

Теперь, когда вы сделали успешный запрос, проверьте случай сбоя, чтобы убедиться, что вызовы на API с *недействительным* токеном будут отклонены, как ожидалось. Один из способов выполнения теста — добавить или изменить несколько символов `GET` в значении маркера, а затем выполнить тот же запрос, что и раньше.

1. Добавьте несколько символов к значению маркера для имитации недействительного маркера. Например, добавьте значение "INVALID" в значение токена:

    ![Раздел заголовков UI Postman, показывающий INVALID, добавленный в токен](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Выберите кнопку **Отправки** для выполнения запроса. С недействительным токеном ожидаемым результатом является несанкционированный код статуса: `401`

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Если вы `401` видите код статуса, вы проверили, что только абоненты с действительным маркером доступа, выпущенным Azure AD B2C, могут делать успешные запросы на API-наидля управления API API.

## <a name="support-multiple-applications-and-issuers"></a>Поддержка нескольких приложений и эмитентов

Несколько приложений обычно взаимодействуют с одним API REST. Чтобы позволить API принимать токены, предназначенные для нескольких приложений, добавьте иные данные их приложений в `<audiences>` элемент в входящий политики APIM.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Аналогичным образом, чтобы поддержать несколько эмитентов токенов, добавьте их конечные УРИ в `<issuers>` элемент в входящий политики APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Мигрировать в b2clogin.com

Если у вас есть APIM API, который проверяет `login.microsoftonline.com` токены, выпущенные устаревшей конечной точкой, следует перенести API и приложения, которые призывают его использовать токены, выпущенные [b2clogin.com.](b2clogin.md)

Вы можете следить за этим общим процессом для выполнения поэтапной миграции:

1. Добавьте поддержку в входящий полис APIM для токенов, выпущенных как b2clogin.com, так и login.microsoftonline.com.
1. Обновляйте приложения по одному, чтобы получать токены с b2clogin.com конечную точку.
1. Как только все приложения правильно получают токены из b2clogin.com, удалите из API поддержку login.microsoftonline.com токенов.

Следующий пример входящего политика APIM иллюстрирует, как принимать токены, выпущенные как b2clogin.com, так и login.microsoftonline.com. Кроме того, он поддерживает запросы API от двух приложений.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о политиках управления API Azure API можно узнать в [справочном индексе политики APIM.](../api-management/api-management-policies.md)

Вы можете найти информацию о миграции OWIN на основе веб-API и их приложений, чтобы b2clogin.com в [migrate OWIN основе веб-API для b2clogin.com.](multiple-token-endpoints.md)
