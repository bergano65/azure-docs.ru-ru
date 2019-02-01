---
title: включение файла
description: включение файла
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 14678a143e1b14b9b0b89435f356ac5df98ef40c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478689"
---
Для доступа к конечной точке прогнозирования предоставляется ключ этой конечной точки. В рамках этого краткого руководства используйте бесплатный начальный ключ, связанный с вашей учетной записи LUIS. 
 
1. Выполните вход, используя учетную запись LUIS. 

    [![Снимок экрана со списком приложений Language Understanding (LUIS)](media/cognitive-services-luis/app-list.png "Screenshot of Language Understanding (LUIS) app list")](media/cognitive-services-luis/app-list.png)

2. В правом верхнем меню выберите свое имя и щелкните **Settings** (Параметры).

    ![Доступ к меню параметров пользователя LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Скопируйте значение из поля **Authoring key** (Ключ разработки). Он понадобится позже при работе с кратким руководством. 

    [![Снимок экрана с параметрами пользователя (LUIS)](media/cognitive-services-luis/get-user-authoring-key.png "Screenshot of Language Understanding (LUIS) user settings")](media/cognitive-services-luis/get-user-authoring-key.png)

    Ключ позволяет бесплатно получить неограниченное количество запросов к API для разработки и до 1000 запросов к API конечной точки прогнозирования в месяц для всех приложений LUIS. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
