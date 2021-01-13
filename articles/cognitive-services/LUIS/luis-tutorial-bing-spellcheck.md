---
title: Исправление слов с ошибками — LUIS
titleSuffix: Azure Cognitive Services
description: Включите исправление слов с ошибками во фрагментах речи, добавив API проверки орфографии Bing версии 7 в запросы конечной точки LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179645"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>Исправление слов с ошибками в Поиск Bing ресурсе

Приложение LUIS можно интегрировать с [Поиск Bing](https://ms.portal.azure.com/#create/Microsoft.BingSearch) , чтобы исправить слова с ошибками в фразы продолжительностью перед тем, как Luis прогнозирует оценку и сущности utterance.

## <a name="create-endpoint-key"></a>Создание ключа конечной точки

Чтобы создать Поиск Bing ресурс в портал Azure, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).

2. В верхнем левом углу щелкните **Создать ресурс**.

3. В поле поиска введите `Bing Search V7` и выберите службу.

4. Справа появится информационная панель, содержащая сведения, включая юридическое уведомление. Выберите **Создать**, чтобы начать процесс создания подписки.

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="Ресурс API Bing для проверки орфографии версии 7":::

5. На следующей панели введите параметры службы. Дождитесь завершения процесса создания службы.

6. После создания ресурса перейдите в колонку **ключи и конечная точка** слева. 

7. Скопируйте один из ключей, которые будут добавлены в заголовок прогнозирующего запроса. Вам потребуется только один из двух ключей.

8. Добавьте ключ в `mkt-bing-spell-check-key` заголовок прогнозирующего запроса.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Добавление ключа в URL-адрес конечной точки
Для каждого запроса, для которого необходимо применить исправление орфографии, запросу конечной точки требуется ключ ресурса Bing проверки орфографии, переданный в параметре заголовка запроса. Можно настроить чат-бот, вызывающий LUIS, или вызывать API конечной точки LUIS напрямую. Независимо от того, как вызывается конечная точка, каждый и каждый вызов должен включать в запрос заголовка необходимые сведения для правильной работы исправлений правописания. Значение ключа необходимо задать с помощью **МКТ-Bing-правописания-Check-Key** .


## <a name="send-misspelled-utterance-to-luis"></a>Отправка фрагментов речи с орфографическими ошибками в LUIS
1. Добавьте в прогнозирующий запрос с ошибкой utterance, который будет отправляться, например "насколько далеко маунтаинн?". В английском языке правильное написание `mountain` — с одной буквой `n`.

2. Служба LUIS передаст ответ с результатом JSON для `How far is the mountain?`. Если API проверки орфографии Bing версии 7 обнаружит орфографическую ошибку, в поле `query` ответа JSON приложения LUIS будет указан исходный запрос, а в поле `alteredQuery` — исправленный запрос, отправленный в LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Пропуск орфографических ошибок

Если вы не хотите использовать службу Поиск Bing API версии 7, необходимо добавить правильную и неправильную орфографию.

Существуют два решения.

* Пример метки фразы продолжительностью, имеющий все различные слова, чтобы LUIS мог изучить правильную орфографию и опечатки. Этот способ требует больше усилий, чем применение проверки орфографии.
* Создайте список фраз со всеми вариациями слова. В этом решении не нужно помечать варианты слов в примере фразы продолжительностью.


> [!div class="nextstepaction"]
> [Подробнее о примерах фрагментов речи](./luis-how-to-add-entities.md)
