---
title: Извлечение ключевой фразы с помощью REST API Анализа текста
titleSuffix: Azure Cognitive Services
description: Узнайте, как извлечь ключевые фразы с помощью REST API Анализа текста из Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: 1d94cff3eb3299692fc4172f5bb5211532ef1002
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697819"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Пример Извлечение ключевых фраз с помощью Анализа текста

[API извлечения ключевых фраз](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) оценивает неструктурированный текст и для каждого документа JSON возвращает список ключевых фраз.

Эта возможность полезна, если необходимо быстро определить основные тезисы в коллекции документов. Например, данный входной текст "Еда была вкусной, и были замечательные сотрудники", служба вернет основные тезисы в записи: "еда" и "замечательные сотрудники".

Дополнительные сведения см. в [списке поддерживаемых языков](../text-analytics-supported-languages.md).

> [!TIP]
> API анализа текста также предоставляет образ контейнера Docker под управлением Linux для извлечения ключевых фраз, поэтому вы можете [установить и запустить контейнер API анализа текста](text-analytics-how-to-install-containers.md) в непосредственной близости к своим данным.

## <a name="preparation"></a>Подготовка

Извлечение ключевых фраз лучше всего работает с большими фрагментами текста, в отличие от анализа тональности, который эффективнее работает на маленьких блоках текста. Для получения наилучших результатов обеих операций советуем реструктуризировать входные данные соответствующим образом.

Необходимо иметь документы JSON в следующем формате: "идентификатор","текст","язык".

Документ должен содержать менее 5120 символов и иметь до 1000 элементов (идентификаторов) в коллекции. Коллекция передается в тексте запроса. Ниже приведен пример иллюстрации содержимого, которое можно отправлять для извлечения ключевых фраз.

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Шаг 1.: Структурирование запроса

Дополнительные сведения об определении запроса см. в статье [How to call the Text Analytics API](text-analytics-how-to-call-api.md) (Как вызвать REST API службы "Анализ текста"). Для удобства повторим следующие моменты.

+ Создайте запрос **POST**. Ознакомьтесь с документацию по API для этого запроса: [API для поиска ключевых фраз](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Задайте конечную точку HTTP для извлечения ключевых фраз с помощью ресурса Анализа текста в Azure или экземпляра [контейнера Анализа текста](text-analytics-how-to-install-containers.md). Он должен включать в себя ресурс `/keyPhrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`.

+ Задайте заголовок запроса, чтобы включить [ключ доступа](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) для операций анализ текста.

+ В тексте запроса укажите набор документов JSON, которые подготовлены для этого анализа.

> [!Tip]
> Используйте [Postman](text-analytics-how-to-call-api.md) или откройте **консоль тестирования API** в [документации](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6), чтобы структурировать запрос и передать его (используя метод POST) в службу.

## <a name="step-2-post-the-request"></a>Шаг 2. Передача запроса

Анализ выполняется при получении запроса. Сведения о размере и количестве запросов, которые можно отправлять в минуту и секунду, см. в разделе об [ограничениях данных](../overview.md#data-limits).

Не забывайте, что эта служба работает без отслеживания состояния. Данные не хранятся в учетной записи. Результаты возвращаются немедленно в ответе.

## <a name="step-3-view-results"></a>Шаг 3. Просмотр результатов

Все запросы POST вернут ответ в формате JSON с идентификаторами и обнаруженными свойствами.

Вывод возвращается немедленно. Можно выполнить потоковую передачу результатов в приложение, которое принимает JSON, или сохранить выходные данные в файле локальной системы, а затем импортировать их в приложение, которое дает возможность сортировки, поиска данных и управления ими.

Далее показан пример выходных данных для извлечения ключевой фразы:

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

Как уже отмечалось, анализатор находит и отбрасывает слова, которые не представляют смысла и сохраняет одиночные термины или фразы, которые могут быть темой или объектом предложения.

## <a name="summary"></a>Сводка

В этой статье рассматриваются основные понятия и рабочий процесс извлечения ключевой фразы с помощью Анализа текста в Cognitive Services. В разделе "Сводка" сделайте следующее.

+ [API извлечения ключевых фраз](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) доступен для некоторых языков.
+ В тексте запроса передаются документы JSON, которые содержат идентификатор, текст и код языка.
+ Запрос POST передается в конечную точку `/keyphrases`, используя личный [ключ доступа и конечную точку](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource), допустимые для вашей подписки.
+ Выходные данные ответа, состоящие из ключевых слов и фраз для каждого идентификатора документа, могут передаваться в любое приложение, поддерживающее формат JSON, например Microsoft Office Excel и Power BI.

## <a name="see-also"></a>См. также

 API анализа текста: [общие сведения](../overview.md) и [вопросы и ответы](../text-analytics-resource-faq.md)</br>
 [Анализ текста](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [API анализа текста](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
