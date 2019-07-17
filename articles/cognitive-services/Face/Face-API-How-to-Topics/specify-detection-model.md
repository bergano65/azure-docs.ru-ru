---
title: Как указать модель обнаружения - API распознавания лиц
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как выбрать модель обнаружения лиц для приложения Azure API распознавания лиц.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 26ab3cb247309aa21791ca5a984f39ef40ce9a78
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249630"
---
# <a name="specify-a-face-detection-model"></a>Указание модели определения лиц

В этом руководстве показано, как задать модель обнаружения лиц для API распознавания лиц Azure.

API распознавания лиц используются моделей машинного обучения для выполнения операций на человеческих лиц на изображениях. Мы смогли улучшить точность наших моделей на основе отзывов клиентов и усовершенствованные возможности исследования и мы предлагаем эти усовершенствования, как обновления модели. У разработчиков есть возможность указать, какую версию модели обнаружения лиц они бы хотели использовать; их можно выбрать модель, которая лучше всего соответствует они используют эту службу.

Читайте дальше, чтобы сведения об указании модели обнаружения лиц в некоторых операциях лиц. API распознавания лиц использует обнаружение лиц, каждый раз, когда он преобразует изображения грани в некоторые другие виды данных.

Если вы не уверены, должен ли использоваться последней модели, перейдите к разделу [оценки различных моделей](#evaluate-different-models) раздел для оценки новой модели и сравнить результаты, используя текущий набор данных.

## <a name="prerequisites"></a>Предварительные требования

Вы должны быть знакомы с концепцией обнаружение лиц искусственного Интеллекта. Если нет, см. в разделе руководства концептуальной обнаружения лиц или Практическое руководство:

* [Основные понятия обнаружение лиц](../concepts/face-detection.md)
* [Как обнаружить лица на изображении](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Обнаружение лиц с указанной модели

Обнаружение лиц находит расположение ограничивающий прямоугольник лиц и определяет их visual ориентиров. Он извлекает функции распознавания лиц и сохраняет их для дальнейшего использования в [распознавания](../concepts/face-recognition.md) операций.

При использовании [Лиц — обнаружения] API, вы можете назначить версии модели с `detectionModel` параметра. Ниже приведены доступные значения.

* `detection_01`
* `detection_02`

URL-адрес запроса для [Лиц — обнаружения] REST API будет выглядеть следующим образом:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Если вы используете клиентскую библиотеку, можно назначить значение `detectionModel` , передав соответствующую строку. Если этот параметр не назначен, API будет использовать версию модели по умолчанию (`detection_01`). См. в следующем примере кода для клиентской библиотеки .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Добавление распознавания лиц в Person с указанной модели

API распознавания лиц можно извлечь данные лиц из образа и свяжите ее с **Person** объекта через [Person каждой группе людей — добавление распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. В этот вызов API, можно указать в модели обнаружения так же как и в [Лиц — обнаружения].

См. в следующем примере кода для клиентской библиотеки .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Этот код создает **каждой группе людей** с Идентификатором `mypersongroupid` и добавляет **Person** к нему. Затем он добавляет грани это **Person** с помощью `detection_02` модели. Если вы не укажете *detectionModel* будет использоваться модель по умолчанию, в параметре, API `detection_01`.

> [!NOTE]
> Не нужно использовать ту же модель обнаружения для всех лиц в **Person** объекта и вам не нужно использовать ту же модель обнаружения при обнаружении новых поверхностей для сравнения с **Person** объекта (в [Face — Identify] API, например).

## <a name="add-face-to-facelist-with-specified-model"></a>Добавление распознавания лиц в FaceList с указанной модели

Можно также указать модель обнаружения при добавлении к существующему грани **FaceList** объекта. См. в следующем примере кода для клиентской библиотеки .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Этот код создает **FaceList** вызывается `My face collection` и добавляет к нему с помощью грани `detection_02` модели. Если вы не укажете *detectionModel* будет использоваться модель по умолчанию, в параметре, API `detection_01`.

> [!NOTE]
> Не нужно использовать ту же модель обнаружения для всех лиц в **FaceList** объекта и вам не нужно использовать ту же модель обнаружения при обнаружении новых поверхностей для сравнения с **FaceList** объекта.

## <a name="evaluate-different-models"></a>Анализ различных моделей

Модели обнаружения разных лиц оптимизированы для различных задач. Общие сведения о различиях см.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Выбор по умолчанию для всех операций обнаружения лиц. | Выпущен в мая 2019 и доступно при необходимости во всех операциях обнаружения лиц.
|Не оптимизирован для малых, вид сбоку и становится нечетким лиц.  | Повышенная точность на малых, вид сбоку и становится нечетким лиц. |
|Возвращает лицевым атрибутам (головной поза, возраст, эмоции и т. д.), если их указания в вызове обнаружить. |  Возвращает атрибуты, распознавания лиц.     |
|Возвращает сталкиваются ориентиры, если их указания в вызове обнаружить.   | Не возвращает позу.  |

Лучший способ сравнения характеристиками `detection_01` и `detection_02` моделей будет использовать их в набор. Рекомендуем вызывать [Лиц — обнаружения] API для разнообразных образы, особенно образы лица или лиц, которые трудно см. в разделе, с помощью каждой модели обнаружения. Обратите внимание на количество лиц, которые возвращает каждой модели.

## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали, как указать модель обнаружения для использования с помощью различных интерфейсов API распознавания лиц. Затем следует выполните краткое руководство поможет приступить к использованию обнаружения лиц.

* [Обнаружение лиц на изображении (пакет SDK для .NET)](../quickstarts/csharp-detect-sdk.md)

[Лиц — обнаружения]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
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
