---
title: Как указать модель обнаружения — API распознавания лиц
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как выбрать модель обнаружения лиц для использования с приложением API распознавания лиц Azure.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 4306a918d56240bfe038100124b3c2b94964cebc
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306684"
---
# <a name="specify-a-face-detection-model"></a>Указание модели определения лиц

В этом руководство показано, как указать модель обнаружения лиц для API распознавания лиц Azure.

API распознавания лиц использует модели машинного обучения для выполнения операций с человеческими лицами в изображениях. Мы продолжаем улучшать точность наших моделей, основываясь на отзывах клиентов и возможностях исследования, а также доставлять эти улучшения как обновления модели. Разработчики имеют возможность указать, какую версию модели обнаружения лиц следует использовать. они могут выбрать модель, которая наилучшим образом соответствует их варианту использования.

Ознакомьтесь со сведениями о том, как указать модель обнаружения лиц в определенных операциях. API распознавания лиц использует распознавание лиц при преобразовании изображения лица в другую форму данных.

Если вы не уверены, следует ли использовать последнюю модель, перейдите к разделу [Вычисление различных моделей](#evaluate-different-models) , чтобы оценить новую модель и сравнить результаты с помощью текущего набора данных.

## <a name="prerequisites"></a>Предварительные требования

Вы должны быть знакомы с концепцией обнаружения лиц в AI. Если вы не видите, ознакомьтесь с концептуальным руководством по обнаружению лиц или пошаговым руководством.

* [Основные понятия обнаружения лиц](../concepts/face-detection.md)
* [Обнаружение лиц в изображении](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Обнаружить лица с указанной моделью

Функция обнаружения лиц находит границы ограничивающих прямоугольников для людей и определяет их визуальные ориентиры. Он извлекает функции лица и сохраняет их для последующего использования в операциях [распознавания](../concepts/face-recognition.md) .

При использовании API [Обнаружение лиц] можно назначить версию модели с `detectionModel` помощью параметра. Допустимые значения:

* `detection_01`
* `detection_02`

URL-адрес запроса для REST API [Обнаружение лиц] будет выглядеть следующим образом:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Если вы используете клиентскую библиотеку, можно присвоить значение `detectionModel` , передав соответствующую строку. Если оставить его неназначенным, API будет использовать версию модели по умолчанию (`detection_01`). См. Следующий пример кода для клиентской библиотеки .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Добавить лицо к лицу с указанной моделью

API распознавания лиц может извлекать данные о лицах из изображения и связывать их с объектом **Person** через API [-интерфейс Персонграуп Person для добавления лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) . В этом вызове API можно указать модель обнаружения таким же образом, как и при [Обнаружение лиц].

См. Следующий пример кода для клиентской библиотеки .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Этот код создает объект **персонграуп** с идентификатором `mypersongroupid` и добавляет в него **пользователя** . Затем он добавляет к этому **пользователю** лицо, использующее `detection_02` модель. Если не указать параметр *детектионмодел* , API будет использовать модель по умолчанию, `detection_01`.

> [!NOTE]
> Не нужно использовать одинаковую модель обнаружения для всех лиц в объекте **Person** , и вам не нужно использовать ту же модель обнаружения при обнаружении новых лиц для сравнения с объектом **Person** (например, в API-интерфейсе распознавания [Face — Identify] ).

## <a name="add-face-to-facelist-with-specified-model"></a>Добавить лицо в Фацелист с указанной моделью

Можно также указать модель обнаружения при добавлении лица в существующий объект **фацелист** . См. Следующий пример кода для клиентской библиотеки .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Этот код создает **фацелист** с именем `My face collection` и добавляет `detection_02` к нему лицо с моделью. Если не указать параметр *детектионмодел* , API будет использовать модель по умолчанию, `detection_01`.

> [!NOTE]
> Не нужно использовать одну и ту же модель обнаружения для всех лиц в объекте **фацелист** , и вам не нужно использовать ту же модель обнаружения при обнаружении новых Сторон для сравнения с объектом **фацелист** .

## <a name="evaluate-different-models"></a>Оценка различных моделей

Различные модели обнаружения лиц оптимизированы для различных задач. Общие сведения о различиях см. в следующей таблице.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Выбор по умолчанию для всех операций обнаружения лиц. | Выпущена в 2019 мая и доступна дополнительно во всех операциях обнаружения лиц.
|Не оптимизировано для мелких, боковых или размытых лиц.  | Улучшенная точность небольших, боковых и размытых лиц. |
|Возвращает атрибуты лица (головной элемент, возраст, распознавания эмоций и т. д.), если они указаны в вызове метода Detect. |  Не возвращает атрибуты лица.     |
|Возвращает ориентиры для лиц, если они указаны в вызове метода Detect.   | Не возвращает ориентиры для лиц.  |

Лучший способ сравнить производительность `detection_01` моделей и `detection_02` — использовать их в образце набора данных. Мы рекомендуем вызывать API распознавания [Обнаружение лиц] на различных изображениях, особенно в изображениях многих лиц или лиц, которые трудно увидеть, используя каждую модель обнаружения. Обратите внимание на количество сторон, возвращаемых каждой моделью.

## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали, как указать модель обнаружения для использования с различными API распознавания лиц. Затем следуйте инструкциям краткого руководства, чтобы приступить к работе с обнаружением лиц.

* [Пакет SDK для .NET для лиц](../Quickstarts/csharp-sdk.md)
* [Пакет SDK для Python для лиц](../Quickstarts/python-sdk.md)

[Обнаружение лиц]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face — Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
