---
title: Глоссарий — LUIS
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

Активная версия LUIS — это версия, которая принимает все изменения, вносимые в модель. Если вы хотите внести изменения в версию, которая не является активной, на портале [Luis](luis-reference-regions.md) необходимо сначала задать эту версию как активную.

## <a name="authoring"></a>Разработка

Разработка — это возможность создания, управления и развертывания [приложения Luis](#luis-app)с помощью портала [Luis](luis-reference-regions.md) или [API-интерфейсов разработки](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Ключ разработки

Прежнее название — программный ключ. Используется для разработки приложения. Не используется для запросов конечной точки рабочего уровня. Дополнительные сведения см. в разделе об [ограничениях ключа](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a>JSON-файл пакетного теста

Пакетное тестирование — это возможность проверить модель текущего приложения LUIS с помощью единообразного и известного тестового набора пользователей фразы продолжительностью. Пакетный тест определяется в [отформатированном файле JSON](luis-concept-batch-test.md#batch-file-format).

См. также:
* [Основные понятия](luis-concept-batch-test.md)
* [Практические руководства](luis-how-to-batch-test.md)
* [Руководство](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a>Участник совместной работы

Участник, [ответственный](#owner) за совместную работу, не является владельцем приложения, но имеет те же разрешения на добавление, изменение и удаление целей, сущностей, фразы продолжительностью.

## <a name="contributor"></a>Участник

Участник — это то же самое, что и [Совместная работа](#collaborator).

## <a name="descriptor"></a>Descriptor

Дескриптор — это [функция](#features) , применяемая к модели во время обучения, включая [списки фраз](#phrase-list) и [сущности](#entity). 

## <a name="domain"></a>Предметная область

В контексте LUIS **предметная область** — это область знаний. Предметная область относится к области знаний приложения. Это может быть общая область, например приложение для туристического агентства. Приложение для туристического агентства также может быть предназначено только для работы с определенной информацией, используемой в компании, например с конкретными географическими расположениями, языками и услугами.

## <a name="endpoint"></a>Конечная точка

На URL-адрес [конечной точки LUIS](https://go.microsoft.com/fwlink/?linkid=2092356) отправляются запросы LUIS после разработки и публикации [приложения LUIS](#luis-app). В URL-адресе конечной точки содержится регион опубликованного приложения, а также идентификатор приложения. Конечную точку можно найти на странице **[Keys and endpoints](luis-how-to-azure-subscription.md)** (Ключи и конечные точки) вашего приложения. Кроме того, URL-адрес конечной точки можно получить из API [получения сведений о приложении](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37).

## <a name="entity"></a>Сущность

[Сущности](luis-concept-entity-types.md) — это важные слова в [высказываниях](luis-concept-utterance.md), которые описывают информацию, относящуюся к [намерению](luis-concept-intent.md), и иногда они необходимы для намерения. Сущность фактически является типом данных в LUIS.

## <a name="f-measure"></a>F-мера

В [пакетном тестировании](luis-interactive-test.md#batch-testing) это измерение точности теста.

## <a name="false-negative"></a>Ложное отрицательное (FN)

В [пакетном тестировании](luis-interactive-test.md#batch-testing) точка данных представляет высказывания, в которых приложение неверно спрогнозировало отсутствие целевого намерения или сущности.

## <a name="false-positive"></a>Ложный положительный результат (FP)

В [пакетном тестировании](luis-interactive-test.md#batch-testing) точка данных представляет высказывания, в которых приложение неверно спрогнозировало наличие целевого намерения или сущности.

## <a name="features"></a>Свойства

В машинном обучении [свойство](luis-concept-feature.md) — это отличительный признак или атрибут данных, наблюдаемых системой.

## <a name="intent"></a>Намерение

[Намерение](luis-concept-intent.md) представляет задачу или действие, которое хочет выполнить пользователь. Это цель, выраженная во введенных пользователем данных, например бронирование авиабилета, оплата счета или поиск новостной статьи. В LUIS прогнозирование намерения основывается на всем высказывании. Сущности, для сравнения, — это части высказывания.

## <a name="labeling"></a>Пометка

Пометка или маркировка — это процесс связывания слова или фразы в [utterance](#utterance) намерений с [сущностью](#entity) (DataType).

## <a name="luis-app"></a>Приложение LUIS

LUIS приложение — это набор языковых моделей для обработки естественного языка, включая [удержания](#intent), [сущности](#entity)и помеченные [фразы продолжительностью](#utterance).

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

Публикация означает, что LUIS активная версия доступна либо в промежуточной, либо в рабочей [конечной точке](#endpoint).  

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

Бесплатный ключ для использования при первом запуске с помощью LUIS.

## <a name="structure"></a>Дереве

Добавьте структуру в сущность, занятую компьютером, чтобы предоставить подкомпоненты с дескрипторами (функциями) и ограничениями (регулярными выражениями или списками сущностей).

## <a name="subscription-key"></a>Ключ подписки

Ключ подписки — это ключ **конечной точки прогнозирования** , связанный со службой Luis [, созданной в Azure](luis-how-to-azure-subscription.md). Этот ключ не является [ключом разработки](#programmatic-key). Имеющийся ключ конечной точки следует использовать для запросов конечной точки. Не используйте для этой цели ключ разработки. Текущий ключ конечной точки можно увидеть в URL-адресе конечной точки в нижней части страницы [**Keys and endpoints**](luis-how-to-azure-subscription.md) (Ключи и конечные точки) на веб-сайте [LUIS](luis-reference-regions.md). Это значение **ключ-подписка** пары "имя-значение".

## <a name="test"></a>Тест

[Тестирование](luis-interactive-test.md#test-your-app) приложения LUIS означает передачу высказывания в LUIS и просмотр результатов JSON.

## <a name="timezoneoffset"></a>Смещение часового пояса

Конечная точка содержит timezoneOffset. Это число минут, добавляемое в предварительно созданную сущность datetimeV2 или удаляемое из нее. Например, для высказывания "what time is it now?" возвращаемой сущностью datetimeV2 является текущее время для запроса клиента. Если запрос клиента поступает из бота или другого приложения, не соответствующего пользователю бота, следует передать смещение между ботом и пользователем.

См. статью [Изменение часового пояса предварительно созданной сущности datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Токен
Токен является наименьшей единицей, которая может быть помечена в сущности. Разметка основана на [языке и региональных параметрах](luis-language-support.md#tokenization).

## <a name="train"></a>Обучение

Обучение — это процесс обучения LUIS о любых изменениях в активной версии с момента последнего обучения.

## <a name="true-negative"></a>Истинный отрицательный результат (TN)

В [пакетном тестировании](luis-interactive-test.md#batch-testing) точка данных представляет высказывания, в которых приложение правильно спрогнозировало отсутствие целевого намерения или сущности.

## <a name="true-positive"></a>Истинный положительный результат (TP)

В [пакетном тестировании](luis-interactive-test.md#batch-testing) точка данных представляет высказывания, в которых приложение правильно спрогнозировало наличие целевого намерения или сущности.

## <a name="utterance"></a>Высказывание

Высказывание — это фраза на естественном языке, например "book 2 tickets to Seattle next Tuesday". Примеры высказываний добавляются в намерение.

## <a name="version"></a>Версия

[Версия](luis-how-to-manage-versions.md) LUIS — это конкретная модель данных, связанная с идентификатором приложения LUIS и опубликованной конечной точкой. У каждого приложения LUIS есть по меньшей мере одна версия.
