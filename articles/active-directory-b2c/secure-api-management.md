---
title: Защита API управления API Azure с помощью Azure Active Directory B2C
description: Узнайте, как использовать маркеры доступа, выданные Azure Active Directory B2C для защиты конечной точки API управления API Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 942c565c885d59a14d64e7ec06beee0354e7c4ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641620"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Защита API управления API Azure с помощью Azure AD B2C

Узнайте, как ограничить доступ к API управления API Azure (APIM) для клиентов, которые прошли проверку подлинности с помощью Azure Active Directory B2C (Azure AD B2C). Выполните действия, описанные в этой статье, чтобы создать и протестировать политику входящего трафика в APIM, которая предоставляет доступ только к тем запросам, которые включают в себя действительный маркер доступа, выданный Azure AD B2C.

## <a name="prerequisites"></a>Предварительные требования

Перед продолжением действий, описанных в этой статье, необходимо выполнить следующие ресурсы:

* [клиент Azure AD B2C](tutorial-create-tenant.md);
* [приложение, зарегистрированное](tutorial-register-applications.md) в клиенте;
* [созданные маршруты пользователей](tutorial-create-user-flows.md) в клиенте.
* [Опубликованный API](../api-management/import-and-publish.md) в службе управления API Azure
* [Публикация](https://www.getpostman.com/) для проверки защищенного доступа (необязательно)

## <a name="get-azure-ad-b2c-application-id"></a>Получить идентификатор приложения Azure AD B2C

При обеспечении безопасности API в службе управления API Azure с Azure AD B2C требуется несколько значений для [политики входящего трафика](../api-management/api-management-howto-policies.md) , создаваемой в APIM. Сначала запишите идентификатор приложения, созданного ранее в клиенте Azure AD B2C. Если вы используете приложение, созданное в предварительных требованиях, используйте идентификатор приложения для *webbapp1*.

Для получения идентификатора приложения можно использовать текущие возможности **приложений** или новый единый интерфейс **Регистрация приложений (Предварительная версия)** . [См. дополнительные сведения о новом интерфейсе](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Приложения](#tab/applications/)

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Или выберите **все службы** , найдите и выберите **Azure AD B2C**.
1. В разделе **Управление**выберите **приложения**.
1. Запишите значение в столбце **идентификатор приложения** для идентификатора *APP1* или другого ранее созданного приложения.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Регистрация приложений (Предварительная версия)](#tab/app-reg-preview/)

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите фильтр **каталог и подписка** в верхнем меню, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. В меню слева выберите **Azure AD B2C**. Или выберите **все службы** , найдите и выберите **Azure AD B2C**.
1. Выберите **Регистрация приложений (Предварительная версия)** , а затем перейдите на вкладку " **принадлежащие приложения** ".
1. Запишите значение в столбце **идентификатор приложения (клиента)** для идентификатора *APP1* или другого ранее созданного приложения.

* * *

## <a name="get-token-issuer-endpoint"></a>Получение конечной точки издателя маркера

Затем получите известный URL-адрес конфигурации для одного из Azure AD B2C пользовательских потоков. Вам также понадобится URI конечной точки издателя маркера, который вы хотите поддерживать в службе управления API Azure.

1. Перейдите к клиенту Azure AD B2C в [портал Azure](https://portal.azure.com).
1. В разделе **политики**выберите **пользовательские потоки (политики)** .
1. Выберите существующую политику, например *B2C_1_signupsignin1*, а затем выберите **выполнить поток пользователя**.
1. Запишите URL-адрес в гиперссылке, которая отображается под заголовком **пользовательский поток запуска** в верхней части страницы. Этот URL-адрес является конечной точкой OpenID Connect Connected Discovery для потока пользователей и используется в следующем разделе при настройке политики входящего трафика в службе управления API Azure.

    ![Ссылка на известный URI на странице "Запуск" портал Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Выберите гиперссылку, чтобы перейти на страницу OpenID Connect Connected Configuration (хорошо известная конфигурация).
1. На странице, которая открывается в браузере, запишите значение `issuer`, например:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Это значение используется в следующем разделе при настройке API в службе управления API Azure.

Теперь для использования в следующем разделе должны быть записаны два URL-адреса: общедоступная конечная точка конфигурации OpenID Connect Connect и URI издателя. Например:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Настройка политики входящего трафика в службе управления API Azure

Теперь все готово к добавлению политики входящего трафика в службе управления API Azure, которая проверяет вызовы API. Добавив политику [проверки JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) , которая проверяет аудиторию и издателя в маркере доступа, можно убедиться, что принимаются только вызовы API с допустимым маркером.

1. Перейдите к экземпляру управления API Azure на [портале Azure](https://portal.azure.com).
1. Выберите **Интерфейсы API**.
1. Выберите API, который требуется защитить с помощью Azure AD B2C.
1. Выберите вкладку **Конструктор**.
1. В разделе **входящая обработка**выберите **\</\>** , чтобы открыть редактор кода политики.
1. Поместите следующий тег `<validate-jwt>` в политику `<inbound>`.

    1. Обновите значение `url` в элементе `<openid-config>` с помощью хорошо известного URL-адреса конфигурации политики.
    1. Обновите элемент `<audience>`, указав идентификатор приложения, созданного ранее в клиенте B2C (например, *APP1*).
    1. Обновите элемент `<issuer>` с помощью конечной точки издателя токена, записанной ранее.

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

## <a name="validate-secure-api-access"></a>Проверка безопасного доступа через API

Чтобы обеспечить доступ к API только тем, которые прошли проверку подлинности, можно проверить конфигурацию управления API Azure, вызвав API с помощью [POST](https://www.getpostman.com/).

Чтобы вызвать API, требуется как маркер доступа, выданный Azure AD B2C, так и ключ подписки APIM.

### <a name="get-an-access-token"></a>Получение маркера доступа

Сначала требуется маркер, выданный Azure AD B2C для использования в заголовке `Authorization` в POST. Его можно получить с помощью функции " **Запустить сейчас** " пользовательского потока регистрации или входа, который должен быть создан в качестве одного из необходимых компонентов.

1. Перейдите к клиенту Azure AD B2C в [портал Azure](https://portal.azure.com).
1. В разделе **политики**выберите **пользовательские потоки (политики)** .
1. Выберите существующий поток регистрации или входа пользователя, например *B2C_1_signupsignin1*.
1. Для **приложения**выберите " *APP1*".
1. В качестве **URL-адреса ответа**выберите `https://jwt.ms`.
1. Выберите **Выполнить поток пользователя**.

    ![Страница "запуск потока пользователей" для входа в поток пользователя в портал Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Завершите процесс входа в систему. Необходимо перенаправить на `https://jwt.ms`.
1. Запишите закодированное значение маркера, отображаемое в браузере. Это значение маркера используется для заголовка авторизации в POST.

    ![Закодированное значение маркера, отображаемое в jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Получение ключа подписки API

Клиентское приложение (в данном случае POST), которое вызывает опубликованный API, должно содержать допустимый ключ подписки управления API в своих HTTP-запросах к API. Чтобы получить ключ подписки для включения в HTTP-запрос POST, выполните следующие действия.

1. Перейдите к экземпляру службы управления API Azure на [портал Azure](https://portal.azure.com).
1. Выберите **Подписки**.
1. Выберите многоточие для **продукта: неограниченно**, а затем выберите **Показать/скрыть ключи**.
1. Запишите **первичный ключ** для продукта. Этот ключ используется для заголовка `Ocp-Apim-Subscription-Key` в HTTP-запросе в POST.

![Страница "ключ подписки" с ключами "Показать/скрыть", выбранные в портал Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Проверка безопасного вызова API

После регистрации маркера доступа и ключа подписки APIM вы можете проверить, правильно ли настроен безопасный доступ к API.

1. Создайте новый запрос `GET` в [POST](https://www.getpostman.com/). В поле URL-адрес запроса укажите конечную точку списка динамиков API, опубликованного в качестве одного из необходимых компонентов. Например:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Затем добавьте следующие заголовки:

    | Ключ | Значение |
    | --- | ----- |
    | `Authorization` | Записанное ранее закодированное значение маркера с префиксом `Bearer ` (включить пробел после "Bearer") |
    | `Ocp-Apim-Subscription-Key` | Записанный ранее ключ подписки APIM |

    URL-адрес запроса **Get** и **заголовки** должны выглядеть следующим образом:

    ![Пользовательский интерфейс POST с указанием URL-адреса и заголовков запроса GET](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Нажмите кнопку **Send (отправить** ) в POST, чтобы выполнить запрос. Если все настроено правильно, вы должны получить ответ JSON с коллекцией докладчиков (ниже показана усеченная):

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

### <a name="test-an-insecure-api-call"></a>Проверка небезопасного вызова API

Теперь, когда вы выполнили успешный запрос, проверьте случай сбоя, чтобы убедиться, что вызовы API с *недопустимым* токеном отклоняются должным образом. Один из способов выполнить тест — добавить или изменить несколько символов в значении маркера, а затем выполнить тот же запрос `GET`, как и раньше.

1. Добавьте несколько символов к значению токена, чтобы имитировать недопустимый токен. Например, добавьте "НЕДОПУСТИМое" в значение токена:

    ![Раздел заголовков в пользовательском интерфейсе POST, отображающий НЕДОПУСТИМое Добавление в токен](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Нажмите кнопку **Send (отправить** ), чтобы выполнить запрос. При использовании недопустимого маркера ожидаемый результат является `401` несанкционированный код состояния:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Если вы видите код состояния `401`, вы проверили, что только вызывающие объекты с действительным маркером доступа, выданными Azure AD B2C, могут успешно выполнять запросы к API управления API Azure.

## <a name="support-multiple-applications-and-issuers"></a>Поддержка нескольких приложений и издателей

Несколько приложений обычно взаимодействуют с одним REST API. Чтобы разрешить API принимать маркеры, предназначенные для нескольких приложений, добавьте идентификаторы приложений в элемент `<audiences>` в политике входящего трафика APIM.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Аналогично, для поддержки нескольких издателей маркеров добавьте их URI конечной точки в элемент `<issuers>` в политике входящих подключений APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Переход на b2clogin.com

Если у вас есть API APIM, который проверяет маркеры, выданные конечной точкой устаревшей `login.microsoftonline.com`, необходимо перенести API и приложения, вызывающие его, для использования маркеров, выданных [b2clogin.com](b2clogin.md).

Для выполнения поэтапной миграции можно использовать следующую общую процедуру.

1. Добавьте поддержку в политику входящей передачи APIM для маркеров, выдаваемых как в b2clogin.com, так и в login.microsoftonline.com.
1. Обновите приложения по одному за раз, чтобы получить маркеры из конечной точки b2clogin.com.
1. После того как все приложения будут правильно получать маркеры от b2clogin.com, удалите поддержку токенов, выпущенных login.microsoftonline.com, из API.

В следующем примере APIM Входящая политика показывает, как принимать маркеры, выданные как b2clogin.com, так и login.microsoftonline.com. Кроме того, он поддерживает запросы API из двух приложений.

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

Дополнительные сведения о политиках управления API Azure см. в статье [указатель справочника по политике APIM](../api-management/api-management-policies.md).

Сведения о миграции веб-API на основе OWIN и их приложений в b2clogin.com можно найти в статье [Миграция веб-API на основе OWIN в b2clogin.com](multiple-token-endpoints.md).
