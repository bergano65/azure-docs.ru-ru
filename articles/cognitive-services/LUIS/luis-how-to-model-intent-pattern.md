---
title: Шаблоны добавляют точности - LUIS
titleSuffix: Azure Cognitive Services
description: Добавьте шаблоны шаблонов для повышения точности прогнозирования в приложениях Language Understanding (LUIS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311722"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Как добавить шаблоны для повышения точности прогнозирования
После того, как приложение LUIS получает высказывания в конечных точках, используйте [шаблон](luis-concept-patterns.md) для повышения точности прогнозирования для высказываний, которые раскрывают закономерность в порядке слова и выборе слов. Шаблоны используют определенный [синтаксис](luis-concept-patterns.md#pattern-syntax) для указания местоположения: [сущностей,](luis-concept-entity-types.md) [ролей](luis-concept-roles.md)сущностей и факультативного текста.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Шаблоны включают только машино-изученные родители сущности, а не подкомпоненты.

## <a name="adding-example-utterances-as-pattern"></a>Добавление примера высказываний в качестве шаблона

Если вы хотите добавить шаблон для сущности, _самый простой_ способ — создать шаблон со страницы деталей Intent. Это гарантирует, что ваш синтаксис соответствует примеру высказывания.

1. На [портале PREVIEW LUIS](https://preview.luis.ai)выберите приложение со страницы **My Apps.**
1. На странице списка **Intents** выберите имя намерения примера, из которого вы хотите создать высказывание шаблона.
1. На странице «Намерения» выберите строку для примерного высказывания, который вы хотите использовать в качестве высказывания шаблона, а затем выберите **« добавить в качестве шаблона»** из панели контекстных инструментов.

    > [!div class="mx-imgBorder"]
    > ![Скриншот выбора примера высказывания в качестве шаблона на странице умысел.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. В всплывающем окне выберите **Done** на странице **Подтверждения шаблонов.** Вам не нужно определять подкомпоненты, ограничения или дескрипторы сущностей. Вам нужно только перечислить объект, изученный машиной.

    > [!div class="mx-imgBorder"]
    > ![Скриншот подтверждающего примера высказывания в качестве шаблона на странице intent details.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Если вам нужно отобразить шаблон, например, выбрать `[]` текст в качестве факультативного, с (квадратными) скобками, вам нужно сделать это редактирование со **страницы шаблонов.**

1. В панели навигации выберите **Train,** чтобы обучить приложение новому шаблону.

## <a name="add-template-utterance-using-correct-syntax"></a>Добавить высказывание шаблона, используя правильный синтаксис

1. Откройте приложение, выбрав его имя на странице **Мои приложения**, а затем выберите **Patterns** (Шаблоны) на левой панели в разделе **Improve app performance** (Повышение производительности приложений).

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана: список шаблонов](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Выберите нужное намерение для шаблона.

1. В текстовом поле шаблона введите фрагмент речи шаблона и нажмите клавишу ВВОД. При необходимости ввода имени сущности используйте правильный синтаксис сущности шаблона. Начните синтаксис сущности с `{`. Будет выведен список сущностей. Выберите правильную сущность.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана сущности для шаблона](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Если ваша сущность включает [роль,](luis-concept-roles.md)укажите `:`роль с одной толстой `{Location:Origin}`кишки, после имени сущности, например. Список ролей для сущностей появится в списке. Выберите роль и нажмите клавишу ВВОД.

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

## <a name="use-contextual-toolbar"></a>Использование контекстной панели инструментов

Контекстная панель инструментов над списком шаблонов позволяет:

* Поиск шаблонов
* Редактирование шаблона
* Переназначение отдельного шаблона другому намерению
* Переназначение нескольких шаблонов другому намерению
* Удалить-одиночный шаблон
* Удаление нескольких шаблонов
* Фильтрация списка шаблонов по сущности
* Фильтр-шаблон-список по намерениям
* Удаление фильтра сущности или намерения
* Добавление шаблона на основе существующего фрагмента речи на странице сущности или намерения

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [построить шаблон](luis-tutorial-pattern.md) с pattern.any и роли с учебником.
* Сведения об [обучении](luis-how-to-train.md) приложений.
