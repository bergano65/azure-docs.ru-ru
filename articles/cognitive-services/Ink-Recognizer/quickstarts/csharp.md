---
title: Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и C#
titleSuffix: Azure Cognitive Services
description: Используйте API Распознавателя рукописного текста, чтобы начать распознавать рукописный текст.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 0c7d3ed7e2cbaee7d30f368efa004bbb3daaafdd
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996868"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и C#

Используйте сведения из этого краткого руководство, чтобы начать отправлять рукописные мазки в API Распознавателя рукописного текста. Это приложение C# отправляет запрос API, содержащий данные рукописного мазка в формате JSON, и возвращает ответ.

Хотя это приложение создается на языке C#, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Обычно вы вызываете API из приложения для рукописного ввода. Этот краткое руководство отправляет данные рукописного фрагмента для следующего рукописного образца из JSON-файла.

![изображение рукописного текста](../media/handwriting-sample.jpg)

Исходный код для этого краткого руководства можно найти на портале [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Предварительные требования

- Любой выпуск [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Чтобы установить Newtonsoft.Json в качестве пакета NuGet в Visual Studio, сделайте следующее.
        1. Щелкните правой кнопкой мыши на **Solution Manager**.
        2. Щелкните **Manage NuGet Packages...**
        3. Выполните поиск пакета `Newtonsoft.Json` и установите его.
- Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).

- Пример данных рукописного фрагмента для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Создание ресурса Распознавателя рукописного текста

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. В Visual Studio создайте новое консольное решение и добавьте следующие пакеты. 
    
    [!code-csharp[imports](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Создайте переменные для ключа подписки и конечной точки, а также пример файла JSON. Конечная точка позже будет совмещена с `inkRecognitionUrl` для доступа к API. 

    [!code-csharp[endpoint file path and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте новую асинхронную функцию с именем `Request`, принимающую переменные, созданные выше.

2. Установите протокол безопасности клиента и информацию заголовка с помощью объекта `HttpClient`. Не забудьте добавить ключ подписки в заголовок `Ocp-Apim-Subscription-Key`. Затем создайте объект `StringContent` для запроса.
 
3. Отправьте запрос с помощью `PutAsync()`. Отправьте ответ при успешном выполнении запроса.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Отправка запроса на распознавание рукописного текста

1. Создайте новую функцию с именем `recognizeInk()`. Создайте запрос, в которой будет включена конечная точка, ключ подписки, URL-адрес API и данные мазка кисты рукописного ввода, а затем отправьте его. Чтобы это сделать используйте функцию `Request()`.

2. Десериализируйте объект JSON и выведите его в консоль. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Загрузка данных рукописного ввода

Чтобы загрузить файл JSON с данных рукописного ввода создайте функцию `LoadJson()`. Используйте `StreamReader` и `JsonTextReader` чтобы создать `JObject` и вернуть его.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Отправка запроса API

1. В методе main в приложении необходимо использовать созданную ранее функцию для загрузки данных JSON. 

2. Вызовите созданную ранее функцию `recognizeInk()`. Чтобы после выполнения приложения оставить консоль открытой, используйте `System.Console.ReadKey()`.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Запуск приложения и просмотр ответа

Запустите приложение. Успешный ответ возвращается в формате JSON. Ответ JSON также можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Чтобы увидеть, как API Распознавателя рукописного текста работает в приложении для рукописного ввода, взгляните на следующие примеры приложений на GitHub.
* [C# и универсальная платформа Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# и Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Приложение браузера Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Мобильное приложение для Java и Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Мобильное приложение для Swift и iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
