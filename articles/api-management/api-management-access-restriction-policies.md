---
title: Политики ограничения доступа в службе управления API Azure | Документация Майкрософт
description: Сведения о политиках ограничения, доступных для использования в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/21/2019
ms.author: apimpm
ms.openlocfilehash: 3201edd3b90d6db1393286db688b24065ea8dc6b
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273544"
---
# <a name="api-management-access-restriction-policies"></a>Политики ограничения доступа в службе управления API

В этой статье рассматриваются приведенные ниже политики управления API. Дополнительные сведения о добавлении и настройке политик см. в статье о [политиках в управлении API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a> Политики ограничения доступа

-   [Проверка заголовка HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) – обеспечивает принудительный ввод заголовка HTTP и/или его значения.
-   [Ограничение частоты вызовов по подписке](api-management-access-restriction-policies.md#LimitCallRate) — предотвращает пики использования API, ограничивая частоту вызовов для каждой подписки.
-   [Ограничение частоты вызовов по ключу](#LimitCallRateByKey) — предотвращает пики использования API, ограничивая частоту вызовов по ключу.
-   [Ограничение IP-адресов вызывающих объектов](api-management-access-restriction-policies.md#RestrictCallerIPs) – фильтрует (разрешает или запрещает) вызовы с конкретных IP-адресов и/или диапазонов адресов.
-   [Задание квоты использования по подписке](api-management-access-restriction-policies.md#SetUsageQuota) — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждой подписки.
-   [Задание квоты использования по ключу](#SetUsageQuotaByKey) — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждого ключа.
-   [Проверка JWT](api-management-access-restriction-policies.md#ValidateJWT) – обеспечивает принудительное задание и проверку JWT, извлеченного из заданного заголовка HTTP или параметра запроса.

> [!TIP]
> Политики ограничений доступа можно использовать в разных областях для разных целей. Например, можно защитить весь API с помощью проверки подлинности AAD, применив `validate-jwt` политику на уровне API, или применить ее на уровне операций API и использовать `claims` для более детального управления.

## <a name="CheckHTTPHeader"></a> Проверка заголовка HTTP

Используйте политику `check-header`, чтобы запрос содержал заданный заголовок HTTP. При необходимости можно проверить, содержит ли заголовок определенное значение, или проверить диапазон допустимых значений. При сбое проверки политика завершает обработку запроса, после чего возвращает код состояния HTTP и сообщение об ошибке, указанное в политике.

### <a name="policy-statement"></a>Правило политики

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Пример

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Элементы

| Название         | Описание                                                                                                                                   | Обязательное значение |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | Корневой элемент.                                                                                                                                 | Да      |
| value        | Допустимое значение заголовка HTTP. Если указано несколько элементов value и одно из значений совпадает, проверка считается успешной. | Нет       |

### <a name="attributes"></a>Атрибуты

| Название                       | Описание                                                                                                                                                            | Обязательное значение | Значение по умолчанию |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Сообщение об ошибке, возвращаемое в тексте ответа HTTP, если заголовок не существует или имеет недопустимое значение. Это сообщение должно содержать правильно экранированные специальные символы. | Да      | Н/Д     |
| failed-check-httpcode      | Код состояния HTTP, который возвращается, если заголовок не существует или имеет недопустимое значение.                                                                                        | Да      | Н/Д     |
| header-name                | Имя заголовка HTTP для проверки.                                                                                                                                  | Да      | Н/Д     |
| ignore-case                | Можно задать значение true или false. Если задано значение true и значение заголовка сравнивается с набором допустимых значений, регистр игнорируется.                                    | Да      | Н/Д     |

### <a name="usage"></a>Использование

Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound, outbound.

-   **Области политики:** все области.

## <a name="LimitCallRate"></a> Ограничение частоты вызовов по подписке

Политика `rate-limit` предотвращает пики использования API для каждой подписки, ограничивая частоту вызовов до указанного числа за определенный период времени. При запуске этой политики вызывающий объект получает код состояния ответа `429 Too Many Requests`.

> [!IMPORTANT]
> Эту политику можно использовать для каждого документа политики только один раз.
>
> Для данной политики [выражения политики](api-management-policy-expressions.md) нельзя использовать в атрибутах политики.

> [!CAUTION]
> Из-за распределенного характера архитектуры регулирования ограничение частоты никогда не является полностью точным. Разница между настроенными и реальным количеством допустимых запросов зависит от объема запроса и скорости, задержки серверной части и других факторов.

### <a name="policy-statement"></a>Правило политики

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Пример

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Элементы

| Название      | Описание                                                                                                                                                                                                                                                                                              | Обязательное значение |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | Корневой элемент.                                                                                                                                                                                                                                                                                            | Да      |
| api       | Добавьте один или несколько этих элементов, чтобы установить ограничение частоты вызовов для API в пределах продукта. Ограничения частоты вызовов продукта и API применяются раздельно. Ссылаться на API можно с помощью `name` или `id`. Если указаны оба атрибута, `id` будет использоваться, а `name` — игнорироваться.                    | Нет       |
| operation | Добавьте один или несколько этих элементов, чтобы применить ограничение частоты вызовов для операций в API. Ограничения частоты вызовов продукта, API и операции применяются раздельно. Ссылаться на операцию можно с помощью `name` или `id`. Если указаны оба атрибута, `id` будет использоваться, а `name` — игнорироваться. | Нет       |

### <a name="attributes"></a>Атрибуты

| Название           | Описание                                                                                           | Обязательное значение | Значение по умолчанию |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Имя API, для которого применяется ограничение частоты.                                                | Да      | Н/Д     |
| calls          | Максимальное общее число вызовов, разрешенное в течение периода времени, указанного в `renewal-period`. | Да      | Н/Д     |
| renewal-period | Период времени (в секундах), по окончании которого сбрасывается квота.                                              | Да      | Н/Д     |

### <a name="usage"></a>Использование

Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.

-   **Области политики:** продукт, API, операция

## <a name="LimitCallRateByKey"></a> Ограничение частоты вызовов по ключу

> [!IMPORTANT]
> Эта функция недоступна в ценовой категории **Потребление** управления API.

Политика `rate-limit-by-key` предотвращает пики использования API для каждого ключа, ограничивая частоту вызовов до указанного числа за определенный период времени. Ключ может содержать произвольное строковое значение и обычно указывается с помощью выражения политики. Чтобы указать, какие запросы следует учитывать для ограничения, можно добавить дополнительное условие увеличения. При запуске этой политики вызывающий объект получает код состояния ответа `429 Too Many Requests`.

Дополнительные сведения и примеры этой политики см. в статье [Расширенное регулирование запросов с помощью управления API](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> Из-за распределенного характера архитектуры регулирования ограничение частоты никогда не является полностью точным. Разница между настроенными и реальным количеством допустимых запросов зависит от объема запроса и скорости, задержки серверной части и других факторов.

### <a name="policy-statement"></a>Правило политики

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Пример

В следующем примере ограничение частоты содержит ключ, состоящий из IP-адреса вызывающего объекта.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Элементы

| Название              | Описание   | Обязательное значение |
| ----------------- | ------------- | -------- |
| ставка-ограничение по ключу | Корневой элемент. | Да      |

### <a name="attributes"></a>Атрибуты

| Название                | Описание                                                                                           | Обязательное значение | Значение по умолчанию |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| вызовы               | Максимальное общее число вызовов, разрешенное в течение периода времени, указанного в `renewal-period`. | Да      | Н/Д     |
| counter-key         | Ключ, используемый для политики ограничения частоты.                                                             | Да      | Н/Д     |
| increment-condition | Логическое выражение, указывающее, следует ли учитывать запрос для квоты (`true`).        | Нет       | Н/Д     |
| renewal-period      | Период времени (в секундах), по окончании которого сбрасывается квота.                                              | Да      | Н/Д     |

### <a name="usage"></a>Использование

Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.

-   **Области политики:** все области.

## <a name="RestrictCallerIPs"></a> Ограничение IP-адресов вызывающих объектов

Политика `ip-filter` фильтрует (разрешает и запрещает) вызовы с конкретных IP-адресов и (или) диапазонов адресов.

### <a name="policy-statement"></a>Правило политики

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Пример

В следующем примере политика разрешает только запросы, поступающие с одного IP-адреса или диапазона IP-адресов.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Элементы

| Название                                      | Описание                                         | Обязательное значение                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Корневой элемент.                                       | Да                                                            |
| адрес                                   | Указывает один IP-адрес для фильтрации.   | По крайней мере один элемент `address` или `address-range` является обязательным. |
| address-range from="address" to="address" | Указывает диапазон IP-адресов для фильтрации. | По крайней мере один элемент `address` или `address-range` является обязательным. |

### <a name="attributes"></a>Атрибуты

| Название                                      | Описание                                                                                 | Обязательное значение                                           | Значение по умолчанию |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | Диапазон IP-адресов, для которого действует разрешение или запрет доступа.                                        | Обязательный атрибут, если используется элемент `address-range`. | Н/Д     |
| ip-filter action="allow &#124; forbid"    | Указывает, должны ли быть разрешены или запрещены вызовы для указанных IP-адресов и диапазонов. | Да                                                | Н/Д     |

### <a name="usage"></a>Использование

Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.
-   **Области политики:** все области.

## <a name="SetUsageQuota"></a> Задание квоты использования по подписке

Политика `quota` принудительно устанавливает возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждой подписки.

> [!IMPORTANT]
> Эту политику можно использовать для каждого документа политики только один раз.
>
> Для данной политики [выражения политики](api-management-policy-expressions.md) нельзя использовать в атрибутах политики.

### <a name="policy-statement"></a>Правило политики

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Пример

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Элементы

| Название      | Описание                                                                                                                                                                                                                                                                                  | Обязательное значение |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Корневой элемент.                                                                                                                                                                                                                                                                                | Да      |
| api       | Добавьте один или несколько этих элементов, чтобы применить квоту вызовов для API в рамках продукта. Квоты для продукта и вызовов API применяются раздельно. Ссылаться на API можно с помощью `name` или `id`. Если указаны оба атрибута, `id` будет использоваться, а `name` — игнорироваться.                    | Нет       |
| operation | Добавьте один или несколько этих элементов, чтобы применить квоту вызова для операций в API. Квоты для продукта, API и вызовов операций применяются раздельно. Ссылаться на операцию можно с помощью `name` или `id`. Если указаны оба атрибута, `id` будет использоваться, а `name` — игнорироваться. | Нет       |

### <a name="attributes"></a>Атрибуты

| Название           | Описание                                                                                               | Обязательное значение                                                         | Значение по умолчанию |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Имя API или операции, к которой применяется квота.                                             | Да                                                              | Н/Д     |
| bandwidth      | Максимальное общее число килобайтов, разрешенное в течение периода времени, указанного в `renewal-period`. | Необходимо указать атрибут `calls`, `bandwidth` или оба вместе. | Н/Д     |
| calls          | Максимальное общее число вызовов, разрешенное в течение периода времени, указанного в `renewal-period`.     | Необходимо указать атрибут `calls`, `bandwidth` или оба вместе. | Н/Д     |
| renewal-period | Период времени (в секундах), по окончании которого сбрасывается квота.                                                  | Да                                                              | Н/Д     |

### <a name="usage"></a>Использование

Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.
-   **Области политики:** product.

## <a name="SetUsageQuotaByKey"></a> Задание квоты использования по ключу

> [!IMPORTANT]
> Эта функция недоступна в ценовой категории **Потребление** управления API.

Политика `quota-by-key` принудительно устанавливает возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждого ключа. Ключ может содержать произвольное строковое значение и обычно указывается с помощью выражения политики. Чтобы указать, какие запросы следует учитывать в квоте, можно добавить дополнительное условие увеличения. Если несколько политик будут увеличивать одно и то же значение ключа, оно увеличивается только один раз за запрос. При достижении этого вызова политики вызывающий объект получает код состояния ответа `403 Forbidden`.

Дополнительные сведения и примеры этой политики см. в статье [Расширенное регулирование запросов с помощью управления API](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Правило политики

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Пример

В следующем примере квота содержит ключ, состоящий из IP-адреса вызывающего объекта.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Элементы

| Название  | Описание   | Обязательное значение |
| ----- | ------------- | -------- |
| quota | Корневой элемент. | Да      |

### <a name="attributes"></a>Атрибуты

| Название                | Описание                                                                                               | Обязательное значение                                                         | Значение по умолчанию |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| пропускная способность           | Максимальное общее число килобайтов, разрешенное в течение периода времени, указанного в `renewal-period`. | Необходимо указать атрибут `calls`, `bandwidth` или оба вместе. | Н/Д     |
| calls               | Максимальное общее число вызовов, разрешенное в течение периода времени, указанного в `renewal-period`.     | Необходимо указать атрибут `calls`, `bandwidth` или оба вместе. | Н/Д     |
| counter-key         | Ключ, используемый для политики квоты.                                                                      | Да                                                              | Н/Д     |
| increment-condition | Логическое выражение, указывающее, следует ли учитывать запрос для квоты (`true`).             | Нет                                                               | Н/Д     |
| renewal-period      | Период времени (в секундах), по окончании которого сбрасывается квота.                                                  | Да                                                              | Н/Д     |

### <a name="usage"></a>Использование

Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.
-   **Области политики:** все области.

## <a name="ValidateJWT"></a> Проверка JWT

Политика `validate-jwt` обеспечивает принудительное задание и проверку маркера JWT, извлеченного из заданного заголовка HTTP или параметра запроса.

> [!IMPORTANT]
> Для политики `validate-jwt` требуется, чтобы зарегистрированное утверждение `exp` было включено в маркер JWT, только если для атрибута `require-expiration-time` не задано значение `false`.
> Политика `validate-jwt` поддерживает алгоритмы подписывания HS256 и RS256. Для HS256 необходимо, чтобы ключ содержался внутри политики в кодировке Base64. Для RS256 ключ должен предоставляться через конечную точку конфигурации Open ID.
> Политика `validate-jwt` поддерживает маркеры, зашифрованные с помощью симметричных ключей с использованием алгоритмов шифрования A128CBC-HS256, A192CBC-HS384 или A256CBC-HS512.

### <a name="policy-statement"></a>Правило политики

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <zumo-master-key id="key identifier">key value</zumo-master-key>
</validate-jwt>

```

### <a name="examples"></a>Примеры

#### <a name="simple-token-validation"></a>Простая проверка маркера

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Проверка маркеров Azure Active Directory

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Проверка токена Azure Active Directory B2C

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Авторизация доступа к операциям на основе утверждений маркера

В этом примере показано, как использовать политику [проверки JWT](api-management-access-restriction-policies.md#ValidateJWT) для авторизации доступа к операциям на основе значения утверждений токена.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

#### <a name="azure-mobile-services-token-validation"></a>Проверка маркеров мобильных служб Azure

```xml
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">
    <issuers>
        <issuer>urn:microsoft:windows-azure:zumo</issuer>
    </issuers>
    <audiences>
        <audience>Facebook</audience>
    </audiences>
    <issuer-signing-keys>
        <zumo-master-key id="0">insert key here</zumo-master-key>
    </issuer-signing-keys>
</validate-jwt>
```

### <a name="elements"></a>Элементы

| Элемент             | Описание                                                                                                                                                                                                                                                                                                                                           | Обязательное значение |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Корневой элемент.                                                                                                                                                                                                                                                                                                                                         | Да      |
| audiences           | Содержит список допустимых утверждений audience, которые могут присутствовать в маркере. При наличии нескольких значений audience каждое значение проверяется либо до их исчерпания (в этом случае проверка будет не пройдена), либо до обнаружения подходящего значения. Необходимо указать по крайней мере один элемент audience.                                                                     | Нет       |
| issuer-signing-keys | Список ключей безопасности в кодировке Base64, используемых для проверки подписанных маркеров. При наличии нескольких ключей безопасности каждый ключ проверяется либо до их исчерпания (в этом случае проверка будет не пройдена), либо до обнаружения подходящего ключа (удобно для смены маркеров). Ключи могут содержать дополнительный атрибут `id`, используемый для сопоставления с утверждением `kid`.               | Нет       |
| decryption-keys     | Список ключей в кодировке Base64 для расшифровки маркеров. При наличии нескольких ключей безопасности каждый ключ проверяется либо до их исчерпания (в этом случае проверка будет не пройдена), либо до обнаружения подходящего ключа. Ключи могут содержать дополнительный атрибут `id`, используемый для сопоставления с утверждением `kid`.                                                 | Нет       |
| issuers             | Список допустимых субъектов-служб, выдавших маркер. При наличии нескольких значений элемента issuer каждое значение проверяется либо до их исчерпания (в этом случае проверка будет не пройдена), либо до обнаружения подходящего значения.                                                                                                                                         | Нет       |
| openid-config       | Элемент, используемый для указания соответствующей конечной точки конфигурации Open ID, из которой можно получить ключи подписывания и элемент issuer.                                                                                                                                                                                                                        | Нет       |
| required-claims     | Содержит список утверждений, которые должны содержаться в маркере, который будет считаться допустимым. Если для атрибута `match` задано значение `all`, каждое значение утверждения в политике должно присутствовать в маркере для успешного завершения проверки. Если для атрибута `match` задано значение `any`, в маркере должно присутствовать по крайней мере одно утверждение для успешного завершения проверки. | Нет       |
| zumo-master-key     | Главный ключ для маркеров, выданных мобильными службами Azure.                                                                                                                                                                                                                                                                                                 | Нет       |

### <a name="attributes"></a>Атрибуты

| Название                            | Описание                                                                                                                                                                                                                                                                                                                                                                                                                                            | Обязательное значение                                                                         | Значение по умолчанию                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | Интервал времени. Настройка максимальной ожидаемой разницы во времени между системными часами поставщика маркеров и экземпляра управления API.                                                                                                                                                                                                                                                                                                               | Нет                                                                               | 0 секунд                                                                         |
| failed-validation-error-message | Сообщение об ошибке, которое возвращается в текст HTTP-ответа, если JWT не прошел проверку. Это сообщение должно содержать правильно экранированные специальные символы.                                                                                                                                                                                                                                                                                                 | Нет                                                                               | Сообщение об ошибке по умолчанию зависит от проблемы проверки, например "JWT отсутствует". |
| failed-validation-httpcode      | Код состояния HTTP, который возвращается, если JWT не прошел проверку.                                                                                                                                                                                                                                                                                                                                                                                         | Нет                                                                               | 401                                                                               |
| header-name                     | Имя заголовка НТТР, содержащего маркер.                                                                                                                                                                                                                                                                                                                                                                                                         | Необходимо указать `header-name`один `query-parameter-name` из `token-value` или. | Н/Д                                                                               |
| query-parameter-name            | Имя параметра запроса, содержащего маркер безопасности.                                                                                                                                                                                                                                                                                                                                                                                                     | Необходимо указать `header-name`один `query-parameter-name` из `token-value` или. | Н/Д                                                                               |
| значение токена                     | Выражение, возвращающее строку, содержащую токен JWT                                                                                                                                                                                                                                                                                                                                                                                                     | Необходимо указать `header-name`один `query-parameter-name` из `token-value` или. | Н/Д                                                                               |
| id                              | Атрибут `id` в элементе `key` позволяет указать строку, которая будет сопоставлена с утверждением `kid` в маркере (при наличии), чтобы найти подходящий ключ для проверки подписи.                                                                                                                                                                                                                                           | Нет                                                                               | Н/Д                                                                               |
| match                           | Атрибут `match` в элементе `claim` указывает, должно ли присутствовать каждое значение утверждения политики в маркере для успешного завершения проверки. Возможные значения:<br /><br /> - `all` — каждое значение утверждения в политике должно присутствовать в маркере для успешного завершения проверки.<br /><br /> - `any` — в маркере должно присутствовать по крайней мере одно значение утверждения для успешного завершения проверки.                                                       | Нет                                                                               | все                                                                               |
| require-expiration-time         | Логическое значение. Указывает, требуется ли утверждение истечения срока действия для маркера.                                                                                                                                                                                                                                                                                                                                                                               | Нет                                                                               | true                                                                              |
| require-scheme                  | Имя схемы маркера, например "Bearer". Когда задан этот атрибут, политики обеспечивают присутствие указанной схемы в значении заголовка Authorization.                                                                                                                                                                                                                                                                                    | Нет                                                                               | Н/Д                                                                               |
| require-signed-tokens           | Логическое значение. Указывает, должен ли быть подписан маркер.                                                                                                                                                                                                                                                                                                                                                                                           | Нет                                                                               | true                                                                              |
| Разделитель                       | Строка. Указывает разделитель (например, ",") для извлечения набора значений из многозначного утверждения.                                                                                                                                                                                                                                                                                                                                          | Нет                                                                               | Н/Д                                                                               |
| url                             | URL-адрес конечной точки конфигурации Open ID, по которому можно получить метаданные конфигурации Open ID. Ответ должен соответствовать спецификациям, как определено в URL-адресе :`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Для Azure Active Directory используйте URL-адрес `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration`, подставив необходимое имя клиента каталога, например `contoso.onmicrosoft.com`. | Да                                                                              | Н/Д                                                                               |
Output-Token-переменная-имя|Строка. Имя переменной контекста, которая будет принимать значение токена как объект типа [`Jwt`](api-management-policy-expressions.md) после успешной проверки токена|Нет|Н/Д

### <a name="usage"></a>Использование

Эта политика может использоваться в следующих [разделах](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) и [областях](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Разделы политики:** inbound.
-   **Области политики:** все области.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о работе с политиками см. в следующих статьях:

-   [Политики в управлении API](api-management-howto-policies.md)
-   [Преобразование API-интерфейсов](transform-api.md).
-   Полный перечень операторов политик и их параметров см. в [справочнике по политикам](api-management-policy-reference.md).
-   [Примеры политик](policy-samples.md).
