---
title: Руководство по Шаблоны — LUIS
titleSuffix: Azure Cognitive Services
description: Используйте шаблоны для улучшения намерения и сущности прогнозирования, в то же время предоставляя меньше примеров высказываний. Шаблон предоставляется в виде примера высказывания шаблона, который включает синтаксис для идентификации сущностей и игнорируемый текст.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 4e4f1787db86378eaeff9df196cc061c42d0ab1e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499008"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Руководство по Добавление шаблонов высказываний в стандартных форматах

В этом руководстве используйте шаблоны для улучшения намерения и сущности прогнозирования, в то же время предоставляя меньше примеров высказываний. Шаблон предоставляется в виде примера высказывания шаблона, который включает синтаксис для идентификации сущностей и игнорируемый текст. Шаблон представляет собой сочетание сопоставления выражений и машинного обучения.  Пример высказывания шаблона вместе с высказываниями намерений дает LUIS лучшее представление о том, какие высказывания соответствуют намерениям. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Импортировать пример приложения 
> * Создавать намерение.
> * Обучение
> * Публикация
> * Получать намерения и сущности из конечной точки.
> * Создавать шаблон.
> * Проверять улучшения прогнозирования для шаблона.
> * Отмечать текст как тот, который нужно игнорировать и вкладывать в шаблон.
> * Использовать панель тестирования для проверки успешности шаблона.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Импортировать пример приложения

Продолжите работу с приложением **HumanResources**, созданным в рамках последнего руководства. 

Выполните следующие действия.

1.  Загрузите и сохраните [JSON-файл приложения](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Импортируйте JSON-файл в новое приложение.

3. Из раздела **Управление** на вкладке **Версии** скопируйте версию и назовите ее `patterns`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. Так как имя версии используется в маршруте URL-адреса, оно не может содержать символы, которые недопустимы в URL-адресе.

## <a name="create-new-intents-and-their-utterances"></a>Создание новых намерений и их высказываний

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. На странице **намерений** выберите **Create new intent** (Создать намерение). 

3. Введите `OrgChart-Manager` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово).

    ![Создание нового сообщения всплывающего окна](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |Who is John W. Smith the subordinate of?|
    |Who does John W. Smith report to?|
    |Who is John W. Smith's manager?|
    |Who does Jill Jones directly report to?|
    |Who is Jill Jones supervisor?|

    [![Снимок экрана LUIS "Добавление новых высказываний к намерениям"](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Снимок экрана LUIS "Добавление новых высказываний к намерениям"")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Не беспокойтесь, если объект keyPhrase помечен в высказываниях намерения, а не сущности Employee. Оба они правильно прогнозированы в тестовой панели и в конечной точке. 

5. В левой области навигации выберите **Intents** (Намерения).

6. Выберите **Create new intent**. (Создать намерение). 

7. Введите `OrgChart-Reports` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово).

8. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |Who are John W. Smith's subordinates?|
    |Who reports to John W. Smith?|
    |Who does John W. Smith manage?|
    |Who are Jill Jones direct reports?|
    |Who does Jill Jones supervise?|

## <a name="caution-about-example-utterance-quantity"></a>Предупреждение о примере количества высказываний

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Обучение

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Публикация

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Получение намерения и сущностей из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `Who is the boss of Jill Jones?`. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Был ли этот запрос успешным? В этом цикле обучения запрос выполнен успешно. Оценки двух главных намерений близки. Так как обучение LUIS не является каждый раз одинаковым, есть много вариантов, где эти две оценки могут инвертироваться на следующем тренировочном цикле. В результате может быть возвращено неправильное намерение. 

Используйте шаблоны, чтобы сделать правильную оценку намерения значительно выше в процентах и выше от следующей самой высокой оценки. 

Не закрывайте это второе окно браузера. Вы продолжите работу с ним далее в руководстве. 

## <a name="template-utterances"></a>Высказывания шаблона
Из-за особенностей предметной области управления персоналом существует несколько общих способов для отправки запросов о связях сотрудников в организациях. Например:

|Высказывания|
|--|
|Who does Jill Jones report to?|
|Who reports to Jill Jones?|

Эти высказывания слишком близки для определения контекстной уникальности каждого из них без использования большого числа примеров высказываний. Благодаря добавлению шаблона для намерения LUIS изучает шаблоны общих высказываний для намерения без использования слишком большого числа примеров высказываний. 

К примерам шаблонов высказываний для этого намерения относятся:

|Примеры высказываний шаблона|Синтаксическое значение|
|--|--|
|Who does {Employee} report to[?]|взаимозаменяем {Employee}, игнорировать [?]}|
|Who reports to {Employee}[?]|взаимозаменяем {Employee}, игнорировать [?]}|

