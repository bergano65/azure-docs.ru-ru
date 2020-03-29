---
title: Как указать модель обнаружения - Лицо
titleSuffix: Azure Cognitive Services
description: В этой статье будет упомяна, как выбрать модель обнаружения лиц с помощью приложения Azure Face.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934581"
---
# <a name="specify-a-face-detection-model"></a>Указание модели определения лиц

В этом руководстве показано, как указать модель распознавания лиц для службы Azure Face.

Служба Face использует модели машинного обучения для выполнения операций на лицах людей на изображениях. Мы продолжаем повышать точность наших моделей на основе отзывов клиентов и достижений в исследованиях, и мы поставляем эти улучшения в качестве обновления моделей. Разработчики имеют возможность указать, какую версию модели распознавания лиц они хотели бы использовать; они могут выбрать модель, которая наилучшим образом соответствует их корпусу использования.

Читайте дальше, чтобы узнать, как указать модель распознавания лиц в определенных операциях лица. Служба Face использует распознавание лиц всякий раз, когда преобразует изображение лица в какую-либо другую форму данных.

Если вы не уверены, следует ли использовать последнюю модель, перейдите в раздел [«Оценка различных моделей»,](#evaluate-different-models) чтобы оценить новую модель и сравнить результаты с помощью текущего набора данных.

## <a name="prerequisites"></a>Предварительные требования

Вы должны быть знакомы с концепцией обнаружения лица ИИ. Если это не так, см. концептуальное руководство по обнаружению лиц или руководство:

* [Концепции распознавания лиц](../concepts/face-detection.md)
* [Как обнаружить лица на изображении](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Обнаружение граней с помощью указанной модели

Обнаружение лица находит ограничивающие места человеческих лиц и определяет их визуальные ориентиры. Он извлекает черты лица и хранит их для последующего использования в операциях [распознавания.](../concepts/face-recognition.md)

При использовании [Face - Detect] API можно назначить `detectionModel` версию модели с параметром. Возможные значения:

* `detection_01`
* `detection_02`

URL-адрес запроса для [Face - Detect] REST API будет выглядеть следующим образом:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Если вы используете клиентскую библиотеку, вы `detectionModel` можете назначить значение, перейдя в соответствующую строку. Если вы оставите его без назначенного, API`detection_01`будет использовать версию модели по умолчанию (). Смотрите следующий пример кода для клиентской библиотеки .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Добавление лица к лицу с указанной моделью

Служба Face может извлекать данные лица из изображения и связывать их с объектом **Person** Group через [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. В этом вызове API можно указать модель обнаружения так же, как и в [Face - Detect.]

Смотрите следующий пример кода для клиентской библиотеки .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Этот код создает **PersonGroup** с идентификатором `mypersongroupid` и добавляет к нему **человека.** Затем он добавляет лицо к `detection_02` этому **человеку** с помощью модели. Если вы не укажете параметр *detectionModel,* API `detection_01`будет использовать модель по умолчанию.

> [!NOTE]
> Вам не нужно использовать одну и ту же модель обнаружения для всех лиц в объекте **Person,** и вам не нужно использовать ту же модель обнаружения при обнаружении новых лиц для сравнения с объектом **Person** (в [лице - Определить] API, например).

## <a name="add-face-to-facelist-with-specified-model"></a>Добавление лица в FaceList с указанной моделью

Можно также указать модель обнаружения при добавлении лица к существующему объекту **FaceList.** Смотрите следующий пример кода для клиентской библиотеки .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Этот код создает **FaceList** называется `My face collection` и добавляет `detection_02` лицо к нему с моделью. Если вы не укажете параметр *detectionModel,* API `detection_01`будет использовать модель по умолчанию.

> [!NOTE]
> Вам не нужно использовать одну и ту же модель обнаружения для всех лиц в объекте **FaceList,** и вам не нужно использовать ту же модель обнаружения при обнаружении новых лиц для сравнения с объектом **FaceList.**

## <a name="evaluate-different-models"></a>Оценка различных моделей

Различные модели распознавания лиц оптимизированы для различных задач. Ниже приведена таблица для обзора различий.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Выбор по умолчанию для всех операций обнаружения лиц. | Выпущен в мае 2019 года и доступен по желанию во всех операциях по обнаружению лиц.
|Не оптимизирован для небольших, боковых или размытых граней.  | Улучшенная точность на небольших, боковых и размытых лицах. |
|Возвращает атрибуты лица (поза головы, возраст, эмоции и так далее), если они указаны в вызове обнаружения. |  Не возвращает атрибуты лица.     |
|Возвраты сталкиваются с ориентирами, если они указаны в вызове обнаружения.   | Не возвращает сяпов.  |

Лучший способ сравнить характеристики `detection_01` `detection_02` и модели — использовать их в наборе выборочных данных. Мы рекомендуем вызывать [Face - Detect] API на различных изображениях, особенно изображениях многих лиц или лиц, которые трудно увидеть, используя каждую модель обнаружения. Обратите внимание на количество граней, которые возвращает каждая модель.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как указать модель обнаружения для использования с различными AIS face. Затем следуйте быстрому запуску, чтобы начать использовать распознавание лиц.

* [Лицо .NET SDK](../Quickstarts/csharp-sdk.md)
* [Лицо Python SDK](../Quickstarts/python-sdk.md)
* [Лицо Go SDK](../Quickstarts/go-sdk.md)

[Лицо — обнаружить;]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Лицо — идентифицировать;]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
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
