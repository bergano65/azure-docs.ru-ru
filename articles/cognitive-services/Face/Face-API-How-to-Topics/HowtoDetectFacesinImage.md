---
title: Обнаружение лиц на изображении - API распознавания лиц
titleSuffix: Azure Cognitive Services
description: Сведения об использовании различных данных, возвращаемых с помощью функции обнаружения лиц.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124549"
---
# <a name="get-face-detection-data"></a>Получение данных обнаружения лиц

В этом руководстве показано, как использовать обнаружение лиц для извлечения таких атрибутов, как пол, возраст или поза из данного изображения. Фрагменты кода в этом руководстве создаются на языке C# с помощью клиентской библиотеки API распознавания лиц Cognitive Services Azure. Ту же функциональность доступна через [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

В этом руководстве показано, как для:

- Получение расположения и размеры сторон изображения.
- Получите расположение различных позу, например учеников, а нос — папы и рот изображения.
- Думаю, пол, возраст, эмоции и другие атрибуты обнаруженного лица.

## <a name="setup"></a>Настройка

В этом руководстве предполагается, что уже создан [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) объект, названный `faceClient`, лиц подписки ключа и конечной точки URL-адрес. На этой странице можно использовать функцию обнаружения лиц с помощью вызова [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), которая будет использоваться в этом руководстве, или [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Инструкции о том, как настроить эту функцию, см. в разделе [найти сталкивается краткое руководство по C# ](../quickstarts/csharp-detect-sdk.md).

Это руководство ориентировано на особенности вызова обнаружить, например, какие аргументы можно передать и что делать с возвращенными данными. Мы рекомендуем запрашивать только необходимых компонентов. Каждая операция требует дополнительного времени для завершения.

## <a name="get-basic-face-data"></a>Получение данных основные лиц

Чтобы найти лиц и получить их позиций в изображение, вызовите метод с _returnFaceId_ параметру присвоить **true**. Это значение по умолчанию.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Вы можете запросить возвращенный [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) объекты для их уникальные идентификаторы и прямоугольник, который предоставляет координаты точки поверхности.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Сведения о том, как выполнить синтаксический анализ, расположение и размеры начертание шрифта, см. в разделе [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Как правило этот прямоугольник содержит глаза, бровей, нос и рот. Вверху head, уши и чэнь не обязательно включаются. Чтобы использовать лицо в рамке обрезать полный head или получить портрет среднего снимок, возможно для образа тип идентификатора фотографий, можно развернуть прямоугольника в каждом направлении.

## <a name="get-face-landmarks"></a>Получить позу

[Сталкиваются ориентиров](../concepts/face-detection.md#face-landmarks) представляют собой набор точек для поиска на грани, например учеников или кончика нос. Чтобы получить данные ориентиров лиц, задайте _returnFaceLandmarks_ параметр **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

В следующем коде показано, как можно извлечь расположения нос и учеников:

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

Также можно использовать данные ориентиров лиц для точного подсчета направление поверхности. Например можно определить поворот поверхности как вектор от центра рот относительно центральной части глаза. Представленный ниже код вычисляет данного вектора:

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

Когда вы знаете, направление поверхности, можно поворачивать кадр прямоугольный лиц и выровнять ее более должным образом. Чтобы обрезать лиц на изображении, можно программным образом Поворачивание изображения так, чтобы отображались грани всегда вертикально.

## <a name="get-face-attributes"></a>Получить черт лица

Помимо лицевым прямоугольникам и ориентиры API распознавания лиц можно анализировать несколько атрибутов концептуальной грани. Полный список см. в разделе [лицевым атрибутам](../concepts/face-detection.md#attributes) концептуальной раздела.

Чтобы проанализировать черт лица, задайте _returnFaceAttributes_ параметр к списку [FaceAttributeType перечисления](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) значения.

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

Затем получите ссылки на возвращаемые данные и выполнять дополнительные операции в соответствии с потребностями.

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

Дополнительные сведения о каждом из атрибутов, см. в разделе [распознавание лиц и атрибуты](../concepts/face-detection.md) концептуальное руководство по.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как использовать различные функциональные возможности обнаружения лиц. Затем интеграции этих функций в ваше приложение, следуя подробным руководством.

- [Учебник. Создание приложения WPF для отображения данных лиц на изображении](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Учебник. Создание приложения Android для обнаружения и кадров лиц на изображении](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>См. также

- [Справочная документация (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Справочная документация (пакет SDK для .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)