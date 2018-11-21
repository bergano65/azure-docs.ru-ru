---
title: Добавление предварительно созданных сущностей в модели Conversation Learner в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Сведения о добавлении предварительно созданных сущностей в модели Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cdd9ad16096c85db21829840b2bfd7acaced5942
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683514"
---
# <a name="how-to-add-pre-built-entities"></a>Добавление предварительно созданных сущностей
В этом руководстве описано, как добавить предварительно созданные сущности в модель Conversation Learner.

## <a name="video"></a>Видео

[![Предварительная версия руководства 7](https://aka.ms/cl-tutorial-07-preview)](https://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>Требования
Для работы с этим руководством требуется запущенный бот обучения общего назначения.

    npm run tutorial-general

## <a name="details"></a>Сведения

Предварительно созданные сущности распознают общие типы сущностей, например числа, даты, денежные суммы и другие.  В отличие от настраиваемых сущностей, они готовы к работе и не требуют обучения.  Также, в отличие от настраиваемых сущностей, их поведение нельзя изменить.  По умолчанию предварительно созданные сущности поддерживают несколько значений — то есть в памяти бота будет накапливаться каждый идентифицированный экземпляр сущности.

## <a name="steps"></a>Действия

### <a name="create-the-model"></a>Создание модели

1. В пользовательском веб-интерфейсе щелкните "Новая модель".
2. В поле "Имя" введите BuiltInEntities. Затем нажмите кнопку "Создать".

### <a name="create-an-entity"></a>Создание сущности

1. Щелкните "Сущности", затем "Создать сущность".
2. Щелкните раскрывающийся список EntityType и выберите datetimev2.
    - Программируемые и отрицаемые параметры недоступны, так как они не применяются к предварительно созданным сущностям.
3. Щелкните Создать.

![](../media/tutorial7_entities_a.PNG)

### <a name="create-two-actions"></a>Создание двух действий

1. Щелкните "Actions" (Действия) и "New Action" (Новое действие).
1. В поле Response (Ответ) введите The date is $builtin-datetimev2.
1. В поле Required Entities (Обязательные сущности) введите $builtin-datetimev2.
1. Щелкните Создать.

![](../media/tutorial7_actions_a.PNG)

Теперь создайте второе действие.

1. Выберите "Действия", затем "Создать действие", чтобы создать второе действие.
1. В поле "Ответ" введите "What's the date?" (Какая дата?).
1. В поле Disqualifying Entities (Блокирующие сущности) введите $builtin-datetimev2.
1. Щелкните Создать.

![](../media/tutorial7_actions2_a.PNG)

Теперь у вас есть два действия.

### <a name="train-the-bot"></a>Обучение бота

1. Щелкните "Train Dialogs" (Обучение диалогам), а затем — "New Train Dialog" (Обучение новому диалогу).
2. Введите "hello" (привет).
3. Щелкните Score Actions (Оценка действий) и выберите "What's the date?" (Какая дата?).
2. Введите "today" (сегодня). 
    - Обратите внимание, что ответ "today" (сегодня) снабжен тегом и отображается во второй строке, так как это предварительно созданная сущность и она не подлежит редактированию.
5. Щелкните Score Actions (Оценить действия).
    - Обратите внимание, что дата теперь отображается в разделе Entity Memory (Память сущности). 
    - Если навести указатель мыши на дату, отобразятся дополнительные данные, предоставленные службой LUIS, которые могут быть полезны и в дальнейшем обработаны в коде. 
6. Выберите The date is $builtin-datetimev2.
7. Щелкните Done Teaching (Завершить обучение).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Альтернативные входные данные](./8-alternative-inputs.md)
