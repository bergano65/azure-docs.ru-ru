---
title: Как указать модель распознавания - Лицо
titleSuffix: Azure Cognitive Services
description: В этой статье будет упомяна, как выбрать модель распознавания с помощью приложения Azure Face.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938821"
---
# <a name="specify-a-face-recognition-model"></a>Указание модели распознавания лиц

В этом руководстве показано, как указать модель распознавания лиц для обнаружения, идентификации и сходства с помощью службы Azure Face.

Служба Face использует модели машинного обучения для выполнения операций на лицах людей на изображениях. Мы продолжаем повышать точность наших моделей на основе отзывов клиентов и достижений в исследованиях, и мы поставляем эти улучшения в качестве обновления моделей. Разработчики имеют возможность указать, какую версию модели распознавания лиц они хотели бы использовать; они могут выбрать модель, которая наилучшим образом соответствует их корпусу использования.

Если вы являетесь новым пользователем, мы рекомендуем вам использовать последнюю модель. Читайте дальше, чтобы узнать, как указать его в различных операциях Face, избегая при этом конфликтов моделей. Если вы продвинутый пользователь и не уверены, следует ли переходить на последнюю модель, перейдите в раздел [«Оценка различных моделей»,](#evaluate-different-models) чтобы оценить новую модель и сравнить результаты с помощью текущего набора данных.

## <a name="prerequisites"></a>Предварительные требования

Вы должны быть знакомы с понятиями обнаружения и идентификации лиц ИИ. Если вы не, посмотрите эти как-к направляющим сям сначала:

* [Как обнаружить лица на изображении](HowtoDetectFacesinImage.md)
* [Как определить лица на изображении](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Обнаружение граней с помощью указанной модели

Обнаружение лица определяет визуальные ориентиры человеческих лиц и находит их местоположения, ограничивающие ящик. Он также извлекает черты лица и хранит их для использования в идентификации. Вся эта информация формирует представление одного лица.

Модель распознавания используется при извлечении функций лица, поэтому при выполнении операции Detect можно указать модельную версию.

При использовании [Face - Detect] API, назначаем модельную версию с параметром. `recognitionModel` Возможные значения:

* `recognition_01`
* `recognition_02`

Дополнительно можно указать параметр _returnRecognitionModelModel_ (по умолчанию **false),** чтобы указать, следует ли возвращать в ответ _модель распознавания._ Таким образом, URL-адрес запроса для [Face - Detect] REST API будет выглядеть следующим образом:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Если вы используете клиентскую библиотеку, вы `recognitionModel` можете назначить значение, передав строку, представляющую версию.
Если вы оставите его без назначенного, будет использоваться версия модели по умолчанию _(recognition_01)._ Смотрите следующий пример кода для клиентской библиотеки .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Определить лица с указанной моделью

Служба Face может извлекать данные лица из изображения и связывать их с объектом **Person** (например, через вызов API API [Add Face),](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) а несколько объектов **Person** могут храниться вместе в **PersonGroup.** Затем новое лицо можно сравнить с **PersonGroup** (с [лицом - Идентифицировать] вызов), и соответствующий человек в этой группе может быть идентифицирован.

**PersonGroup** должна иметь одну уникальную модель распознавания для всех **лиц,** и вы можете указать это с помощью `recognitionModel` параметра при создании группы[(PersonGroup - Создать] или [LargePersonGroup - Создать]). Если не указать этот параметр, используется исходная `recognition_01` модель. Группа всегда будет использовать модель распознавания, с которой она была создана, и новые лица будут связаны с этой моделью, когда они будут добавлены к ней; это не может быть изменено после создания группы. Чтобы увидеть, с какой моделью **настроена PersonGroup,** используйте [PersonGroup - Получите] API с набором параметров _returnRecognitionModel_ как **верный.**

Смотрите следующий пример кода для клиентской библиотеки .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

В этом коде создается `mypersongroupid` **PersonGroup** с идентификатором, и он настроен на использование модели _recognition_02_ для извлечения функций лица.

Соответственно, необходимо указать, какую модель использовать при обнаружении лиц для сравнения с этой **PersonGroup** (через [Face - Detect] API). Используемая модель всегда должна соответствовать конфигурации **PersonGroup;** в противном случае операция завершится неудачей из-за несовместимых моделей.

Существует никаких изменений в [лице - Определить] API; вам нужно только указать модельную версию в обнаружении.

## <a name="find-similar-faces-with-specified-model"></a>Найти похожие лица с указанной моделью

Вы также можете указать модель распознавания для поиска сходства. Версию модели можно назначить при `recognitionModel` создании списка лиц [FaceList - Создать] API или [LargeFaceList - Создать.] Если не указать этот параметр, используется исходная `recognition_01` модель. Список лиц всегда будет использовать модель распознавания, с которой он был создан, и новые лица будут связаны с этой моделью, когда они будут добавлены к ней; это не может быть изменено после создания. Чтобы увидеть, с какой моделью настроен список лиц, используйте [FaceList - Получите] API с набором параметров _returnRecognitionModel_ как **верный.**

Смотрите следующий пример кода для клиентской библиотеки .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Этот код создает список лиц под названием, `My face collection`используя _recognition_02_ модель для извлечения функций. При поиске этого списка лиц для аналогичных лиц с новым обнаруженным лицом, это лицо должно быть обнаружено[(Лицо - Обнаружить)]с помощью _модели recognition_02._ Как и в предыдущем разделе, модель должна быть последовательной.

Существует никаких изменений в [лице - Найти аналогичные] API; вы только указать модельверсию в обнаружении.

## <a name="verify-faces-with-specified-model"></a>Проверка лиц с указанной моделью

[Лицо - Проверка] API проверяет, принадлежат ли два лица одному и тому же человеку. В API Verify не имеет сятвий в отношении моделей распознавания, но можно сравнить только те лица, которые были обнаружены с той же моделью. Таким образом, два лица должны быть обнаружены с помощью `recognition_01` или `recognition_02`.

## <a name="evaluate-different-models"></a>Оценка различных моделей

Если вы хотите сравнить характеристики _recognition_01_ и _recognition_02_ моделей на ваших данных, вам нужно будет:

1. Создайте два **С PersonGroup**с _recognition_01_ и _recognition_02_ соответственно.
1. Используйте свои данные изображений, чтобы обнаружить лица и зарегистрировать их в **Person**s для этих двух **PersonGroup,** и запустить процесс обучения с [PersonGroup - Train] API.
1. Тест с [лицом - Определить] на обоих **PersonGroup**и сравнить результаты.

Если обычно указывается порог доверия (значение между нулем и значением, определяющим, насколько уверена модель для определения лица), возможно, потребуется использовать различные пороги для разных моделей. Порог для одной модели не предназначен для совместного использования другой и не обязательно даст те же результаты.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как указать модель распознавания для использования с различными AA-сервиса Face. Затем следуйте быстрому запуску, чтобы начать использовать распознавание лиц.

* [Лицо .NET SDK](../Quickstarts/csharp-sdk.md)
* [Лицо Python SDK](../Quickstarts/python-sdk.md)
* [Лицо Go SDK](../Quickstarts/go-sdk.md)

[Лицо — обнаружить;]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Лицо - Найти похожие]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Лицо — идентифицировать;]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Лицо - Проверить]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup — Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Персонгруппа - Получить]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup — Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup — Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Создание]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Получить]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Создание]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
