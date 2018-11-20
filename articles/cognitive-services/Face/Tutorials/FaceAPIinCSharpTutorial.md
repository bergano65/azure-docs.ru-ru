---
title: Руководство. Определение лиц и отображение данных о них на изображении с использованием пакета SDK для .NET
titleSuffix: Azure Cognitive Services
description: С помощью этого руководства вы создадите простое приложение Windows, которое использует API распознавания лиц для определения и выделения лиц на изображении.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: pafarley
ms.openlocfilehash: 5319584d892c261dcc6290703e9ca6518640cc94
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685486"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Руководство. Создание приложения WPF для отображения данных о лицах на изображении

В этом руководстве вы узнаете, как с помощью API распознавания лиц Azure, поставляемого через клиентский пакет SDK для .NET, распознавать лица на изображении, а затем представлять эти данные в пользовательском интерфейсе. Вы создадите простое приложение Windows Presentation Framework (WPF), которое распознает лица, рисует рамку вокруг них и отображает описание лица в строке состояния. 

В этом учебнике описаны следующие процедуры.

> [!div class="checklist"]
> - создание приложения WPF;
> - установка клиентской библиотеки API распознавания лиц;
> - использование клиентской библиотеки для обнаружения лиц на изображении;
> - выделение рамкой каждого обнаруженного лица;
> - отображение описания выделенного лица в строке состояния.

![Снимок экрана, на котором обнаруженные лица выделены прямоугольниками](../Images/getting-started-cs-detected.png)

Полный пример кода доступен в репозитории [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) на сайте GitHub.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу. 


## <a name="prerequisites"></a>Предварительные требования

- Ключ подписки на API распознавания лиц. Вы можете получить ключ бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Или следуйте инструкциям в руководстве по [созданию учетной записи Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), чтобы получить подписку на API распознавания лиц и свой ключ.
- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

Чтобы создать проект нового приложения WPF, сделайте следующее.

1. В Visual Studio откройте диалоговое окно "Новый проект". Разверните **Установленные**, а затем — **Visual C#** и выберите **Приложение WPF (.NET Framework)**.
1. Присвойте приложению имя **FaceTutorial** и нажмите кнопку **ОК**.
1. Получите необходимые пакеты NuGet. Щелкните проект правой кнопкой мыши в обозревателе решений и выберите **Управление пакетами NuGet**, а затем найдите и установите следующий пакет:
    - Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview.

## <a name="add-the-initial-code"></a>Добавление исходного кода

В этом разделе вы добавите базовую платформу приложения без функций обработки лиц.

### <a name="create-the-ui"></a>Создание пользовательского интерфейса

Откройте файл *MainWindow.xaml* и замените его содержимое приведенным ниже кодом, который создаст окно интерфейса. Обратите внимание, что `FacePhoto_MouseMove` и `BrowseButton_Click` являются обработчиками событий, которые будут определены позже.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>Создание главного класса

Откройте файл *MainWindow.xaml.cs* и добавьте пространства имен клиентской библиотеки, а также другие необходимые пространства имен. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

Вставьте следующий код в класс **MainWindow**. Он создает экземпляр **FaceClient** с использованием ключа подписки, который нужно ввести самостоятельно. Для параметра `faceEndpoint` нужно указать строку с правильным регионом для вашей подписки.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

Вставьте приведенный ниже код в метод **MainWindow**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

Наконец, добавьте методы **BrowseButton_Click** и **FacePhoto_MouseMove** в класс. Они соответствуют обработчикам событий, объявленным в файле *MainWindow.xaml*. Метод **BrowseButton_Click** создает объект **OpenFileDialog**, что позволяет пользователю выбрать изображение в формате JPG. В главном окне будет выведено событие. Вы вставите оставшийся код для **BrowseButton_Click** и **FacePhoto_MouseMove** на последующих шагах. Обратите также внимание на ссылку `faceList` &mdash;список объектов **DetectedFace**. Здесь ваше приложение будет хранить и вызывать фактические данные о лицах.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>Использование приложения

В меню выберите команду **Начать**, чтобы протестировать приложение. В открывшемся окне приложения в левом нижнем углу нажмите кнопку **Обзор**. Появится диалоговое окно **открытия файла**. Выберите изображение из файловой системы и убедитесь, что оно отображается в окне. Закройте приложение и перейдите к следующему шагу.

![Снимок экрана с фотографией лиц без изменений](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Передача изображений и определение лиц

Ваше приложение будет распознавать лица, вызывая метод **FaceClient.Face.DetectWithStreamAsync**, который инкапсулирует REST API [обнаружения](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) для передачи локального изображения.

Вставьте следующий метод в класс **MainWindow** ниже метода **FacePhoto_MouseMove**. Он определяет список атрибутов лица для извлечения и считывает представленный файл изображения в **потоке**. Затем он передает оба этих объекта в вызов метода **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>Размещение прямоугольников вокруг лиц

Теперь нужно вставить код, чтобы добавить прямоугольник вокруг каждого распознанного лица на изображении. В классе **MainWindow** вставьте следующий код в конце метода **BrowseButton_Click** после строки `FacePhoto.Source = bitmapSource`. Он позволяет заполнить список распознанных лиц из вызова **UploadAndDetectFaces**. Затем вокруг каждого лица рисуется прямоугольник, и измененное изображение отображается в главном окне.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>Описания лиц

Добавьте следующий метод в класс **MainWindow** ниже метода **UploadAndDetectFaces**. Этот метод преобразует полученные атрибуты лица в строку описания лица.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=228-286)]

## <a name="display-the-face-description"></a>Отображение описания лица

Добавьте следующий код в метод **FacePhoto_MouseMove**. Этот обработчик событий отображает строку описания лица в `faceDescriptionStatusBar`, когда курсор наводится на прямоугольник распознанного лица.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=151-186)]


## <a name="run-the-app"></a>Запуск приложения

Запустите приложение и с помощью обзора найдите изображение, содержащее лицо. Подождите несколько секунд, чтобы служба распознавания лиц смогла ответить. На каждом лице на изображении отобразится красный прямоугольник. При наведении указателя мыши на прямоугольник вокруг лица в строке состояния появится описание лица.

![Снимок экрана, на котором обнаруженные лица выделены прямоугольниками](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Дополнительная информация

С помощью этого руководства вы изучили базовый процесс использования пакета SDK NET для службы распознавания лиц и создали приложение, в котором лица на изображениях отображаются и выделяются рамкой. Ознакомьтесь с дополнительными сведениями об определении лиц.

> [!div class="nextstepaction"]
> [Как обнаруживать лица на изображении](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
