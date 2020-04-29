---
title: Шаблоны добавляют точность — LUIS
titleSuffix: Azure Cognitive Services
description: Добавьте шаблоны шаблонов для повышения точности прогнозов в приложениях Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76311722"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Добавление шаблонов для повышения точности прогнозов
После того как приложение LUIS получит конечную точку фразы продолжительностью, используйте [шаблон](luis-concept-patterns.md) , чтобы улучшить точность прогнозов для фразы продолжительностью, чтобы показать шаблон в порядке слов и выбрать слово. В шаблонах используется специальный [синтаксис](luis-concept-patterns.md#pattern-syntax) для указания расположения [сущностей](luis-concept-entity-types.md), [ролей](luis-concept-roles.md)сущностей и необязательного текста.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Шаблоны включают только те элементы, которые изучены компьютером, а не подкомпоненты.

## <a name="adding-example-utterances-as-pattern"></a>Добавление примера фразы продолжительностью в качестве шаблона

Чтобы добавить шаблон для сущности, _проще всего_ создать шаблон на странице «сведения о намерении». Это обеспечит совпадение синтаксиса с примером utterance.

1. На [портале предварительной версии Luis](https://preview.luis.ai)выберите приложение на странице **Мои приложения** .
1. На странице список **целей** выберите имя цели в примере utterance, из которого нужно создать шаблон utterance.
1. На странице сведения о намерениях выберите строку для примера utterance, которую нужно использовать в качестве шаблона utterance, а затем выберите **+ Добавить как шаблон** на панели инструментов контекста.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с выбором примера utterance как шаблон шаблона на странице сведений о намерениях.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Во всплывающем окне выберите **Готово** на странице **Подтверждение шаблонов** . Не нужно определять подкомпоненты, ограничения или дескрипторы сущностей. Вам нужно только получить список известных для компьютера сущностей.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с подтверждением примера utterance как шаблон шаблона на странице сведений о намерениях.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Если необходимо изменить шаблон, например, выбрав текст как необязательный, с `[]` квадратными скобками необходимо внести это изменение на странице « **шаблоны** ».

1. На панели навигации выберите **обучение** , чтобы обучить приложение с новым шаблоном.

## <a name="add-template-utterance-using-correct-syntax"></a>Добавить шаблон utterance с правильным синтаксисом

1. Откройте приложение, выбрав его имя на странице **Мои приложения**, а затем выберите **Patterns** (Шаблоны) на левой панели в разделе **Improve app performance** (Повышение производительности приложений).

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана: список шаблонов](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Выберите нужное намерение для шаблона.

1. В текстовом поле шаблона введите фрагмент речи шаблона и нажмите клавишу ВВОД. При необходимости ввода имени сущности используйте правильный синтаксис сущности шаблона. Начните синтаксис сущности с `{`. Будет выведен список сущностей. Выберите правильную сущность.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана сущности для шаблона](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Если сущность содержит [роль](luis-concept-roles.md), укажите ее с одним двоеточием `:`, после имени сущности, например. `{Location:Origin}` Список ролей для сущностей появится в списке. Выберите роль и нажмите клавишу ВВОД.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана сущности с ролью](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    После выбора правильной сущности завершите ввод шаблона, а затем нажмите клавишу ВВОД. Закончив вводить шаблоны, выполните [обучение](luis-how-to-train.md) приложения.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана: введенный шаблон с обоими типами сущностей](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Обучение приложения после изменения модели с шаблонами
После добавления, изменения, удаления или переназначения шаблона выполните [обучение](luis-how-to-train.md) и [публикацию](luis-how-to-publish-app.md) приложения, чтобы применить изменения к запросам конечной точки.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Использовать контекстную панель инструментов

Контекстная панель инструментов над списком «шаблоны» позволяет выполнять следующие задачи:

* Поиск шаблонов
* Редактирование шаблона
* Переназначение отдельного шаблона другому намерению
* Переназначение нескольких шаблонов другому намерению
* Удалить один шаблон
* Удаление нескольких шаблонов
* Фильтрация списка шаблонов по сущности
* Фильтр-шаблон — список по намерениям
* Удаление фильтра сущности или намерения
* Добавление шаблона на основе существующего фрагмента речи на странице сущности или намерения

## <a name="next-steps"></a>Следующие шаги

* Узнайте, как [создать шаблон](luis-tutorial-pattern.md) с помощью шаблона. любые роли и с учебником.
* Сведения об [обучении](luis-how-to-train.md) приложений.
