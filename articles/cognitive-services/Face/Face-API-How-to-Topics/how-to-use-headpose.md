---
title: Используйте атрибут HeadPose
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать атрибут HeadPose автоматически повернуть лицо в рамке или обнаружения головной жесты на видео с веб-канала.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058577"
---
# <a name="use-the-headpose-attribute"></a>Используйте атрибут HeadPose

В этом руководстве вы увидите, как можно использовать атрибут HeadPose обнаруженного лица для поддержки некоторых важных сценариев.

## <a name="rotate-the-face-rectangle"></a>Повернуть прямоугольник лиц

Лицо в рамке, возвращенного с каждого обнаруженного лица, отмечает расположение и размер лица на изображении. По умолчанию прямоугольник всегда выравнивается с изображением (его сторон вертикальной и горизонтальной); Это может быть неэффективен для кадрирования наклонный лиц. В ситуациях, где необходимо программно обрезать лиц на изображении лучше иметь возможность поворота прямоугольника обрезки.

[WPF распознавания лиц Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) пример приложения с помощью атрибута HeadPose повернуть ее прямоугольники обнаруженного лица.

### <a name="explore-the-sample-code"></a>Изучение примера кода

С помощью атрибута HeadPose программными средствами можно поворачивать лицо в рамке. Если этот атрибут задан, при обнаружении лиц (см. в разделе [как обнаружить лица](HowtoDetectFacesinImage.md)), вы сможете запрашивать его позже. Следующий метод из [WPF распознавания лиц Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) приложение принимает список **DetectedFace** объектов и возвращает список **[лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** объекты. **Грани** здесь — это пользовательский класс, что магазины сталкиваются с данными, включая обновленные прямоугольными координатами. Новые значения вычисляются для **верхней**, **левой**, **ширины**, и **высота**и новое поле **FaceAngle**указывает поворот.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Отображение обновленных прямоугольника

На этой странице можно использовать возвращенный **лиц** объектов на экране. В следующих строках из [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) Показать, как новый прямоугольник подготавливается к просмотру из этих данных:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Обнаружить головной жесты

Вы можете обнаружить головного жестов, таких как nodding и head встряхните, отслеживая изменения HeadPose в режиме реального времени. Эту функцию можно использовать как обнаружения настраиваемые активности.

Обнаружение жизнеспособность — задача определения, что субъект — это человек, а не представлением изображение или видео. Детектор головной жестов можно использовать в качестве один из способов для проверки активности, особенно в отличие от представлений изображений человека.

> [!CAUTION]
> Чтобы обнаружить головной жестов в режиме реального времени, необходимо вызвать API распознавания лиц с высокой скоростью (более одного раза в секунду). Если у вас есть подписка бесплатная уровня (f0), это будет невозможно. Если у вас есть платная уровня подписки, убедитесь, что вычисления затрат на создание быстрой API вызовы для head жестов обнаружения.

См. в разделе [пример HeadPose API распознавания лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) на сайте GitHub рабочий пример головы жестов обнаружения.

## <a name="next-steps"></a>Дальнейшие действия

См. в разделе [WPF распознавания лиц Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) приложение на сайте GitHub рабочий пример повернутого лиц прямоугольников. См. в разделе [пример HeadPose API распознавания лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) приложение, которое отслеживает атрибут HeadPose в режиме реального времени для обнаружения головной перемещений.