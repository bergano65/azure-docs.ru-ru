---
title: Краткое руководство. Клиентская библиотека компьютерного зрения для .NET | Документация Майкрософт
description: Начало работы с клиентской библиотекой службы "Компьютерное зрение" для .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 72c66241965db1264cb508ed1f8f4c76a07883be
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974995"
---
# <a name="quickstart-computer-vision-client-library-for-net"></a>Краткое руководство. Клиентская библиотека службы "Компьютерное зрение" для .NET

Начало работы с клиентской библиотекой службы "Компьютерное зрение" для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба "Компьютерное зрение" предоставляет доступ к передовым алгоритмам обработки изображений и возврата данных.

С помощью клиентской библиотеки службы "Компьютерное зрение" для .NET можно выполнять следующие операции:

* анализ изображений на наличие тегов, текстового описания, лиц, содержимого для взрослых и многого другого.
* распознавание печатного и рукописного текста с помощью API пакетной службы чтения.

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Примеры](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Настройка

### <a name="create-a-computer-vision-azure-resource"></a>Создание ресурса Компьютерного зрения в Azure

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Компьютерного зрения с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [интерфейса командной строки Azure (CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* Получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки [задайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и URL-адреса конечной точки с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе. 

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `computer-vision-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```console
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

В каталоге проекта откройте файл *Program.cs* в предпочитаемом редакторе или интегрированной среде разработки. Затем добавьте следующие `using` директивы:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

В классе приложения **Программа** создайте переменные для конечной точки и ключа Azure вашего ресурса.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В каталоге приложения установите клиентскую библиотеку службы "Компьютерное зрение" для .NET с помощью следующей команды:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы обрабатывают некоторые основные функции пакета SDK .NET для службы "Компьютерное зрение".

|ИМЯ|ОПИСАНИЕ|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Этот класс требуется для всех функций Компьютерного зрения. Вы создаете его экземпляр с информацией о подписке и используете его для выполнения большинства операций с образами.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Этот класс содержит дополнительные методы для **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Это перечисление определяет различные типы анализа изображений, которые можно выполнить в стандартной операции анализа. Набор значений VisualFeatureTypes указывается в зависимости от потребностей. |

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки службы "Компьютерное зрение" для .NET.

* [аутентификация клиента](#authenticate-the-client);
* [Анализ изображения](#analyze-an-image)
* [Чтение печатного и рукописного текста](#read-printed-and-handwritten-text).

## <a name="authenticate-the-client"></a>Аутентификация клиента

> [!NOTE]
> В этом кратком руководстве предполагается, что вы уже [создали переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа API и конечной точки Компьютерного зрения с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и соответственно `COMPUTER_VISION_ENDPOINT`.

В новом методе создайте экземпляр клиента с использованием конечной точки и ключа. Создайте объект [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) с помощью своего ключа и используйте его с вашей конечной точкой для создания объекта [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

Скорее всего, вы будете вызывать этот метод из метода `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Анализ изображения

В следующем коде определен метод `AnalyzeImageUrl`, который использует клиентский объект для анализа удаленного образа и печати результатов. Метод возвращает текстовое описание, категоризацию, список тегов, обнаруженные лица, флажки содержимого для взрослых, основные цвета и тип изображения.

Добавьте вызов метода в ваш метод `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Настройка тестового изображения

В классе **Program** сохраните ссылку на URL-адрес изображения, который вы хотите проанализировать.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> Можно также проанализировать локальный образ. См. пример кода на [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) для сценариев, включающих локальные образы.

### <a name="specify-visual-features"></a>Указание визуальных компонентов

Определите новый метод для анализа образов. Добавьте приведенный ниже код, в котором задаются визуальные компоненты, которые вы хотите извлечь при анализе. Полный список компонентов см. в описании перечисления [VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

### <a name="analyze"></a>Анализ

Метод **AnalyzeImageAsync** возвращает объект **ImageAnalysis**, содержащий всю извлеченную информацию.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

В следующих разделах подробно показано, как анализировать эти сведения.

### <a name="get-image-description"></a>Получение описания изображения

Следующий код получает список созданных заголовков для изображения. Дополнительные сведения см. в статье [Описание изображений на понятном для пользователя языке](../concept-describing-images.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Получение категории изображения

Следующий код получает обнаруженную категорию изображения. Дополнительные сведения см. в статье [Categorize images by subject matter](../concept-categorizing-images.md) (Классификация изображений по темам).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Получение тегов изображения

Следующий код получает набор обнаруженных тегов изображения. Дополнительные сведения см. в статье о [тегах содержимого](../concept-tagging-images.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Обнаружение объектов

Следующий код обнаруживает общие объекты в образе и выводит их на консоль. Дополнительные сведения см. в статье [Обнаружение объектов](../concept-object-detection.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Обнаружение торговых марок

Следующий код обнаруживает фирменные торговые марки и логотипы в образе и выводит их на консоль. Дополнительные сведения см. в статье [Обнаружение торговых марок](../concept-brand-detection.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Распознавание лиц

Следующий код возвращает обнаруженные лица на изображении с их координатами прямоугольника и атрибутами выбора лиц. Дополнительные сведения см. в статье [Определение лиц с помощью компьютерного зрения](../concept-detecting-faces.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Обнаружение содержимого для взрослых, непристойного или жуткого содержимого

Следующий код выводит сведения об обнаружении содержимого для взрослых на изображении. Дополнительные сведения см. в статье [Detect adult content](../concept-detecting-adult-content.md) (Обнаружение содержимого для взрослых).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Получение цветовой схемы изображения

Следующий код выводит обнаруженные атрибуты цвета на изображении, например доминирующие и акцентные цвета. Дополнительные сведения см. в статье [Обнаружение цветовых схем на изображениях](../concept-detecting-color-schemes.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Получение содержимого, связанного с определенной предметной областью

API "Компьютерное зрение" может использовать специализированные модели для дальнейшего анализа изображений. Дополнительные сведения см. в статье [Обнаружение содержимого, связанного с определенными предметными областями](../concept-detecting-domain-content.md). 

Следующий код анализирует данные об обнаруженных известных лицах на изображении.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

Следующий код анализирует данные об обнаруженных достопримечательностях на изображении.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Получение сведений о типе изображения

Следующий код выводит информацию о типе изображения, независимо от того, является ли это картинкой или рисунком.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Чтение печатного и рукописного текста

Компьютерное зрение может считывать видимый текст в образе и преобразовывать его в поток символов. Код в этом разделе определяет метод `ExtractTextUrl`, который использует клиентский объект для обнаружения и извлечения печатного или рукописного текста в образе.

Добавьте вызов метода в ваш метод `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Настройка тестового изображения

В классе **Программа** сохраните ссылку на URL-адрес образа, из которого вы хотите извлечь текст.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> Кроме того, можно извлечь текст из локального образа. См. пример кода на [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) для сценариев, включающих локальные образы.

### <a name="call-the-read-api"></a>Вызов API чтения

Определите новый метод для чтения текста. Добавьте приведенный ниже код, который вызывает метод **BatchReadFileAsync** для данного образа. Он возвращает идентификатор операции и запускает асинхронный процесс чтения содержимого образа.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Получение результатов чтения

Затем получите идентификатор операции, возвращенный из вызова **BatchReadFileAsync**, и используйте его для запроса в службу для результатов операции. Следующий код проверяет операцию с интервалами в одну секунду, пока не будут возвращены результаты. После этого извлеченные текстовые данные выводятся на консоль.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Отображение результатов чтения

Добавьте следующий код для анализа и отображения полученных текстовых данных и завершения определения метода.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение из каталога приложения с помощью команды `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Справочник по API службы "Компьютерное зрение" (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Что собой представляет Компьютерное зрение](../Home.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).
