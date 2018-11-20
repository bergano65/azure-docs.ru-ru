---
title: Краткое руководство. Распознавание лиц на изображении с помощью пакета SDK распознавания лиц Azure для .NET
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как определять лица на изображении с помощью пакета SDK распознавания лиц Azure и C#.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: pafarley
ms.openlocfilehash: 4fbbde167a8c895a71ab3614e8c3ecbce26604a9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578167"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Краткое руководство. Распознавание лиц на изображении с помощью пакета SDK распознавания лиц для .NET

В этом кратком руководстве описано, как определять лица на изображении с помощью пакета SDK распознавания лиц и C#. Рабочий пример кода, используемого в этом кратком руководстве, можно найти в проекте Face в репозитории [Cognitive Services Vision csharp quickstarts](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) на GitHub.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования

- Ключ подписки на API распознавания лиц. Вы можете получить ключ бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Или следуйте инструкциям в руководстве по [созданию учетной записи Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), чтобы получить подписку на API распознавания лиц и свой ключ.
- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

1. В Visual Studio создайте проект **Консольное приложение (.NET Framework)** и назовите его **FaceDetection**. 
1. При наличии других проектов в решении выберите этот в качестве единого запускаемого проекта.
1. Получите необходимые пакеты NuGet. В обозревателе решений щелкните свой проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. Нажмите кнопку **Обзор** и выберите **Включить предварительные выпуски**, а затем найдите и установите следующий пакет:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview).

## <a name="add-face-detection-code"></a>Добавление кода определения лиц

Откройте файл *Program.cs* нового проекта. Здесь вы добавите код, необходимый для загрузки изображений и определения лиц.

### <a name="include-namespaces"></a>Включение пространства имен

Добавьте следующие инструкции `using` в начало файла *Program.cs*.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Добавление важных полей

Добавьте следующие поля в класс **Program** . Они указывают, как подключиться к службе распознавания лиц и где получить входные данные. Вам нужно будет обновить поле `subscriptionKey`, указав значение вашего ключа подписки, и может потребоваться изменить строку `faceEndpoint`, чтобы она содержала идентификатор правильного региона. Необходимо также задать для `localImagePath` и (или) `remoteImageUrl` пути, которые указывают на фактические файлы изображений.

Поле `faceAttributes` — это просто массив определенных типов атрибутов. Оно определяет, какую информацию о распознанных лицах нужно получить.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=13-34)]

### <a name="create-and-use-the-face-client"></a>Создание и использование клиента службы распознавания лиц

Далее добавьте следующий код в метод **Main** класса **Program**. Он настраивает клиент API распознавания лиц.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=38-41)]

Кроме того, в методе **Main** добавьте следующий код, чтобы использовать созданный клиент службы распознавания лиц для определения лиц на удаленном и локальном изображениях. Методы распознавания лиц будут определены в следующем разделе. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-49)]

### <a name="detect-faces"></a>Распознавание лиц

Добавьте следующий метод в класс **Program**. Он использует клиент службы распознавания лиц для определения лиц на удаленном изображении, указанном по URL-адресу. Обратите внимание, что он использует поле `faceAttributes` &mdash; объекты **DetectedFace**, добавленные в `faceList`, будут иметь указанные атрибуты (в этом случае возраст и пол).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

Аналогичным образом добавьте метод **DetectLocalAsync**. Он использует клиент службы распознавания лиц для определения лиц на локальном изображении, указанном по пути к файлу.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Извлечение и отображение атрибутов лица

Теперь определите метод **GetFaceAttributes**. Он возвращает строку с информацией о соответствующих атрибутах.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Наконец, определите метод **DisplayAttributes** для записи данных об атрибутах лица в выходные данные консоли.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-123)]

## <a name="run-the-app"></a>Запуск приложения

При успешном ответе отобразится пол и возраст каждого лица на изображении. Например: 

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали простое консольное приложение .NET, которое может использовать службу API распознавания лиц для распознавания лиц на локальных и удаленных изображениях. Перейдите к более подробному руководству, чтобы узнать, как можно интуитивно понятным образом предоставлять информацию о лицах пользователю.

> [!div class="nextstepaction"]
> [Руководство. Создание приложения WPF для обнаружения и выделения лиц на изображении с помощью пакета SDK .NET](../Tutorials/FaceAPIinCSharpTutorial.md)