Синтаксис `{Employee}` отмечает расположение сущности в высказывании шаблона, а также указывает, какая это сущность. Необязательный синтаксис `[?]` отмечает слова или знаки препинания, которые являются необязательными. LUIS сопоставляет высказывание, игнорируя необязательный текст внутри скобок.

Хотя синтаксис выглядит как регулярные выражения, он им не является. Поддерживается синтаксис только фигурной (`{}`) и квадратной (`[]`) скобок. Их можно вложить до двух уровней.

Чтобы шаблон соответствовал высказываниям, сущности в высказываниях должны сначала совпадать с сущностями высказывания шаблона. Тем не менее шаблон не помогает прогнозировать сущности, а только намерения. 

**Хотя шаблоны и помогают указать меньше примеров фраз, но если сущности не обнаруживаются, шаблоны не совпадают.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Добавление шаблонов для намерений OrgChart-Manager

1. Выберите функцию **Сборка** в верхнем меню.

2. В левой области навигации в разделе **Повышение производительности приложения** выберите **Шаблоны** в панели навигации слева.

3. Выберите намерение **OrgChart-Manager**, а затем введите следующие высказывания шаблона.

    |Высказывания шаблона|
    |:--|
    |Who is {Employee} the subordinate of[?]|
    |Who does {Employee} report to[?]|
    |Who is {Employee}['s] manager[?]|
    |Who does {Employee} directly report to[?]|
    |Who is {Employee}['s] supervisor[?]|
    |Who is the boss of {Employee}[?]|

    Сущности с ролями используют синтаксис, который включает в себя имя роли, и рассматриваются в [руководстве по улучшению приложения с помощью ролей шаблона](luis-tutorial-pattern-roles.md). 

    При вводе высказывания шаблона LUIS помогает заполнить сущность после того, как будет введена левая фигурная скобка `{`.

    [![Снимок экрана: ввод высказываний шаблона для намерения](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Оставаясь на странице "Шаблоны", выберите намерение **OrgChart-Reports**, а затем введите следующие фразы шаблона.

    |Высказывания шаблона|
    |:--|
    |Who are {Employee}['s] subordinates[?]|
    |Who reports to {Employee}[?]|
    |Who does {Employee} manage[?]|
    |Who are {Employee} direct reports[?]|
    |Who does {Employee} supervise[?]|
    |Who does {Employee} boss[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Запрос конечной точки при использовании шаблонов

Теперь, когда шаблоны добавлены в приложение, можно обучать, публиковать и запрашивать приложения в конечной точке среды выполнения прогноза.

1. Обучите и опубликуйте приложение еще раз.

1. Вернитесь на вкладку браузера с URL-адресом конечной точки.

1. Перейдите в конец URL-адреса и введите `Who is the boss of Jill Jones?` в качестве высказывания. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Прогноз намерения теперь значительно точнее.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Работа с предварительно созданными сущностями и необязательным текстом

В предыдущих высказываниях шаблона в этом руководстве содержалось несколько примеров с необязательным текстом, например использование буквы s в притяжательном падеже (`'s`) и вопросительного знака (`?`). Предположим, что необходимо разрешить текущие и будущие даты в тексте фразы.

Примеры высказываний:

|Намерение|Примеры высказываний с предварительно созданными сущностями и необязательным текстом|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

В каждом из этих примеров используется глагол (`was`, `is`, `will be`), а также дата (`March 3`, `now` и `in a month`), которые служба LUIS должна правильно спрогнозировать. Обратите внимание, что в последних двух примерах используется практически тот же текст. Отличаются в нем только предлоги (`in` и `on`).

Пример шаблона фразы, в котором можно использовать дополнительные сведения. 

|Намерение|Примеры высказываний с предварительно созданными сущностями и необязательным текстом|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


За счет использования синтаксиса с квадратными скобками (`[]`) этот необязательный текст легко добавить в высказывание шаблона и вложить вплоть до второго уровня (`[[]]`). Кроме того, вы можете добавить сущности или текст.


**Вопрос. Почему все первые буквы `w` каждого высказывания в шаблоне написаны в нижнем регистре? Разве регистр не следует выбирать случайным образом?** Регистр высказывания, отправленного клиентским приложением на конечную точку запроса, преобразовывается в нижний. Высказывание шаблона может иметь верхний или нижний регистр. То же самое относится к высказыванием конечной точки. Сравнение всегда выполняется после преобразования в нижний регистр.

**Вопрос. Почему предварительно созданная числовая часть шаблона высказывания March 3 не прогнозируется как число `3` и дата `March 3`?** В зависимости от контекста высказывание шаблона прогнозирует дату буквально (`March 3`) или абстрагировано (`in a month`). Дата может содержать число, но число не обязательно рассматривается как дата. Всегда используйте сущность, которая лучше всего представляет тип, который вы хотите вернуть в результатах прогнозирования JSON.  

**Вопрос. Как насчет плохо сформулированных высказываний, например `Who will {Employee}['s] manager be on March 3?`?** Грамматически разные временные глаголы, например глаголы с разделенными частями `will` и `be`, должны быть добавлены как новое высказывание шаблона. Имеющееся высказывание шаблона не сопоставит эти глаголы. Хотя намерение не изменилось, изменилась расстановка слов в высказывании. Это изменение влияет на прогноз в LUIS. Вы можете [группировать и (или)](#use-the-or-operator-and-groups) глаголы разных времен, чтобы объединять эти фразы. 

**Помните: сначала находятся сущности, а затем сравнивается шаблон.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Изменение имеющегося высказывания шаблона

1. На веб-сайте LUIS выберите **Build** (Создать) в верхнем меню, а затем щелкните **Patterns** (Шаблоны) в меню слева. 

1. Найдите существующий шаблон фразы (`Who is {Employee}['s] manager[?]`) и нажмите кнопку с многоточием (***...***) справа, затем во всплывающем меню выберите **Изменить**. 

1. Измените высказывание шаблона следующим образом: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Добавление новых высказываний шаблона

1. В разделе **Patterns** (Шаблоны) окна **Build** (Создание) добавьте несколько новых высказываний шаблона. В раскрывающемся меню намерений выберите **OrgChart-Manager** и введите каждое из этих высказываний шаблона:

    |Намерение|Примеры высказываний с предварительно созданными сущностями и необязательным текстом|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Обучите приложение.

3. Чтобы открыть панель тестирования, выберите **Test** (Тестировать) в верхней части панели. 

4. Введите несколько тестовых высказываний, чтобы проверить сопоставление шаблона и достаточно ли высока оценка намерения. 

    Когда введете первое высказывание, выберите **Inspect** (Проверить), чтобы просмотреть все результаты прогнозирования. Каждая фраза должна иметь намерение **OrgChart Manager** и должна извлечь значения для объектов сотрудника и datetimeV2.

    |Фраза|
    |--|
    |Who will be Jill Jones manager|
    |who will be jill jones's manager|
    |Who will be Jill Jones's manager?|
    |who will be Jill jones manager on March 3|
    |Who will be Jill Jones manager next Month|
    |Who will be Jill Jones manager in a month?|

Для всех этих высказываний найдены сущности, поэтому они соответствуют тому же шаблону и имеют высокую оценку прогнозирования.

## <a name="use-the-or-operator-and-groups"></a>Использование оператора OR и групп

Некоторые фразы из предыдущего шаблона очень похожи. Используйте синтаксис **групп** `()` и **OR** `|` для уменьшения шаблонных фраз. 

Следующие 2 шаблона можно объединить в один с помощью синтаксиса группы `()` и OR `|`.

|Намерение|Примеры высказываний с предварительно созданными сущностями и необязательным текстом|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Новый шаблон фразы будет выглядеть следующим образом. 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

В нем используется **группа** вокруг необходимого времени глагола и возможный союз `in` и `on` с каналом **OR** между ними. 

1. На странице **Шаблоны** выберите фильтр **OrgChart-Manager**. Сократите список, выполнив поиск `manager`. 

    ![Поиск термина "manager" в шаблонах намерений OrgChart-Manager](./media/luis-tutorial-pattern/search-patterns.png)

1. Сохраните одну версию шаблонной фразы (для внесения изменений на следующем шаге) и удалите другие варианты. 

1. Измените шаблонную фразу следующим образом. 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Обучите приложение.

1. Используйте панель "Проверить" для проверки версий фразы.

    |Фразы для ввода в панель "Проверить"|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Использование привязок в начале и в конце фразы

Синтаксис шаблона поддерживает синтаксис привязок в начале и в конце фразы с помощью символа `^`. Привязки в начале и в конце фразы могут использоваться совместно для определения очень специфических и, возможно, буквальных фраз или отдельно для определения намерений. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве добавляются два намерения для высказываний, которые трудно спрогнозировать с высокой точностью, не имея большого количества примеров высказываний. Добавление шаблонов позволило LUIS лучше прогнозировать намерение со значительно высшей оценкой достоверности. Маркировка сущностей и текст, который нужно игнорировать, позволяют LUIS применять шаблон к более широкому кругу высказываний.

> [!div class="nextstepaction"]
> [Tutorial: Improve app with pattern roles](luis-tutorial-pattern-roles.md) (Руководство. Улучшение приложения с помощью ролей шаблонов)
