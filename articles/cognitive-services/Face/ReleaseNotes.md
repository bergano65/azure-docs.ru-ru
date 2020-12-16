---
title: Новые возможности службы "Мои лица"
titleSuffix: Azure Cognitive Services
description: Заметки о выпуске для службы распознавания лиц включают историю изменений выпуска для различных версий.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8a409d66065a7ccd2b34365bd565202f207ac0e8
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560752"
---
# <a name="whats-new-in-face-service"></a>Новые возможности службы "Мои лица"

Служба обслуживания лиц Azure обновляется на постоянной основе. В этой статье содержатся сведения об улучшениях, исправлениях и обновлениях документации.

## <a name="november-2020"></a>Ноябрь 2020 г.
* Опубликовал пример приложения регистрации, чтобы продемонстрировать рекомендации по установке осмысленного согласия и созданию высокоточной системы распознавания лиц с высоким уровнем качества. Пример с открытым исходным кодом можно найти в разделе Создание руководств по [приложению регистрации](build-enrollment-app.md) и в [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample), готового для разработчиков к развертыванию или настройке.  

## <a name="june-2019"></a>Июнь 2019 г.

* Добавлена новая модель обнаружения лиц с повышенной точностью для мелких, боковых, перекрыто и размытых лиц. Используйте его с помощью распознавания [лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Фацелисти, добавления](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)лица, [LargeFaceListа](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), добавления грани, [Персонграуп человека, добавления](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) лица и [ларжеперсонграуп Person — добавьте лицо](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) , указав новое имя модели обнаружения лиц `detection_02` в `detectionModel` параметре. Дополнительные сведения см. в [описании способа указания модели обнаружения](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>Апрель 2019 г.

* Улучшена общая точность `age` атрибутов и `headPose` . `headPose`Атрибут также обновляется со `pitch` значением, включенным сейчас. Используйте эти атрибуты, указав их в `returnFaceAttributes` параметре параметра распознавания [лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 

* Улучшенная скорость [распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [фацелиста](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Добавление](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)лица, [Персонграуп человек — Добавление](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) лица и [ларжеперсонграуп Person — Добавление](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)лица.

## <a name="march-2019"></a>Март 2019 г.

* Добавлена новая модель распознавания лиц с улучшенной точностью. Используйте его с помощью [распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [фацелист-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [персонграуп-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) и [ларжеперсонграуп-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) , указав новое имя модели распознавания лиц `recognition_02` в `recognitionModel` параметре. Дополнительные сведения см. в [описании способа указания модели распознавания](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Январь 2019 г.

* Добавлена функция моментального снимка для поддержки переноса данных между подписками: [snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Дополнительные сведения о [переносе данных о лицах в подписку другого лица](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Октябрь 2018 г.

* Четкое описание для `status`, `createdDateTime`, `lastActionDateTime` и `lastSuccessfulTrainingDateTime` см. в [PersonGroup — Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) (PersonGroup — получение состояния обучения), [LargePersonGroup — Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) (LargePersonGroup — получение состояния обучения) и [LargeFaceList — Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf) (LargeFaceList — получение состояния обучения).

## <a name="may-2018"></a>Май 2018 г.

* Значительно улучшено поведение атрибута `gender`, а также улучшены атрибуты `age`, `glasses`, `facialHair`, `hair` и `makeup`. Используйте их с помощью параметра [распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 

* Предельный размер файла изображения увеличен с 4 МБ до 6 МБ для методов [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) и [LargePersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2018"></a>Март 2018 г.

* Добавлены контейнеры, принимающие миллионы объектов: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) и [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Дополнительные сведения см. в статье [How to use the large-scale feature](Face-API-How-to-Topics/how-to-use-large-scale.md) (Использование функции для увеличения масштаба).

* Увеличено [лицо — обозначить](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` параметр с [1, 5] на [1, 100] и по умолчанию равен 10.

## <a name="may-2017"></a>Май 2017 г.

* Добавлены `hair` атрибуты,,,,, `makeup` `accessory` `occlusion` `blur` `exposure` и `noise` в параметре распознавания [лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .

* Для PersonGroup в методе [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) поддерживаются до 10 000 пользователей.

* Добавлено разбиение на страницы в методе [PersonGroup Person — List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) с использованием необязательных параметров `start` и `top`.

* Добавлена поддержка параллелизма при добавлении и удалении лиц для разных FaceLists и разных людей в PersonGroup.

## <a name="march-2017"></a>Март 2017 г.
* Добавлен `emotion` атрибут в параметре [распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .

* Исправлена ошибка, не позволявшая повторно обнаружить лицо с наложенным прямоугольником, который возвращается методом [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) в качестве `targetFace` в [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) и [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Исправлен размер обнаруживаемого лица, чтобы он строго находился в диапазоне от 36×36 до 4096×4096 пикселей.

## <a name="november-2016"></a>Ноябрь 2016 г.
* Добавлена подписка категории "Стандартный" для Хранилища изображений лиц, которая позволяет сохранять между сеансами дополнительные изображения лиц и использовать их в [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) или [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) для идентификации людей или поиска похожих. За хранение изображений взимается плата: 0,5 долл. США за 1000 изображений лиц. Плата распределяется пропорционально за каждый день. Для подписки категории "Бесплатный" сохраняется ограничение на общее количество людей — до 1000 человек.

## <a name="october-2016"></a>Октябрь 2016 г.
* Изменено сообщение об ошибке, возникающее при передаче сведений о нескольких лицах в метод targetFace. Вместо "There are more than one face in the image" теперь возвращается строка "There is more than one face in the image" в методах [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) и [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="july-2016"></a>Июль 2016 г.
* Добавлена поддержка аутентификации путем сравнения идентификатора лица с идентификатором объекта Person в методе [Face — Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Добавлен необязательный параметр `mode`, который позволяет выбрать один из двух режимов работы (`matchPerson` или `matchFace`) в методе [Face — Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). По умолчанию используется значение `matchPerson`.

* Добавлен необязательный параметр `confidenceThreshold`, с помощью которого пользователь может задать порог привязки лица к объекту Person в методе [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Добавлены необязательные параметры `start` и `top` в методе [PersonGroup — List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248), с помощью которых пользователь может указать начальную точку и общее число отображаемых групп PersonGroup.

## <a name="v10-changes-from-v0"></a>Отличия версии 1.0 от версии 0

* Корневая конечная точка службы изменена с ```https://westus.api.cognitive.microsoft.com/face/v0/``` на ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Внесены изменения в методы [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face — Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) и [Face — Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Минимальный размер обнаруживаемого лица изменен на 36×36 пикселей. Теперь лица размером менее 36×36 не обнаруживаются.

* Данные PersonGroup и Person в интерфейсе распознавания лиц версии 0 объявлены нерекомендуемыми. Эти данные недоступны в службе распознавания лиц версии 1.0.

* Конечная точка API распознавания лиц версии 0 объявлена нерекомендуемой с 30 июня 2016 г.
