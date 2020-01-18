---
title: Заметки о выпуске — служба распознавания лиц
titleSuffix: Azure Cognitive Services
description: Заметки о выпуске для службы распознавания лиц включают историю изменений выпуска для различных версий.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165864"
---
# <a name="face-release-notes"></a>Заметки о выпуске лиц

Эта статья относится к версии службы лиц 1,0.

### <a name="release-changes-in-june-2019"></a>Изменения выпуска в июне 2019

* Добавлена новая модель обнаружения лиц с повышенной точностью для мелких, перекрыто и размытых лиц. Используйте его с помощью распознавания [лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [фацелисти](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [Персонграуп Person — Добавление](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) лица и [ларжеперсонграуп Person — добавьте лицо](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) , указав новое имя модели обнаружения лиц `detection_02` в параметре `detectionModel`. Дополнительные сведения см. в [описании способа указания модели обнаружения](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Изменения выпуска в апреле 2019

* Улучшена общая точность атрибутов `age` и `headPose`. Атрибут `headPose` также обновляется с включенным параметром `pitch`. Используйте эти атрибуты, указав их в параметре `returnFaceAttributes` параметра `returnFaceAttributes` [-распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . 

* Улучшенная скорость [распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [фацелиста](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Добавление](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)лица, [Персонграуп человек — Добавление](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) лица и [ларжеперсонграуп Person — Добавление](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)лица.

### <a name="release-changes-in-march-2019"></a>Изменения выпуска в марте 2019

* Добавлена новая модель распознавания лиц с улучшенной точностью. Используйте его с помощью [распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [фацелист-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [персонграуп-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) и [ларжеперсонграуп-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) , указав новое имя модели распознавания лиц `recognition_02` в `recognitionModel` параметре. Дополнительные сведения см. в [описании способа указания модели распознавания](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Изменения в выпуске за январь 2019 г.

* Добавлена функция моментального снимка для поддержки переноса данных между подписками: [snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Дополнительные сведения о [переносе данных о лицах в подписку другого лица](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Изменения в выпуске за октябрь 2018 г.

* Четкое описание для `status`, `createdDateTime`, `lastActionDateTime` и `lastSuccessfulTrainingDateTime` см. в [PersonGroup — Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) (PersonGroup — получение состояния обучения), [LargePersonGroup — Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) (LargePersonGroup — получение состояния обучения) и [LargeFaceList — Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf) (LargeFaceList — получение состояния обучения).

### <a name="release-changes-in-may-2018"></a>Изменения в выпуске за май 2018 г.

* Значительно улучшено поведение атрибута `gender`, а также улучшены атрибуты `age`, `glasses`, `facialHair`, `hair` и `makeup`. Используйте их с помощью параметра `returnFaceAttributes` распознавания [лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . 

* Предельный размер файла изображения увеличен с 4 МБ до 6 МБ для методов [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) и [LargePersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Изменения в выпуске за март 2018 г.

* Добавлены контейнеры, принимающие миллионы объектов: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) и [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Дополнительные сведения см. в статье [How to use the large-scale feature](Face-API-How-to-Topics/how-to-use-large-scale.md) (Использование функции для увеличения масштаба).

* Увеличено [лицо — выявление](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) параметра `maxNumOfCandidatesReturned` от [1, 5] до [1, 100] и по умолчанию равным 10.

### <a name="release-changes-in-may-2017"></a>Изменения в выпуске за май 2017 г.

* Добавлены атрибуты `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`и `noise` в параметре " [определить](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`".

* Для PersonGroup в методе [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) поддерживаются до 10 000 пользователей.

* Добавлено разбиение на страницы в методе [PersonGroup Person — List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) с использованием необязательных параметров `start` и `top`.

* Добавлена поддержка параллелизма при добавлении и удалении лиц для разных FaceLists и разных людей в PersonGroup.

### <a name="release-changes-in-march-2017"></a>Изменения в выпуске за март 2017 г.
* Добавлен атрибут `emotion` в параметре `returnFaceAttributes` для распознавания [лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

* Исправлена ошибка, не позволявшая повторно обнаружить лицо с наложенным прямоугольником, который возвращается методом [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) в качестве `targetFace` в [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) и [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Исправлен размер обнаруживаемого лица, чтобы он строго находился в диапазоне от 36×36 до 4096×4096 пикселей.

### <a name="release-changes-in-november-2016"></a>Изменения в выпуске за ноябрь 2016 г.
* Добавлена подписка категории "Стандартный" для Хранилища изображений лиц, которая позволяет сохранять между сеансами дополнительные изображения лиц и использовать их в [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) или [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) для идентификации людей или поиска похожих. За хранение изображений взимается плата: 0,5 долл. США за 1000 изображений лиц. Плата распределяется пропорционально за каждый день. Для подписки категории "Бесплатный" сохраняется ограничение на общее количество людей — до 1000 человек.

### <a name="release-changes-in-october-2016"></a>Изменения в выпуске за октябрь 2016 г.
* Изменено сообщение об ошибке, возникающее при передаче сведений о нескольких лицах в метод targetFace. Вместо "There are more than one face in the image" теперь возвращается строка "There is more than one face in the image" в методах [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) и [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Изменения в выпуске за июль 2016 г.
* Добавлена поддержка аутентификации путем сравнения идентификатора лица с идентификатором объекта Person в методе [Face — Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Добавлен необязательный параметр `mode`, который позволяет выбрать один из двух режимов работы (`matchPerson` или `matchFace`) в методе [Face — Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). По умолчанию используется значение `matchPerson`.

* Добавлен необязательный параметр `confidenceThreshold`, с помощью которого пользователь может задать порог привязки лица к объекту Person в методе [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Добавлены необязательные параметры `start` и `top` в методе [PersonGroup — List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248), с помощью которых пользователь может указать начальную точку и общее число отображаемых групп PersonGroup.

### <a name="v10-changes-from-v0"></a>Отличия версии 1.0 от версии 0
* Корневая конечная точка службы изменена с ```https://westus.api.cognitive.microsoft.com/face/v0/``` на ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Внесены изменения в методы [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face — Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) и [Face — Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Минимальный размер обнаруживаемого лица изменен на 36×36 пикселей. Теперь лица размером менее 36×36 не обнаруживаются.

* Данные PersonGroup и Person в интерфейсе распознавания лиц версии 0 объявлены нерекомендуемыми. Эти данные недоступны в службе распознавания лиц версии 1.0.

* Конечная точка API распознавания лиц версии 0 объявлена нерекомендуемой с 30 июня 2016 г.
