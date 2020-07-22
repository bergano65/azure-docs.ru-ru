---
title: Добавление соединителей API в потоки для самостоятельной регистрации в Azure AD
description: Настройка веб-API для использования в потоке пользователя.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0498a2015b75221763ab5fdd4f6e94428922bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386748"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Добавление соединителя API в поток пользователя

Чтобы использовать [соединитель API](api-connectors-overview.md), сначала создайте соединитель API, а затем включите его в потоке пользователя.

## <a name="create-an-api-connector"></a>Создание соединителя API

1. Войдите на [портал Azure](https://portal.azure.com/) с учетной записью администратора Azure AD.
2. В разделе **Службы Azure** щелкните **Azure Active Directory**.
3. В меню слева щелкните **Внешние удостоверения**.
4. Выберите **все соединители API (Предварительная версия)**, а затем выберите **новый соединитель API**.

   ![Добавить новый соединитель API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Укажите отображаемое имя для вызова. Например, **Проверьте состояние утверждения**.
6. Укажите **URL-адрес конечной точки** для вызова API.
7. Укажите сведения для проверки подлинности для API.

   - В настоящее время поддерживается только обычная проверка подлинности. Если вы хотите использовать API без обычной проверки подлинности для целей разработки, просто введите фиктивное **имя пользователя** и **пароль** , которые API может игнорировать. Для использования с функцией Azure с ключом API можно включить код в качестве параметра запроса в **URL-адрес конечной точки** (например, HTTPS []() ://contoso.azurewebsites.NET/API/Endpoint<b>? Code = 0123456789</b>).

   ![Добавить новый соединитель API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Выберите утверждения, которые необходимо отправить в API.
9. Выберите любые утверждения, которые вы планируете получить обратно из API.

   ![Задание утверждений соединителя API](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Нажмите кнопку **Сохранить**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Включение соединителя API в потоке пользователя

Выполните следующие действия, чтобы добавить соединитель API в поток пользователей самостоятельной регистрации.

1. Войдите на [портал Azure](https://portal.azure.com/) с учетной записью администратора Azure AD.
2. В разделе **Службы Azure** щелкните **Azure Active Directory**.
3. В меню слева щелкните **Внешние удостоверения**.
4. Выберите **потоки пользователей (Предварительная версия)**, а затем выберите пользовательский поток, к которому нужно добавить соединитель API.
5. Выберите **соединители API**, а затем выберите конечные точки API, которые нужно вызвать, на следующих шагах в потоке пользователя:

   - **После входа с помощью поставщика удостоверений**
   - **Перед созданием пользователя**

   ![Добавление API в поток пользователя](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Нажмите кнопку **Сохранить**.

Узнайте, [где можно включить соединитель API в потоке пользователя](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>Запрос, отправленный API

Соединитель API материализации как запрос HTTP POST, отправляющий выбранные утверждения в виде пар "ключ-значение" в теле JSON. Ответ должен также содержать заголовок HTTP `Content-Type: application/json` . Атрибуты сериализуются точно так же, как Microsoft Graph атрибуты пользователя. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Пример запроса

```http
POST <API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Утверждение **национальных настроек пользовательского интерфейса ("ui_locales")** отправляется по умолчанию во все запросы. Он предоставляет языковые стандарты пользователя и может использоваться API для возврата международных ответов. Он не отображается в области конфигурации API.

Если утверждение для отправки не имеет значения во время вызова конечной точки API, утверждение не будет отправлено в API.

Пользовательские атрибуты можно создать для пользователя с помощью extension_ формата ** \<extensions-app-id> _AttributeName** . API должен рассчитывать на получение утверждений в том же сериализованном формате. API-интерфейс может возвращать утверждения с помощью или без него `<extensions-app-id>` . Дополнительные сведения о настраиваемых атрибутах см. в разделе [Определение настраиваемых атрибутов для самостоятельной регистрации](user-flow-add-custom-attributes.md).

> [!TIP] 
> [**удостоверения ("удостоверения")**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) и **адрес электронной почты ("email_address")** можно использовать для идентификации пользователя, прежде чем он будет иметь учетную запись в клиенте. Утверждение "удостоверения" отправляется, когда пользователь проходит проверку подлинности с помощью Google или Facebook, а "email_address" всегда отправляется.

## <a name="expected-response-types-from-the-web-api"></a>Ожидаемые типы ответов из веб-API

Когда веб-API получает запрос HTTP от Azure AD во время потока пользователя, он может вернуть следующие ответы:

- [Ответ продолжения](#continuation-response)
- [Блокирующий ответ](#blocking-response)
- [Проверка-ошибка при отклике](#validation-error-response)

### <a name="continuation-response"></a>Ответ продолжения

Ответ на продолжение означает, что поток пользователя переходит к следующему шагу. В ответе на продолжение API может возвращать утверждения.

Если в API возвращается утверждение, которое выбирается в качестве **утверждения для получения**, утверждение выполняет следующие действия:

- Предварительно заполнить поля ввода на странице коллекции атрибутов, если соединитель API вызывается до представления страницы.
- Переопределяет любое значение, которое уже было назначено утверждению.
- Присваивает заявке значение, если оно было ранее null.

#### <a name="example-of-a-continuation-response"></a>Пример ответа продолжения

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Параметр                                          | Тип              | Обязательно | Описание                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | Строка            | Да      | Версия API.                                                                                                                                                                                                                                                                |
| action                                             | Строка            | Да      | Необходимое значение: `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Нет       | Значения могут храниться в каталоге, если они выбраны в качестве **утверждения для получения** в конфигурации соединителя API и **пользовательских атрибутов** для потока пользователя. Значения могут возвращаться в токене, если выбрано в качестве **утверждения приложения**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Нет       | Возвращаемое утверждение, по желанию, не может содержать `_<extensions-app-id>_` . Значения хранятся в каталоге, если они выбраны в качестве **утверждения для получения** в конфигурации соединителя API и **пользовательского атрибута** для потока пользователя. Пользовательские атрибуты не могут быть отправлены обратно в токен. |

### <a name="blocking-response"></a>Блокирующий ответ

Блокирующий ответ завершает поток пользователя. Он может быть специально выдан API-интерфейсом для прекращения продолжения работы потока пользователя путем отображения страницы блокировки для пользователя. На странице Block отображается `userMessage` предоставленный API-интерфейс.

Ниже приведен пример блокирующего ответа.

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Параметр   | Тип   | Обязательно | Описание                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | Строка | Да      | Версия API.                                                    |
| action      | Строка | Да      | Значение должно быть`ShowBlockPage`                                              |
| userMessage | Строка | Да      | Сообщение, отображаемое для пользователя.                                            |
| код        | Строка | Нет       | Код ошибки. Может использоваться в целях отладки. Не отображается для пользователя. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Реагирование на работу пользователей с блокирующим ответом

![Пример блокировки страницы](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Проверка-ошибка при отклике

Вызов API, вызванный после страницы коллекции атрибутов, может вернуть ответ на ошибку проверки. При этом поток пользователя остается на странице Коллекция атрибутов и `userMessage` отображается пользователю. Пользователь может изменить и повторно отправить форму. Этот тип ответа можно использовать для проверки входных данных.

#### <a name="example-of-a-validation-error-response"></a>Пример ответа на ошибку проверки

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Параметр   | Тип    | Обязательно | Описание                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | Строка  | Да      | Версия API.                                                    |
| action      | Строка  | Да      | Необходимое значение: `ValidationError`.                                           |
| status      | Целое число | Да      | `400`Для ответа ValidationError должно быть значение.                        |
| userMessage | Строка  | Да      | Сообщение, отображаемое для пользователя.                                            |
| код        | Строка  | Нет       | Код ошибки. Может использоваться в целях отладки. Не отображается для пользователя. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Пользовательский интерфейс с ответом на ошибку проверки

![Страница "пример проверки"](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Интеграция с Функциями Azure
Вы можете использовать триггер HTTP в службе "функции Azure" как простой способ создания API для использования с соединителем API. С помощью функции Azure можно, [например](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), выполнить логику проверки и ограничить число входов для конкретных доменов. Вы также можете вызывать и вызывать другие веб-API, пользовательские хранилища и другие облачные службы.

## <a name="next-steps"></a>Дальнейшие шаги

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Узнайте, как [Добавить пользовательский рабочий процесс утверждения для самостоятельной регистрации](self-service-sign-up-add-approvals.md)
- Приступая к работе с [примерами краткого руководства по функциям Azure](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
