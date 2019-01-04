---
title: Создание нового приложения
titleSuffix: Language Understanding - Azure Cognitive Services
description: Создание и настройка приложений на веб-странице службы "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: ddfee80c67c22c7c6016ed87dc17925c91637d21
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714004"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Создание приложения LUIS на портале LUIS
Создать приложение LUIS можно двумя способами: на портале [LUIS](https://www.luis.ai) или с помощью [API-интерфейсов](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) разработки LUIS.

## <a name="using-the-luis-portal"></a>Использование портала LUIS
На портале LUIS приложение можно создать несколькими способами:

* начать с пустого приложения и создать намерения, фрагменты речи и сущности;
* начать с пустого приложения и добавить [готовую предметную область](luis-how-to-use-prebuilt-domains.md);
* импортировать приложение LUIS из JSON-файла, который уже содержит намерения, фрагменты речи и сущности.

## <a name="using-the-authoring-apis"></a>Использование API-интерфейсов разработки
С помощью API-интерфейсов разработки приложение можно создать несколькими способами:

* [начать](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) с пустого приложения и создать намерения, фрагменты речи и сущности;
* [начать](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) с готовой предметной области.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Создание приложения в LUIS

1. На странице **Мои приложения** выберите **Create new app** (Создать приложение).

    ![Список приложений LUIS](./media/luis-create-new-app/apps-list.png)


2. В диалоговом окне введите имя приложения TravelAgent.

    ![Диалоговое окно создания приложения](./media/luis-create-new-app/create-app.png)

3. Выберите язык и региональные параметры приложения (для приложения TravelAgent выберите английский язык), а затем нажмите кнопку **Готово**. 

    > [!NOTE]
    > Язык и региональные параметры нельзя изменить после создания приложения. 


## <a name="next-steps"></a>Дополнительная информация

Первая задача в приложении — [добавление намерений](luis-how-to-add-intents.md).