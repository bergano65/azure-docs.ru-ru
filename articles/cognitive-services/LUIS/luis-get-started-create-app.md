---
title: Краткое руководство по созданию приложения
titleSuffix: Language Understanding - Azure Cognitive Services
description: Узнайте, как создать приложение LUIS, которое использует предварительно созданную предметную область `HomeAutomation` для включения и отключения освещения и устройств. Эта предварительно созданная предметная область предоставляет для использования намерения, сущности и фразы. Когда все будет готово, вы получите работающую в облаке конечную точку LUIS.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 71f3084be697dd84f3f262d2a79cd04a0ba76d8e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086816"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Краткое руководство. Использование предварительно созданного приложения для системы домашней автоматики

В этом кратком руководстве описано, как создать приложение LUIS, которое использует предварительно созданную предметную область `HomeAutomation` для включения и отключения освещения и устройств. Эта предварительно созданная предметная область предоставляет для использования намерения, сущности и фразы. Когда все будет готово, вы получите работающую в облаке конечную точку LUIS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей требуется бесплатная учетная запись LUIS, которую можно создать на портале LUIS по адресу [https://www.luis.ai](https://www.luis.ai). 

## <a name="create-a-new-app"></a>Создание нового приложения
Создавать и администрировать приложения можно на странице **Мои приложения**. 

1. Войдите на портал LUIS.

2. Выберите **Создать приложение**.

    [![Снимок экрана со списком приложений](media/luis-quickstart-new-app/app-list.png "Screenshot of app list")](media/luis-quickstart-new-app/app-list.png)

3. В диалоговом окне присвойте приложению имя Home Automation.

    [![Снимок экрана со всплывающим диалоговым окном "Создание приложения"](media/luis-quickstart-new-app/create-new-app-dialog.png "Screenshot of Create new app pop-up dialog")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Выберите язык и региональные параметры для приложения. Для нашего примера Home Automation выберите английский язык. Затем выберите **Готово**. LUIS создаст приложение Home Automation. 

    >[!NOTE]
    >Язык и региональные параметры нельзя изменить после создания приложения. 

## <a name="add-prebuilt-domain"></a>Добавление готовой предметной области

На панели навигации слева выберите **Prebuilt domains** (Предварительно созданные предметные области). Выполните поиск по строке Home. Выберите **Add domain** (Добавить предметную область).

[![Снимок экрана с предметной областью Home Automation, вызываемой из предварительно созданного меню предметных областей](media/luis-quickstart-new-app/home-automation.png "Screenshot of Home Automation domain called out in prebuilt domain menu")](media/luis-quickstart-new-app/home-automation.png)

Когда предметная область будет успешно добавлена, в области предварительно созданных предметных областей отобразится кнопка **Remove domain** (Удалить предметную область).

[![Снимок экрана с кнопкой удаления в предметной области Home Automation](media/luis-quickstart-new-app/remove-domain.png "Screenshot of Home Automation domain with remove button")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Намерения и сущности

Выберите **Intents** (Намерения) в области навигации слева, чтобы просмотреть намерения из предметной области HomeAutomation. По каждому намерению представлены примеры высказываний.

> [!NOTE]
> Намерение **None** (Нет) стандартно предоставляется всеми приложениями LUIS. Оно используется для обработки фраз, которые не соответствуют функциям приложения. 

Выберите намерение **HomeAutomation.TurnOff**. Вы увидите, что это намерение содержит список фраз с обозначенными сущностями.

[![Снимок экрана с намерением HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnon.png "Screenshot of HomeAutomation.TurnOff intent")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Тестирование приложения
После обучения можно выполнить тестирование приложения. Щелкните **Test** (Тестировать) в меню навигации сверху. Введите тестовое высказывание, например Turn off the lights (Выключить свет) в области интерактивного тестирования и нажмите клавишу ВВОД. 

```
Turn off the lights
```

Убедитесь, что намерение с самой высокой оценкой для этого тестового высказывания выбрано правильно.

В нашем примере Turn off the lights (Выключить свет) правильно распознано как намерение HomeAutomation.TurnOff.

[![Снимок экрана с выделенной фразой на панели тестирования](media/luis-quickstart-new-app/test.png "Screenshot of Test panel with utterance highlighted")](media/luis-quickstart-new-app/test.png)


Снова щелкните **Test** (Тестировать), чтобы свернуть панель тестирования. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Перейдите в конец URL-адреса, введите `turn off the living room light` и нажмите клавишу ВВОД. Браузер отображает ответ JSON для конечной точки HTTP.

    [![Снимок экрана браузера с результатом JSON, в котором обнаружено намерение TurnOff](media/luis-quickstart-new-app/turn-off-living-room.png "Screenshot of browser with JSON result detects the intent TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Эту конечную точку можно вызвать из кода:

> [!div class="nextstepaction"]
> [Руководство по вызову конечной точки LUIS с помощью кода](luis-get-started-cs-get-intent.md).
