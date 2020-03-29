---
title: Создать новое приложение - LUIS
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220834"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Создание приложения LUIS на портале LUIS
Создать приложение LUIS можно двумя способами: на портале LUIS или с помощью [API-интерфейсов](developer-reference-resource.md) разработки LUIS.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Использование портала LUIS

Создать новое приложение можно на портале предварительного просмотра несколькими способами:

* начать с пустого приложения и создать намерения, фрагменты речи и сущности;
* начать с пустого приложения и добавить [готовую предметную область](luis-how-to-use-prebuilt-domains.md);
* Импортируйте приложение LUIS `.lu` `.json` из файла, который уже содержит намерения, высказывания и сущности.

## <a name="using-the-authoring-apis"></a>Использование API-интерфейсов разработки
С помощью API-интерфейсов разработки приложение можно создать несколькими способами:

* [Добавить приложение](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - начните с пустого приложения и создавайте намерения, высказывания и сущности.
* [Добавьте заранее построенное приложение](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - начните с заранее построенного домена, включая намерения, высказывания и сущности.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Создание приложения в LUIS

1. На странице **My Apps** выберите подписку, а затем создайте ресурс **.** Если вы используете бесплатный пробный ключ, узнайте, как [создать авторский ресурс.](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)

    ![Список приложений LUIS](./media/create-app-in-portal.png)


1. В поле диалога введите название приложения, `Pizza Tutorial`например.

    ![Диалоговое окно создания приложения](./media/create-pizza-tutorial-app-in-portal.png)

1. Выберите культуру приложения, а затем выберите **Done**. На данный момент ресурс описания и прогнозирования является необязательным. Вы можете установить затем в любое время в разделе **Управление** портала.

    > [!NOTE]
    > Язык и региональные параметры нельзя изменить после создания приложения. 

    После создания приложения портал LUIS показывает список **намерений** с уже созданным для вас намерением. `None` Теперь у вас есть пустое приложение. 
    
    > [!div class="mx-imgBorder"]
    > ![Список намерений с ни одним намерением, созданным без примерных высказываний.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Другие действия доступны

Панель контекстных инструментов содержит другие действия:

* Переименование приложения
* Импорт из `.lu` файла с использованием или`.json`
* Экспорт приложение `.lu` как (для `.json` [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), , или `.zip` (для [контейнера LUIS](luis-container-howto.md))
* Журналы конечных точек импортного контейнера для рассмотрения высказываний конечных точек
* Экспортные журналы конечных `.csv`точек, как , для автономного анализа
* Удаление приложения

## <a name="next-steps"></a>Дальнейшие действия

Если дизайн приложения включает обнаружение намерений, [создайте новые намерения](luis-how-to-add-intents.md)и добавьте пример высказываний. Если дизайн приложения — это только извлечение данных, добавьте пример высказываний в замыслу None, а затем [создайте сущности](luis-how-to-add-example-utterances.md)и обозначьте пример высказываний с этими сущностями. 
