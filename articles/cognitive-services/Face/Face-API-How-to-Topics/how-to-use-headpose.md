---
title: Используется для настройки лицо в рамке HeadPose
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать атрибут HeadPose для автоматического поворота лицо в рамке.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576506"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>Использовать атрибут HeadPose для корректировки лицо в рамке

В этом руководстве будет использовать атрибут обнаруженного лица, HeadPose, чтобы повернуть прямоугольник лиц объекта. В образце кода в этом руководстве из [WPF распознавания лиц Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) пример приложения, использует пакет SDK для .NET.

Лицо в рамке, возвращенного с каждого обнаруженного лица, отмечает расположение и размер лица на изображении. По умолчанию прямоугольник всегда выравнивается с изображением (его стороны равны вполне вертикальные и горизонтальные); Это может быть неэффективен для кадрирования наклонный лиц. В ситуациях, где необходимо программно обрезать лиц на изображении удобно иметь возможность поворота прямоугольника обрезки.

## <a name="explore-the-sample-code"></a>Изучение примера кода

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

## <a name="display-the-updated-rectangle"></a>Отображение обновленных прямоугольника

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

## <a name="next-steps"></a>Дальнейшие действия

См. в разделе [WPF распознавания лиц Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) приложение на сайте GitHub рабочий пример повернутого лиц прямоугольников. См. в разделе [пример HeadPose API распознавания лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) приложение, которое отслеживает атрибут HeadPose в режиме реального времени для обнаружения различных головной перемещений (nodding, встряхните).