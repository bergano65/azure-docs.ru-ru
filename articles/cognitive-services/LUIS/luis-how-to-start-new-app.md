---
title: Создание нового приложения — LUIS
titleSuffix: Azure Cognitive Services
description: Создание и настройка приложений на веб-странице службы "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904265"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Создание приложения LUIS на портале LUIS
Создать приложение LUIS можно двумя способами: Вы можете создать приложение LUIS на портале LUIS или с помощью [API-интерфейсов](developer-reference-resource.md)разработки Luis.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Использование портала LUIS

Вы можете создать новое приложение на портале предварительной версии несколькими способами:

* начать с пустого приложения и создать намерения, фрагменты речи и сущности;
* начать с пустого приложения и добавить [готовую предметную область](luis-how-to-use-prebuilt-domains.md);
* Импортируйте приложение LUIS из `.lu` или `.json` файла, который уже содержит объекты, фразы продолжительностью и сущности.

## <a name="using-the-authoring-apis"></a>Использование API-интерфейсов разработки
С помощью API-интерфейсов разработки приложение можно создать несколькими способами:

* [Добавление приложения](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) — запуск с пустым приложением и создание целей, фразы продолжительностью и сущностей.
* [Добавьте](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) готовое приложение — Начните с предварительно созданного домена, в том числе целей, фразы продолжительностью и сущностей.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Создание приложения в LUIS

1. На странице **Мои приложения** выберите подписку и создайте ресурс, а затем — **создать**. Если вы используете ключ бесплатной пробной версии, Узнайте, как [создать ресурс для разработки](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

    ![Список приложений LUIS](./media/create-app-in-portal.png)


1. В диалоговом окне введите имя приложения, например `Pizza Tutorial`.

    ![Диалоговое окно создания приложения](./media/create-pizza-tutorial-app-in-portal.png)

1. Выберите язык и региональные параметры приложения, а затем нажмите кнопку **Готово**. В этом случае ресурс Description и PREDICTION является необязательным. Вы можете задать в любое время в разделе **Управление** на портале.

    > [!NOTE]
    > Язык и региональные параметры нельзя изменить после создания приложения. 

    После создания приложения на портале LUIS **появится список "цели" с** уже созданным намерением `None`. Теперь у вас есть пустое приложение. 
    
    > [!div class="mx-imgBorder"]
    > ![список целей без намерения создания без примеров фразы продолжительностью.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Другие доступные действия

Контекстная панель инструментов предоставляет другие действия:

* Переименование приложения
* Импорт из файла с помощью `.lu` или `.json`
* Экспорт приложения как `.lu` (для [лудовн](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json`или `.zip` (для [контейнера Luis](luis-container-howto.md))
* Импорт журналов конечной точки контейнера для просмотра конечной точки фразы продолжительностью
* Экспорт журналов конечных точек в качестве `.csv`для автономного анализа
* Удаление приложения

## <a name="next-steps"></a>Дополнительная информация

Если проект приложения включает обнаружение намерения, [Создайте новые](luis-how-to-add-intents.md)цели и добавьте пример фразы продолжительностью. Если проект приложения предназначен только для извлечения данных, добавьте пример фразы продолжительностью к намерению None, затем [Создайте сущности](luis-how-to-add-example-utterances.md)и пометка примера фразы продолжительностью с этими сущностями. 
