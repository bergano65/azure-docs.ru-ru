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
ms.openlocfilehash: 8275965e84021c41a3d0b3d13a4fb71d22090757
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139917"
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

    

<!--

## Import new app
You can set the name (50 char max), version (10 char max), and description of an app in the JSON file. Examples of application JSON files are available at [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. On **My Apps** page, select **Import new app**.
2. In the **Import new app** dialog, select the JSON file defining the LUIS app.

    ![Import a new app dialog](./media/luis-create-new-app/import-app.png)

## Export app
1. On **My Apps** page, select the ellipsis (***...***) at the end of the app row.

    [![Screenshot of pop-up dialog of per-app actions](media/luis-create-new-app/apps-list.png "Screenshot of pop-up dialog of per-app actions")](media/luis-create-new-app/three-dots.png#lightbox)

2. Select **Export app** from the menu. 

## Rename app

1. On **My Apps** page, select the ellipsis (***...***) at the end of the app row. 
2. Select **Rename** from the menu.
3. Enter the new name of the app and select **Done**.

## Delete app

> [!CAUTION]
> You are deleting the app for all collaborators and the owner. [Export](#export-app) the app before deleting it. 

1. On **My Apps** page, select the ellipsis (***...***) at the end of the app row. 
2. Select **Delete** from the menu.
3. Select **Ok** in the confirmation window.

## Export endpoint logs
The logs contain the Query, UTC time, and LUIS JSON response.

1. On **My Apps** page, select the ellipsis (***...***) at the end of the app row. 
2. Select **Export endpoint logs** from the menu.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```
-->
## <a name="next-steps"></a>Дополнительная информация

Первая задача в приложении — [добавление намерений](luis-how-to-add-intents.md).