---
title: Glossary - LUIS
titleSuffix: Azure Cognitive Services
description: В глоссарии объясняются термины, с которыми вы можете столкнуться при работе со службой API LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325948"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Основные понятия и определения глоссария Интеллектуальной службы распознавания речи (LUIS)
В глоссарии Интеллектуальной службы распознавания речи (LUIS) объясняются термины, с которыми вы можете столкнуться при работе со службой API LUIS.

## <a name="active-version"></a>Активная версия

Активная версия LUIS — это версия, которая принимает все изменения, вносимые в модель. In the [LUIS](luis-reference-regions.md) portal, if you want to make changes to a version that is not the active version, you need to first set that version as active.

## <a name="authoring"></a>Разработка

Authoring is the ability to create, manage and deploy a [LUIS app](#luis-app), either using the [LUIS](luis-reference-regions.md) portal or the [authoring APIs](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Ключ разработки

Прежнее название — программный ключ. Используется для разработки приложения. Не используется для запросов конечной точки рабочего уровня. Дополнительные сведения см. в разделе об [ограничениях ключа](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a>Batch test JSON file

Batch testing is the ability to validate a current LUIS app's model with a consistent and known test set of user utterances. The batch test is defined in a [JSON formatted file](luis-concept-batch-test.md#batch-file-format).

См. также:
* [Основные понятия](luis-concept-batch-test.md)
* [How-to](luis-how-to-batch-test.md)
* [Руководство](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a>Участник совместной работы

A collaborator/contributor, is not the [owner](#owner) of the app, but has the same permissions to add, edit, and delete the intents, entities, utterances.

## <a name="contributor"></a>Contributor

A contributor is the same thing as a [collaborator](#collaborator).

## <a name="descriptor"></a>Descriptor

A descriptor is a [feature](#features) applied to a model at training time, including [phrase lists](#phrase-list) and [entities](#entity). 

## <a name="domain"></a>Предметная область

В контексте LUIS **предметная область** — это область знаний. Предметная область относится к области знаний приложения. Это может быть общая область, например приложение для туристического агентства. Приложение для туристического агентства также может быть предназначено только для работы с определенной информацией, используемой в компании, например с конкретными географическими расположениями, языками и услугами.

## <a name="endpoint"></a>Конечная точка

На URL-адрес [конечной точки LUIS](https://go.microsoft.com/fwlink/?linkid=2092356) отправляются запросы LUIS после разработки и публикации [приложения LUIS](#luis-app). В URL-адресе конечной точки содержится регион опубликованного приложения, а также идентификатор приложения. Конечную точку можно найти на странице **[Keys and endpoints](luis-how-to-azure-subscription.md)** (Ключи и конечные точки) вашего приложения. Кроме того, URL-адрес конечной точки можно получить из API [получения сведений о приложении](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37).

## <a name="entity"></a>Сущность

[Сущности](luis-concept-entity-types.md) — это важные слова в [высказываниях](luis-concept-utterance.md), которые описывают информацию, относящуюся к [намерению](luis-concept-intent.md), и иногда они необходимы для намерения. Сущность фактически является типом данных в LUIS.

## <a name="f-measure"></a>F-мера

В [пакетном тестировании](luis-interactive-test.md#batch-testing) это измерение точности теста.

## <a name="false-negative"></a>False negative (FN)

В [пакетном тестировании](luis-interactive-test.md#batch-testing) точка данных представляет высказывания, в которых приложение неверно спрогнозировало отсутствие целевого намерения или сущности.

## <a name="false-positive"></a>False positive (FP)

В [пакетном тестировании](luis-interactive-test.md#batch-testing) точка данных представляет высказывания, в которых приложение неверно спрогнозировало наличие целевого намерения или сущности.

## <a name="features"></a>Свойства

В машинном обучении [свойство](luis-concept-feature.md) — это отличительный признак или атрибут данных, наблюдаемых системой.

## <a name="intent"></a>Намерение

[Намерение](luis-concept-intent.md) представляет задачу или действие, которое хочет выполнить пользователь. Это цель, выраженная во введенных пользователем данных, например бронирование авиабилета, оплата счета или поиск новостной статьи. В LUIS прогнозирование намерения основывается на всем высказывании. Сущности, для сравнения, — это части высказывания.

## <a name="labeling"></a>Пометка

Labeling, or marking, is the process of associating a word or phrase in an intent's [utterance](#utterance) with an [entity](#entity) (datatype).

## <a name="luis-app"></a>Приложение LUIS

A LUIS app is a collection of language models for natural language processing including [intents](#intent), [entities](#entity), and labeled [utterances](#utterance).

## <a name="owner"></a>Владелец

У каждого приложения есть один владелец. Это человек, создавший приложение. Владелец может добавлять [участников совместной работы](#collaborator).

## <a name="pattern"></a>Шаблоны
Предыдущий компонент "Шаблон" заменен компонентом [Шаблоны](luis-concept-patterns.md). Шаблоны позволяют повышать точность прогнозирования за счет предоставления меньшего количества обучающих примеров.

## <a name="phrase-list"></a>Список фраз

В [список фраз](luis-concept-feature.md) входит группа значений (слов или фраз), принадлежащих одному классу и обрабатываемых аналогичным образом (например, названия городов или продуктов). Список взаимозаменяемых слов считается списком синонимов.

## <a name="prebuilt-domains"></a>Предварительно созданная предметная область

[Предварительно созданная предметная область](luis-how-to-use-prebuilt-domains.md) — это приложение LUIS, настроенное для определенной предметной области, такой как домашняя автоматика (HomeAutomation) или бронирование мест в ресторане (RestaurantReservation). Для каждой предметной области настраиваются намерения, высказывания и сущности.

## <a name="prebuilt-entity"></a>Предварительно созданная сущность

[Предварительно созданная сущность](luis-prebuilt-entities.md) — это сущность, предоставляемая LUIS для общих типов сведений, таких как число, URL-адрес, адрес электронной почты. Предварительно созданную сущность можно добавить в приложение.

## <a name="precision"></a>Точность
В [пакетном тестировании](luis-interactive-test.md#batch-testing) точность (также называемая положительным значением прогнозирования) представляет собой долю соответствующих высказываний в извлеченных высказываниях.

## <a name="programmatic-key"></a>Программный ключ

Переименован в [ключ разработки](#authoring-key).

## <a name="publish"></a>Публикация

Publishing means making a LUIS active version available on either the staging or production [endpoint](#endpoint).  

## <a name="quota"></a>Квота

Квота LUIS — это ограничение [уровня подписки Azure](https://aka.ms/luis-price-tier). Квота LUIS может быть ограничена как количеством запросов в секунду (состояние HTTP 429), так и общим количеством запросов в месяц (состояния HTTP 403).

## <a name="recall"></a>Отзыв
В [пакетном тестировании](luis-interactive-test.md#batch-testing) отзыв (также известный как чувствительность) представляет собой возможность LUIS, которая состоит в подготовке к использованию.

## <a name="semantic-dictionary"></a>Семантический словарь
Семантический словарь приведен на станице сущности списка, а также на странице списка фраз. Семантический словарь содержит перелагаемые варианты слов на основе текущей области действия.

## <a name="sentiment-analysis"></a>Анализ тональности
В результате анализа тональности выводятся положительные или отрицательные значения высказываний, предоставленных службой [анализа текста](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Подготовка речи

Функция подготовки речи позволяет подготовить службу распознавания речи в модели LUIS.

## <a name="spelling-correction"></a>Исправление орфографических ошибок

Включите средство проверки орфографии Bing для исправления неправильно введенных слов в высказывании перед прогнозированием.

## <a name="starter-key"></a>Ключ для начала разработки

A free key to use when first starting out using LUIS.

## <a name="structure"></a>Structure

Add structure to a machine-learned entity to provide subcomponents with descriptors (features) and constraints (regular expression or list entities).

## <a name="subscription-key"></a>Ключ подписки

The subscription key is the **prediction endpoint** key associated with the LUIS service [you created in Azure](luis-how-to-azure-subscription.md). Этот ключ не является [ключом разработки](#programmatic-key). Имеющийся ключ конечной точки следует использовать для запросов конечной точки. Не используйте для этой цели ключ разработки. Текущий ключ конечной точки можно увидеть в URL-адресе конечной точки в нижней части страницы [**Keys and endpoints**](luis-how-to-azure-subscription.md) (Ключи и конечные точки) на веб-сайте [LUIS](luis-reference-regions.md). Это значение **ключ-подписка** пары "имя-значение".

## <a name="test"></a>Тест

[Тестирование](luis-interactive-test.md#test-your-app) приложения LUIS означает передачу высказывания в LUIS и просмотр результатов JSON.

## <a name="timezoneoffset"></a>Смещение часового пояса

Конечная точка содержит timezoneOffset. Это число минут, добавляемое в предварительно созданную сущность datetimeV2 или удаляемое из нее. Например, для высказывания "what time is it now?" возвращаемой сущностью datetimeV2 является текущее время для запроса клиента. Если запрос клиента поступает из бота или другого приложения, не соответствующего пользователю бота, следует передать смещение между ботом и пользователем.

См. статью [Изменение часового пояса предварительно созданной сущности datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Токен
Токен является наименьшей единицей, которая может быть помечена в сущности. Разметка основана на [языке и региональных параметрах](luis-language-support.md#tokenization).

## <a name="train"></a>Обучение

Training is the process of teaching LUIS about any changes to the active version since the last training.

## <a name="true-negative"></a>Истинный отрицательный результат (TN)

В [пакетном тестировании](luis-interactive-test.md#batch-testing) точка данных представляет высказывания, в которых приложение правильно спрогнозировало отсутствие целевого намерения или сущности.

## <a name="true-positive"></a>Истинный положительный результат (TP)

В [пакетном тестировании](luis-interactive-test.md#batch-testing) точка данных представляет высказывания, в которых приложение правильно спрогнозировало наличие целевого намерения или сущности.

## <a name="utterance"></a>Высказывание

Высказывание — это фраза на естественном языке, например "book 2 tickets to Seattle next Tuesday". Примеры высказываний добавляются в намерение.

## <a name="version"></a>Версия

[Версия](luis-how-to-manage-versions.md) LUIS — это конкретная модель данных, связанная с идентификатором приложения LUIS и опубликованной конечной точкой. У каждого приложения LUIS есть по меньшей мере одна версия.
