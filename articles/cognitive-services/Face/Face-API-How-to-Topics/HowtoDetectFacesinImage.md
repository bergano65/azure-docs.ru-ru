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
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588777"
---
# <a name="get-face-detection-data"></a>Получение данных обнаружения лиц

В этом руководстве будет показано, как использовать обнаружение лиц для извлечения таких атрибутов, как пол, возраст или поза из данного изображения. Фрагменты кода в этом руководстве создаются на языке C# с помощью клиентской библиотеки API распознавания лиц, но те же функциональные возможности доступна через [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

В этом руководстве показано, как для:

- Получение расположения и размеры сторон изображения.
- Получите расположение различных позу (учеников, а нос — папы, рот и т. д.) в образ.
- Думаю, пол, возраст и определение эмоций и другие атрибуты обнаруженного лица.

## <a name="setup"></a>Настройка

В этом руководстве предполагается, что вы уже построены **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** объект, названный `faceClient`, лиц подписки ключа и конечной точки URL-адрес. На этой странице можно использовать функцию обнаружения лиц с помощью вызова **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (используемые в данном руководстве) или **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. См. в разделе [обнаружения лиц краткое руководство по C# ](../quickstarts/csharp-detect-sdk.md) инструкции о том, как выполнить такую настройку.

В этом руководстве основное внимание уделяется особенности вызова найти&mdash;какие аргументы можно передать и что делать с возвращенными данными. Рекомендуется, только запрашивая необходимых компонентов, так как каждая операция занимает больше времени для завершения.

## <a name="get-basic-face-data"></a>Получение данных основные лиц

Чтобы найти лиц и получить их позиций в изображение, вызовите метод с _returnFaceId_ параметру присвоить **true** (по умолчанию).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Возвращенный **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** объектов, которые могут запрашиваться для их уникальные идентификаторы и прямоугольник, который предоставляет координаты точки поверхности.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

См. в разделе **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** сведения о том, как выполнить синтаксический анализ, расположение и размеры начертание шрифта. Как правило включает этот прямоугольник глаза, бровей, нос и рот; вверху head, уши и чэнь, обязательно не включаются. Если вы планируете использовать лицо в рамке обрезать полный head или среднего снимок Книжная (фото идентификатор типа образа), может потребоваться Растяните прямоугольник по определенные поля в каждом направлении.

## <a name="get-face-landmarks"></a>Получить позу

Позу: для поиска точек на грани например учеников, или кончика нос. Можно получить данные ориентиров лиц, задав _returnFaceLandmarks_ параметр **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

По умолчанию используются 27 предопределенных точек ориентиров. На следующем рисунке показана все 27 точки:

![Схема лиц с меткой все 27 ориентиров](../Images/landmarks.1.jpg)

Возвращаемый моментов в единицах точек, так же, как кадр прямоугольник лиц. В следующем коде показано, как можно извлечь расположения нос и учеников:

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

Данные ориентиров лиц могут также использоваться для точного подсчета направление поверхности. Например можно определить поворот поверхности как вектор от центра рот относительно центральной части глаза. Приведенный ниже код вычисляет данного вектора:

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

Зная направление поверхности, можно повернуть кадр прямоугольный лиц и выровнять ее более должным образом. Если требуется обрезать лиц на изображении, можно программным образом Поворачивание изображения, фрагменты всегда отображаются вертикально.

## <a name="get-face-attributes"></a>Получить черт лица

Помимо лицевым прямоугольникам и ориентиры API распознавания лиц можно анализировать несколько атрибутов концептуальной грани. в частности такие:

- Age
- пол;
- интенсивность улыбки;
- растительность на лице;
- Очки
- 3D головной поза
- API распознавания эмоций

> [!IMPORTANT]
> Эти атрибуты по прогнозу алгоритмы, статистические и могут быть неточными. Будьте осторожны при принятии решений на основе данных атрибута.
>

Чтобы проанализировать черт лица, задайте _returnFaceAttributes_ параметр к списку **[перечисления FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** значения.

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

Затем получите ссылки на возвращаемые данные и выполнить более операций в соответствии с потребностями.

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

Дополнительные сведения о каждом из атрибутов, см. [Глоссарий](../Glossary.md).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как использовать различные функциональные возможности обнаружения лиц. Далее мы расскажем об [Глоссарий](../Glossary.md) для более подробно изучить полученные данные лиц.

## <a name="related-topics"></a>Связанные темы

- [Справочная документация (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Справочная документация (пакет SDK для .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
