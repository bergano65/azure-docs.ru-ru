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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: cbd8ad73ff4a03790dd6b22d5ce33acf09a2b125
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491359"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Добавление шаблонов для повышения точности прогнозов
После того как приложение LUIS получит конечную точку фразы продолжительностью, используйте [шаблон](luis-concept-patterns.md) , чтобы улучшить точность прогнозов для фразы продолжительностью, чтобы показать шаблон в порядке слов и выбрать слово. В шаблонах используется специальный [синтаксис](luis-concept-patterns.md#pattern-syntax) для указания расположения [сущностей](luis-concept-entity-types.md), [ролей](luis-concept-roles.md)сущностей и необязательного текста.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Добавление фрагмента речи шаблона для создания шаблона
1. Откройте приложение, выбрав его имя на странице **Мои приложения**, а затем выберите **Patterns** (Шаблоны) на левой панели в разделе **Improve app performance** (Повышение производительности приложений).

    ![Снимок экрана: список шаблонов](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Выберите нужное намерение для шаблона. 

    ![Выбор намерения](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. В текстовом поле шаблона введите фрагмент речи шаблона и нажмите клавишу ВВОД. При необходимости ввода имени сущности используйте правильный синтаксис сущности шаблона. Начните синтаксис сущности с `{`. Будет выведен список сущностей. Выберите правильную сущность, а затем нажмите клавишу ВВОД. 

    ![Снимок экрана сущности для шаблона](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Если сущность содержит [роль](luis-concept-roles.md), укажите ее с одним двоеточием `:`после имени сущности, например `{Location:Origin}`. Список ролей для сущностей появится в списке. Выберите роль и нажмите клавишу ВВОД. 

    ![Снимок экрана сущности с ролью](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    После выбора правильной сущности завершите ввод шаблона, а затем нажмите клавишу ВВОД. Закончив вводить шаблоны, выполните [обучение](luis-how-to-train.md) приложения.

    ![Снимок экрана: введенный шаблон с обоими типами сущностей](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создать шаблон](luis-tutorial-pattern.md) с помощью шаблона. любые роли и с учебником.
* Сведения об [обучении](luis-how-to-train.md) приложений.
