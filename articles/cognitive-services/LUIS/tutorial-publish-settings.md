---
title: Руководство по параметрам публикации. Распознавание речи
description: В этом руководстве вы измените параметры публикации, чтобы улучшить прогнозирование/
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: d976d06fbf20ea0e64fa01bd94658a27d680bc86
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588801"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Руководство по  добавлению анализа тональности как параметра публикации

В этом руководстве содержатся сведения об изменении параметров публикации для извлечения анализа тональности и последующего выполнения запроса к конечной точке Распознавания речи, чтобы увидеть возвращенную тональность речевого фрагмента пользователя.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Добавление анализа тональности как параметра публикации
> * Получение тональности речевого фрагмента из опубликованной конечной точки

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Анализ тональности — параметр публикации

Ниже приведены примеры фраз, передающие тональность.

|Мнение|Оценка|Фраза|
|:--|:--|:--|
|Негативная|0,01 |Пицца была ужасной.|
|Позитивная|0,97 |Сырная пицца была великолепной.|

Анализ тональности — это параметр публикации, который применяется к каждому высказыванию. После установки приложение будет возвращать тональность речевого фрагмента, исключая необходимость добавления меток к данным.

Так как это параметр публикации, он не будет отмечен на страницах намерений или сущностей. Он будет отображаться в области [интерактивного тестирования](luis-interactive-test.md#view-sentiment-results) или при тестировании на странице по URL-адресу конечной точки.

## <a name="download-json-file-for-app"></a>Скачивание JSON-файла для приложения

Скачайте и сохраните [JSON-файл приложения](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

## <a name="import-json-file-for-app"></a>Импорт JSON-файла для приложения

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Обучение приложения

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Настройка приложения для включения анализа тональности

1. Выберите **Опубликовать** в верхнем меню. Анализ тональности — параметр публикации.

1. Последовательно выберите **Production slot** (Рабочий слот), **Change settings** (Изменить параметры).
1. Задайте для параметра "Sentiment Analysis" (Анализ тональности) значение **On** (Включено).

    ![Добавление анализа тональности как параметра публикации](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Получение тональности речевого фрагмента из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Перейдите в конец URL-адреса в адресной строке и замените _YOUR_QUERY_HERE_ на следующее:

    `Deliver 2 of the best cheese pizzas ever!!!`

    Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `OrderPizza` с извлечением анализа тональности.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    Анализ тональности является положительным при оценке 86 %.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Дополнительные сведения

* Анализ тональности предоставляется службой [Анализ текста](../Text-Analytics/index.yml) Cognitive Services. Эта функция ограничивается [поддерживаемыми языками](luis-language-support.md#languages-supported) Анализа текста.
* [Обучение активной версии приложения LUIS](luis-how-to-train.md)
* [Как опубликовать предложение](luis-how-to-publish-app.md)
* [Тестирование приложения LUIS на портале LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Дальнейшие действия
В рамках этого руководства вы добавили анализ тональности как параметр публикации, чтобы извлечь значения тональности из всего высказывания.

> [!div class="nextstepaction"]
> [Проверка фрагментов речи конечной точки в приложении для управления персоналом](luis-tutorial-review-endpoint-utterances.md)

