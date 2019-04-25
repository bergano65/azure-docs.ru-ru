---
title: Authentication
titleSuffix: Cognitive Services - Azure
description: 'Существует три способа проверки подлинности запроса к ресурсу Azure Cognitive Services: ключ подписки, маркер носителя и подписка на несколько служб. В этой статье вы получите информацию о всех этих методах и узнаете, как выполнить запрос.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: erhopf
ms.openlocfilehash: 90bc2bf4c207f3bb2727d76c2e6b4fd5597539b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60336786"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Проверка подлинности запросов к Azure Cognitive Services

Каждый запрос к службе Azure Cognitive должен содержать заголовок проверки подлинности. Этот заголовок передает ключ подписки или маркер доступа, который позволяет проверить права подписки на доступ к службе или группе служб. В этой статье вы получите сведения о трех методах проверки подлинности запроса и требованиях для каждого из них.

* [Проверка подлинности по ключу подписки для одной службы](#authenticate-with-a-single-service-subscription-key)
* [Проверка подлинности по ключу подписки для нескольких служб](#authenticate-with-a-multi-service-subscription-key)
* [Проверка подлинности по маркеру](#authenticate-with-an-authentication-token)

## <a name="prerequisites"></a>Технические условия

Чтобы выполнять запрос, вам нужно иметь учетную запись Azure и подписку Azure Cognitive Services. Если у вас есть учетная запись, пропустите этот раздел и перейдите к следующему. Если у вас нет учетной записи, воспользуйтесь этим руководством и создайте ее за несколько минут: [Краткое руководство. Создание учетной записи Cognitive Services на портале Azure](cognitive-services-apis-create-account.md).

Можно получить ключ подписки из [портала Azure](cognitive-services-apis-create-account.md#access-your-resource) после создания учетной записи или активации [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/my-apis).
 
## <a name="authentication-headers"></a>Заголовки проверки подлинности

Давайте кратко рассмотрим заголовки проверки подлинности, которые доступны для использования с Azure Cognitive Services.

| Верхний колонтитул | ОПИСАНИЕ |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Используйте этот заголовок для проверки подлинности по ключу подписки для конкретной службы или для нескольких служб. |
| Ocp-Apim-Subscription-Region | Этот заголовок является обязательным, только если используется ключ подписки для нескольких служб с [API перевода текстов](./Translator/reference/v3-0-reference.md). Используйте этот заголовок, чтобы указать регион подписки. |
| Авторизация | Используйте этот заголовок, если вы применяете маркер проверки подлинности. В следующих разделах подробно описан процесс обмена маркерами. Значение предоставляется в следующем формате: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Проверка подлинности по ключу подписки для одной службы

Первый вариант — проверка подлинности запроса по ключу подписки для конкретной службы, например для службы перевода текста. Ключи можно получить на портале Azure для каждого созданного ресурса. Чтобы использовать ключ подписки для проверки подлинности запроса, этот ключ следует передать в заголовке `Ocp-Apim-Subscription-Key`.

В этих примерах запросов показано использование заголовка `Ocp-Apim-Subscription-Key`. Учтите, что для работы с этим примером вам потребуется действительный ключ подписки.

Вот пример вызова к API Bing для поиска в Интернете:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Вот пример вызова к API перевода текстов:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

В следующем видео показано использование ключа Cognitive Services.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Проверка подлинности по ключу подписки для нескольких служб

>[!WARNING]
> Сейчас некоторые службы **не** поддерживают ключи для нескольких служб: QnA Maker, службы распознавания речи и Пользовательская служба визуального распознавания.

Этот метод проверки подлинности запросов также использует ключ подписки. Основное различие заключается в том, что этот ключ подписки не привязан к конкретной службе, а применяется для проверки подлинности запросов к нескольким службам Cognitive Services. На странице [цен на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) вы найдете сведения о доступности по регионам, поддерживаемых функциях и стоимости.

Ключ подписки предоставляется в заголовке `Ocp-Apim-Subscription-Key` каждого запроса.

[![Видеоролик о ключе подписки на несколько служб для Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Поддерживаемые регионы

Если вы используете ключ подписки для нескольких служб в запросе к `api.cognitive.microsoft.com`, необходимо указать регион в URL-адресе. Например, `westus.api.cognitive.microsoft.com`.

Если вы используете ключ подписки для нескольких служб в запросе к API перевода текстов, необходимо указать регион в заголовке `Ocp-Apim-Subscription-Region`.

Проверка подлинности по ключу для нескольких служб поддерживается в следующих регионах:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Примеры запросов

Вот пример вызова к API Bing для поиска в Интернете:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Вот пример вызова к API перевода текстов:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Проверка подлинности по маркеру проверки подлинности

Некоторые службы Azure Cognitive Services поддерживают или даже требуют маркер проверки подлинности. Сейчас маркеры проверки подлинности поддерживают следующие службы:

* API перевода текстов;
* Службы распознавания речи: REST API преобразования речи в текст;
* Службы распознавания речи: REST API преобразования текста в речь.

>[!NOTE]
> QnA Maker также использует заголовок авторизации, но требует указывать ключ конечной точки. Дополнительные сведения см. в статье [ Quickstart: Get answer from knowledge base using cURL](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md) (Краткое руководство. Получение ответов из базы знаний с помощью cURL).

>[!WARNING]
> Со временем список служб, которые поддерживают маркеры проверки подлинности, может меняться. Обратитесь к документации по API конкретной службы, прежде чем внедрять этот метод проверки подлинности.

Маркер проверки подлинности можно получить в обмен на любой ключ подписки: для одной или для нескольких службы. Маркер проверки подлинности действует в течение 10 минут.

Маркеры проверки подлинности включаются в запрос в заголовке `Authorization`. К значению маркера следует добавить префикс `Bearer`, например так: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Примеры запросов

Используйте этот URL-адрес для обмена ключа подписки на маркер проверки подлинности: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Обмен маркерами поддерживается в следующих регионах с несколькими службами:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Получив маркер проверки подлинности, его следует включать в каждый запрос в заголовке `Authorization`. Вот пример вызова к API перевода текстов:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>См. также

* [Что такое Cognitive Services?](welcome.md)
* [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Создание учетной записи](cognitive-services-apis-create-account.md)
