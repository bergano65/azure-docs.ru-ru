---
title: Обновление API автозаполнения Bing версии 5 до версии 7
titlesuffix: Azure Cognitive Services
description: Идентифицирует части приложения, которые необходимо обновить для использования версии 7.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: 8ada9ab6622ea12e48475230787168d90e95ec0d
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829874"
---
# <a name="autosuggest-api-upgrade-guide"></a>Руководство по обновлению API автозаполнения

В этом руководстве по обновлению определены изменения между версиями 5 и 7 API автозаполнения Bing. Руководство поможет определить компоненты приложения, которые необходимо обновить для использования версии 7.

## <a name="breaking-changes"></a>Критические изменения

### <a name="endpoints"></a>Конечные точки

- Номер версии конечной точки изменен с 5 на 7. Например, https://api.cognitive.microsoft.com/bing/\*\*v7.0\*\*/Suggestions.

### <a name="error-response-objects-and-error-codes"></a>Объекты ответов на ошибки и коды ошибок

- Все невыполненные запросы теперь должны включать в тело ответа объект `ErrorResponse`.

- В объект `Error` добавлены следующие поля:  
  - `subCode`&mdash;Разделяет код ошибки на дискретные сегменты, если это возможно
  - `moreDetails`&mdash;Дополнительные сведения об ошибке, описанной в поле `message`

- Коды ошибок версии 5 заменены следующими возможными значениями `code` и `subCode`.

|Код|SubCode (дополнительный код)|ОПИСАНИЕ
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing возвращает ServerError (ошибку сервера) каждый раз при возникновении любого из условий вложенного кода. Ответ включает в себя ошибки, если код состояния HTTP — 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Заблокировано|Bing возвращает ошибку InvalidRequest (недопустимый запрос) всякий раз, когда любая часть запроса недопустима. Например, отсутствует обязательный параметр или значение параметра недопустимо.<br/><br/>В случае ошибки ParameterMissing (отсутствующий параметр) или ParameterInvalidValue (недопустимое значение параметра) кодом состояния HTTP будет 400.<br/><br/>При ошибке HttpNotAllowed (HTTP запрещен) будет наблюдаться код состояния HTTP 410.
|RateLimitExceeded||Bing возвращает ошибку RateLimitExceeded всякий раз при превышении квоты запросов в секунду (QPS) или запросов в месяц (QPM).<br/><br/>Bing возвращает код состояния HTTP 429 при превышении квоты QPS и 403 при превышении QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing возвращает InvalidAuthorization, когда Bing не может проверить подлинность вызывающего объекта. Например, когда заголовок `Ocp-Apim-Subscription-Key` отсутствует или при недопустимом ключе подписки.<br/><br/>Избыточность возникает, если указать более одного способа проверки подлинности.<br/><br/>При ошибке InvalidAuthorization кодом состояния HTTP будет 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing возвращает InsufficientAuthorization, когда вызывающая сторона не имеет разрешений на доступ к ресурсу. Это может произойти, если ключ подписки отключен или срок его действия истек. <br/><br/>При ошибке InsufficientAuthorization кодом состояния HTTP будет 403.

- Ниже представлено сопоставление предыдущих кодов ошибок с новыми. При наличии зависимостей от кодов ошибок версии 5 обновите свой код соответствующим образом.

|Код версии 5|Код версии 7.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Отключено|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Заблокировано|InvalidRequest.Blocked

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Требования к использованию и отображению](./UseAndDisplayRequirements.md)
