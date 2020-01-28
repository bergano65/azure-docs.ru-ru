---
title: Использование атрибута HeadPose
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать атрибут HeadPose для автоматического вращения прямоугольной рамки вокруг лица или обнаружения жестов головой в видеотрансляции.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169789"
---
# <a name="use-the-headpose-attribute"></a>Использование атрибута HeadPose

Из этого руководства вы узнаете, как использовать атрибут HeadPose обнаруженного лица для реализации некоторых распространенных сценариев.

## <a name="rotate-the-face-rectangle"></a>Вращение прямоугольной рамки вокруг лица

Прямоугольная рамка вокруг лица, которая возвращается вместе с каждым обнаруженным лицом, отмечает расположение и размер этого лица на изображении. По умолчанию прямоугольник всегда выравнивается по изображению (его стороны расположены вертикально и горизонтально), но это не всегда удобно, если лицо повернуто под углом. Если нужно обрезать лицо в изображении с помощью программных средств, полезно иметь возможность поворачивать прямоугольник для обрезки.

В примере приложения [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) применяется атрибут HeadPose, который позволяет вращать прямоугольники для обнаруженных лиц.

### <a name="explore-the-sample-code"></a>Изучение примера кода

Вы можете программно вращать прямоугольную рамку вокруг лица с помощью атрибута HeadPose. Указав этот атрибут при обнаружении лиц (как описано [здесь](HowtoDetectFacesinImage.md)), вы сможете позднее получить его значение. Следующий метод из примера приложения [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) принимает список объектов **DetectedFace** и возвращает список объектов **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** . Здесь **Face** является пользовательским классом, который хранит данные о лицах, в том числе обновленные координаты прямоугольника. Вычисляются новые значения параметров **top** (верхний край), **left** (левый край), **width** (ширина) и **height** (высота), а новое поле **FaceAngle** обозначает вращение.

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

### <a name="display-the-updated-rectangle"></a>Отображение обновленного прямоугольника

Здесь вы можете использовать полученные объекты **Face** для отображения на экране. В следующих строках из файла [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) показано, как отображается новый прямоугольник на основе этих данных:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Обнаружение жестов головой

Вы можете обнаруживать жесты головой, например кивки и покачивания, отслеживая изменения HeadPose в реальном времени. Эту функцию можно использовать в качестве инструмента для обнаружения активности.

Задача обнаружения активности нужна для того, чтобы отличить реального человека от фиксированного изображения или видео. Детектор жестов головой может выполнять функцию обнаружения активности, которая наиболее эффективна при отсеве неподвижных изображений человека.

> [!CAUTION]
> Для обнаружения жестов головой в режиме реального времени необходимо обращаться к API распознавания лиц с высокой скоростью (более одного раза в секунду). Это невозможно выполнить, используя подписку уровня "Бесплатный" (F0). Для платной подписки не забудьте учесть расходы на частое выполнение вызовов API для обнаружения жестов.

Рабочий пример обнаружения жестов см. в примере [Face HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) на сайте GitHub.

## <a name="next-steps"></a>Дальнейшие действия

Рабочий пример с вращением прямоугольных рамок вокруг лица см. в примере приложения [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) на сайте GitHub. Также вы можете ознакомиться с примером приложения [Face HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples), которое отслеживает атрибут HeadPose в реальном времени для обнаружения движений головой.