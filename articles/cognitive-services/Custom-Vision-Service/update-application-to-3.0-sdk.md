---
title: Как обновить проект до API 3.0
titleSuffix: Azure Cognitive Services
description: Узнайте, как обновить проекты Custom Vision с предыдущей версии API до API 3.0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647497"
---
# <a name="update-to-the-30-api"></a>Обновление до API 3.0

Пользовательское видение уже достигло общей доступности и претерпело обновление API.
Это обновление включает в себя несколько новых функций и, что важно, несколько изменений:

* API прогнозирования теперь разделен на два на основе типа проекта.
* Вариант экспорта Vision AI Developer Kit (VAIDK) требует создания проекта определенным образом.
* Итерации по умолчанию были удалены в пользу публикации / непубликации названной итерации.

Это руководство покажет вам, как обновить проекты для работы с новой версией API. Полный список изменений можно узнать в [примечаниях к выпуску.](release-notes.md)

## <a name="use-the-updated-prediction-api"></a>Используйте обновленный API прогнозирования

AIS 2.x использовали один и тот же вызов прогнозирования как для классификаций изображений, так и для проектов детектора объектов. Оба типа проектов были приемлемы для вызовов **PredictImage** и **PredictImageUrl.** Начиная с 3.0, мы разделили этот API, чтобы вам нужно было сопоставить тип проекта с вызовом:

* Используйте **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** и **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** для получения прогнозов для проектов классификации изображений.
* Используйте **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** и **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** для получения прогнозов для проектов обнаружения объектов.

## <a name="use-the-new-iteration-publishing-workflow"></a>Используйте новый рабочий процесс публикации итерации

AIS 2.x использовали итерацию по умолчанию или указанный идентификатор итерации, чтобы выбрать итерацию для использования для прогнозирования. Начиная с 3.0, мы приняли издательский поток, в соответствии с которым вы сначала публикуете итерацию под указанным названием из обучаемого API. Затем вы передайте имя методам прогнозирования, чтобы указать, какую итерацию использовать.

> [!IMPORTANT]
> AAP 3.0 не используют функцию итерации по умолчанию. До тех пор, пока мы не обезвстановим старые AIS, вы можете продолжать использовать 2.x AAP для переключения итерации по умолчанию. Эти AAP будут поддерживаться в течение определенного периода времени, и вы можете вызвать метод **[UpdateIteration,](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** чтобы отметить итерацию по умолчанию.

### <a name="publish-an-iteration"></a>Публикация итерации

После обучения итерации можно сделать ее доступной для прогнозирования с помощью метода **[PublishIteration.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** Чтобы опубликовать итерацию, вам понадобится идентификатор ресурса прогнозирования, который доступен на странице настроек веб-сайта CustomVision.

![Страница настроек веб-сайта Custom Vision с идентификатором ресурса прогнозирования.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Вы также можете получить эту информацию с [портала Azure,](https://portal.azure.com) перейдя на ресурс Custom Vision Prediction и выбрав **свойства.**

После публикации итерации приложения могут использовать ее для прогнозирования, указав имя в вызове API-извашего прогноза. Чтобы сделать итерацию недоступной для вызовов прогнозирования, используйте API **[UnpublishIteration.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**

## <a name="next-steps"></a>Дальнейшие действия

* [Справочная документация API обучения (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Справочная документация API прогнозирования (REST)](https://go.microsoft.com/fwlink/?linkid=865445)