---
title: Как перенести проект 3.0 API
titlesuffix: Azure Cognitive Services
description: Узнайте, как перенести проекты Custom Vision от предыдущей версии API-интерфейса в версии 3.0 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044012"
---
# <a name="migrate-to-the-30-api"></a>Перенос 3.0 API

Custom Vision теперь вышла общедоступная и произошло обновление API.
Это обновление содержит ряд новых функций и, что важно, несколько критических изменений:

* Prediction API теперь разбить на две зависимости от типа проекта.
* Функция экспорта в концепции AI Developer Kit (VAIDK) требуется создание проекта определенным образом.
* Итераций по умолчанию будут удалены в пользу публикация / отменить публикацию именованный итерации.

В этом руководстве показано, как обновить проекты для работы с новой версии API. См. в разделе [заметки о выпуске](release-notes.md) полный список изменений.

## <a name="use-the-updated-prediction-api"></a>Использовать обновленный API прогноза

2.x API-интерфейсы используется один и тот же вызов прогноза для классификаторов, что и объект обнаружения проектов. Оба проекта типы были приемлемым для **PredictImage** и **PredictImageUrl** вызовов. Начиная с 3.0, мы разделили этот API, что необходимость в соответствии с типом проекта к вызову:

* Используйте **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** и **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** для получения прогнозов для проектов классификации изображений.
* Используйте **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** и **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** для получения прогнозов для проектов обнаружения объекта.

## <a name="use-the-new-iteration-publishing-workflow"></a>Используйте новый рабочий процесс публикации, итерации

2.x API-интерфейсы позволяют выбрать итерацию, которую требуется использовать для прогнозирования итерации по умолчанию или идентификатор определенной итерации. Начиная с версии 3.0, мы приняли поток публикации, при котором сначала опубликовать итерации под указанным именем из API обучения. Затем передайте имя методам прогноза для указания какой итерации для использования.

> [!IMPORTANT]
> 3.0 API не используют возможности итерации по умолчанию. Пока мы отказаться от старых API хостинга, вы можете назначить 2.x API-интерфейсы для переключения итерации по умолчанию. Эти API-интерфейсы будут поддерживаться в течение заданного времени, и вы можете вызвать **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** метод, чтобы пометить итерации по умолчанию.

### <a name="publish-an-iteration"></a>Публикация итерации

После обучения итерации, вы можете сделать его доступным для прогноза с помощью **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** метод. Чтобы опубликовать итерации, вам потребуется идентификатор ресурса прогноза, который доступен на странице параметров CustomVision веб-сайта.

![Страница параметров веб-сайт Custom Vision с описанные идентификатор ресурса прогноза.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Эти сведения также можно получить [портал Azure](https://portal.azure.com) к ресурсу Custom Vision прогноза и выбрать **свойства**.

После публикации итерации приложения можно использовать для прогноза путем указания имени в телефонного вызова API-интерфейса прогнозирования. Чтобы сделать недоступными для вызовов прогнозирования итерации, используйте **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="additional-export-options"></a>Дополнительные параметры экспорта

С 3.0 интерфейсы API, мы предоставляете два дополнительных Экспорт целевых объектов: Архитектура ARM и пакет средств разработки концепции искусственного Интеллекта.

* Чтобы использовать ARM, необходимо просто выбрать Compact домена и затем выберите файл DockerFile и затем ARM как параметры экспорта.
* Для пакета средств разработки ии концепции проекта должны создаваться с помощью __Общие (Compact)__ домена, а также указывает VAIDK в целевом объекте Экспорт аргумент платформ.

## <a name="next-steps"></a>Дальнейшие действия

* [Справочная документация по API обучения (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Справочная документация по API-интерфейса прогнозирования (REST)](https://go.microsoft.com/fwlink/?linkid=865445)