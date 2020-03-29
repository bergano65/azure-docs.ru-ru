---
title: Обнаружение лиц на изображении - Лицо
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как использовать распознавание лиц для извлечения таких атрибутов, как пол, возраст или поза из данного изображения.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169879"
---
# <a name="get-face-detection-data"></a>Получайте данные об обнаружении лиц

В этом руководстве показано, как использовать распознавание лиц для извлечения таких атрибутов, как пол, возраст или поза из данного изображения. Фрагменты кода в этом руководстве написаны на канале C' с помощью клиентской библиотеки Azure Cognitive Services Face. Такая же функциональность доступна через [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

В этом руководстве показано, как:

- Получите расположение и размеры лиц в изображении.
- Получите расположение различных ориентиров лица, таких как зрачки, нос и рот, в изображении.
- Угадайте пол, возраст, эмоции и другие атрибуты обнаруженного лица.

## <a name="setup"></a>Настройка

Это руководство предполагает, что вы уже построили `faceClient`объект [FaceClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) названный , с ключом подписки Face и URL-адресом конечных точек. Отсюда вы можете использовать функцию обнаружения лица, позвонив либо [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), который используется в этом руководстве, или [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Для получения инструкций о том, как настроить эту функцию, следуйте одному из quickstarts.

В этом руководстве основное внимание уделяется специфике вызова Detect, например, какие аргументы можно передать и что можно сделать с возвращенными данными. Мы рекомендуем вам запросить только те функции, которые вам нужны. Каждая операция занимает дополнительное время.

## <a name="get-basic-face-data"></a>Получить базовые данные о лице

Чтобы найти лица и получить их местоположение в изображении, позвоните **true**методу с набором параметра _returnFaceId._ Это значение по умолчанию.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Вы можете запросить возвращенные объекты [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) для их уникальных идентификаций и прямоугольника, который дает пиксельные координаты лица.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Для получения информации о том, как разобрать расположение и размеры лица, [см.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet) Как правило, этот прямоугольник содержит глаза, брови, нос и рот. В верхней части головы, ушей и подбородка не обязательно включены. Чтобы использовать прямоугольник для обрезки полной головы или получить портрет среднего выстрела, возможно, для изображения типа удостоверения личности, вы можете расширить прямоугольник в каждом направлении.

## <a name="get-face-landmarks"></a>Получить лицо ориентиры

[Лицевые ориентиры](../concepts/face-detection.md#face-landmarks) представляют собой набор простых в поиске точек на лице, таких как зрачки или кончик носа. Чтобы получить данные ориентира лица, установите параметр _returnFaceLandmarks_ на **истину.**

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Следующий код показывает, как можно получить расположение носа и зрачков:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Вы также можете использовать данные о звехах лица для точного расчета направления лица. Например, можно определить вращение лица как вектор от центра рта к центру глаз. Следующий код вычисляет этот вектор:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Когда вы знаете направление лица, вы можете повернуть прямоугольную рамку лица, чтобы выровнять его более правильно. Чтобы обрезать лица на изображении, можно программно повернуть изображение так, чтобы лица всегда появлялись в вертикальном положении.

## <a name="get-face-attributes"></a>Получить атрибуты лица

Помимо прямоугольников и ориентиров, API обнаружения лица может анализировать несколько концептуальных атрибутов лица. Полный список можно опереть в концептуальном разделе [атрибуты Face.](../concepts/face-detection.md#attributes)

Чтобы проанализировать атрибуты лица, установите параметр _returnFaceAttributes_ в список значений [FaceAttributeType Enum.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Затем получите ссылки на возвращенные данные и сделайте больше операций в соответствии с вашими потребностями.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Чтобы узнать больше о каждом из [Face detection and attributes](../concepts/face-detection.md) атрибутов, см.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как использовать различные функциональные возможности обнаружения лица. Затем интегрируйте эти функции в приложение, следуя углубленному учебнику.

- [Руководство. Создание приложения WPF для отображения данных о лицах на изображении](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Руководство. Создание приложения Android для обнаружения и выделения лиц на изображении](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Связанные разделы

- [Справочная документация (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Справочная документация (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)