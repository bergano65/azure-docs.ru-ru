---
title: Тестирование приложения на портале LUIS
titleSuffix: Azure Cognitive Services
description: Используйте службу "Распознавание речи" (LUIS) для постоянной работы над приложением, чтобы усовершенствовать его и улучшить функции распознавания речи.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 628547e8254bb0055cf1f09af50e79b68311a759
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221731"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Тестирование приложения LUIS на портале LUIS

[Testing](luis-concept-test.md) an app is an iterative process. После обучения приложения LUIS протестируйте его с примерами высказываний на предмет правильного распознавания намерений и сущностей. Если распознавание выполняется неправильно, внесите изменения в приложение, а затем обучите его и протестируйте еще раз. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Train before testing

In order to test against the most recent version of the active app, select **Train** from the top menu, before testing. 

## <a name="test-an-utterance"></a>Тестирование высказывания

The test utterance should not be exactly the same as any example utterances in the app. The test utterance should include word choice, phrase length, and entity usage you expect for a user. 

1. Откройте приложение, выбрав его имя на странице **Мои приложения**. 

1. Чтобы открыть выдвигающуюся панель **Test** (Тестирование), щелкните **Test** (Тестировать) на верхней панели приложения.

    > [!div class="mx-imgBorder"]
    > ![Train & Test App page](./media/luis-how-to-interactive-test/test.png)

1. Введите высказывание в текстовом поле и нажмите клавишу ВВОД. На панели **Test** (Тестирование) можно вводить неограниченное количество тестовых высказываний, но только одно высказывание за раз.

1. Высказывание, его основное намерение и оценка добавляются в список высказываний под текстовым полем.

    ![Интерактивное тестирование определяет неправильное намерение](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Проверка оценки

Проверка сведений в результате тестирования выполняется на панели **Inspect** (Проверка). 
 
1. На открытой выдвигающейся панели **Test** (Тестирование) нажмите кнопку **Inspect** (Проверить) рядом с высказыванием, которое нужно сравнить. 

    ![Нажмите кнопку "Инспектировать" для получения дополнительных сведений о результатах теста.](./media/luis-how-to-interactive-test/inspect.png)

1. Появится панель **Inspection** (Проверка). На панели находится намерение с высокой оценкой, а также любые идентифицированные сущности. На панели отображается результат выбранного высказывания.

    ![На панели находится намерение с высокой оценкой, а также любые идентифицированные сущности. На панели отображается результат выбранного высказывания.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Исправление намерения с высокой оценкой

1. Если намерение с высокой оценкой неправильное, нажмите кнопку **Изменить**.

1.  В раскрывающемся списке выберите правильное намерение для высказывания.

    ![Выбор правильного намерения](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Просмотр результатов тональности

Если на странице **[Publish](luis-how-to-publish-app.md#enable-sentiment-analysis)** (Публикация) настроен **анализ тональности**, результаты теста содержат тональность, найденную в высказывании. 

![Изображение панели тестирования с анализом тональности](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Исправление соответствующего намерения шаблона

Если при использовании [шаблонов](luis-concept-patterns.md) высказывание совпало с шаблоном, но было спрогнозировано неправильное намерение, щелкните ссылку **Изменить** в шаблоне, а затем выберите правильное намерение.

## <a name="compare-with-published-version"></a>Сравнение с опубликованной версией

Можно протестировать активную версию приложения с опубликованной версией [конечной точки](luis-glossary.md#endpoint). На панели **Inspect** (Проверка) выберите **Compare with published** (Сравнить с опубликованной). Средства на тестирование на соответствие опубликованной модели вычитаются из суммы квоты на подписку Azure. 

![Сравнение с опубликованной версией](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Просмотр JSON конечной точки на панели тестирования
Чтобы просмотреть JSON конечной точки, возвращенный для сравнения, выберите **Show JSON view** (Показать представление JSON).

![Опубликованный ответ JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Дополнительные параметры на панели тестирования

### <a name="luis-endpoint"></a>Конечная точка LUIS

При наличии нескольких конечных точек LUIS щелкните ссылку **Дополнительные параметры** на панели "Published" (Опубликовано), чтобы изменить конечную точку, используемую для тестирования. Если вы не уверены, какую конечную точку выбрать, выберите значение по умолчанию **Starter_Key**. 

> [!div class="mx-imgBorder"]
> ![Test panel with Additional Settings link highlighted](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)

<!--
###  View Bing Spell Check corrections in test panel

Requirements to view the spelling corrections: 

* Published app
* Bing Spell Check [service key](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). The service key is not stored and needs to be reset for each browser session. 

Use the following procedure to include the [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) service  in the Test pane results. 

1. In the **Test** pane, enter an utterance. When the utterance is predicted, select **[Inspect](#inspect-score)** underneath the utterance you entered. 

1. When the **Inspect** panel opens, select **[Compare with Published](#compare-with-published-version)**. 

1. When the **Published** panel opens, select **[Additional Settings](#additional-settings-in-test-panel)**.

1. In the pop-up dialog, check **Enable Bing Spell Check** and enter the key, then select **Done**. 
    ![Enter Bing Spell Check service key](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Enter a query with an incorrect spelling such as `book flite to seattle` and select enter. The incorrect spelling of the word `flite` is replaced in the query sent to LUIS and the resulting JSON shows both the original query, as `query`, and the corrected spelling in the query, as `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
-->

## <a name="batch-testing"></a>Пакетное тестирование
Ознакомьтесь с [принципами](luis-concept-batch-test.md) пакетного тестирования и узнайте, [как](luis-how-to-batch-test.md) протестировать пакет высказываний.

## <a name="next-steps"></a>Дальнейшие действия

Если тестирование показывает, что приложение LUIS не распознает правильные намерения и сущности, вы можете повысить точность приложения LUIS путем пометки дополнительных высказываний или добавления функций. 

* [Label suggested utterances with LUIS](luis-how-to-review-endpoint-utterances.md) (Добавление меток к предлагаемым фразам в LUIS) 
* [Use features to improve your LUIS app's performance](luis-how-to-add-features.md) (Использование функций для повышения производительности приложения LUIS) 
