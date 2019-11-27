---
title: Краткое руководство. Клиентская библиотека Content Moderator для .NET | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Приступите к работе с клиентской библиотекой Content Moderator для .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: 5b287253e471ced244db3169be9f7d8f14e5862e
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045224"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Краткое руководство. Клиентская библиотека Content Moderator для .NET

Приступите к работе с клиентской библиотекой Content Moderator для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Content Moderator — это когнитивная служба, которая проверяет текст, изображения и видео на наличие потенциально оскорбительных, представляющих риск или нежелательных по иным причинам материалов. При обнаружении таких материалов служба применяет к содержимому соответствующие метки (флаги). Затем приложение может обрабатывать помеченное содержимое для обеспечения соответствия нормативным требованиям или оставлять его как предполагаемую среду для пользователей.

Клиентскую библиотеку Content Moderator для .NET можно использовать для такой задачи:

* [модерация текста](#moderate-text);
* [модерация изображений](#moderate-images);
* [создание проверки](#create-a-review).

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Примеры](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Настройка

### <a name="create-a-content-moderator-azure-resource"></a>Создание ресурса Azure для Content Moderator

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Content Moderator с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [интерфейса командной строки Azure (CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* Получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки [задайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и URL-адреса конечной точки с именами `CONTENT_MODERATOR_SUBSCRIPTION_KEY` и `CONTENT_MODERATOR_ENDPOINT` соответственно.

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или текстовом редакторе. 

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `content-moderator-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

В каталоге проекта откройте файл *Program.cs* в предпочитаемом редакторе или интегрированной среде разработки. Добавьте следующие операторы `using` :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

Затем создайте переменные среды для расположения конечной точки ресурса и ключа в классе **Program**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Если вы создали переменные среды после запуска приложения, для доступа к переменным следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эти переменные были запущены.

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В каталоге приложения установите клиентскую библиотеку Content Moderator для .NET с помощью следующей команды:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.

## <a name="object-model"></a>Объектная модель

Следующие классы обрабатывают некоторые основные функции пакета SDK .NET для Content Moderator.

|ИМЯ|ОПИСАНИЕ|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Этот класс требуется для всех функций Content Moderator. Вы создаете его экземпляр с информацией о подписке и используете его для создания экземпляров других классов.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Этот класс предоставляет функции для анализа изображений на наличие содержимого для взрослых, личной информации или лиц людей.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Этот класс предоставляет функции для анализа текста с целью определения языка, наличия ненормативной лексики, ошибок и личной информации.|
|[Проверки](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Этот класс предоставляет функциональные возможности API-интерфейсов проверки, в том числе методы создания заданий, настраиваемых рабочих процессов и пользовательских проверок.|

## <a name="code-examples"></a>Примеры кода


Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Content Moderator для .NET:

* [аутентификация клиента](#authenticate-the-client);
* [модерация текста](#moderate-text);
* [модерация изображений](#moderate-images);
* [создание проверки](#create-a-review).

## <a name="authenticate-the-client"></a>Аутентификация клиента

В новом методе создайте клиентские объекты с использованием конечной точки и ключа. Вам не нужен другой клиент для каждого сценария, но он поможет поддерживать организацию кода.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Модерация текста

В следующем коде используется клиент Content Moderator для анализа текста и вывода результатов в консоль. В корне класса **Program** определите входные и выходные файлы:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Затем в корне проекта добавьте файл *TextFile.txt*. Добавьте в этот файл свой текст или используйте текст из этого примера:

```
Is this a grabage email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
Crap is the profanity here. Is this information PII? phone 3144444444
```

Добавьте следующий вызов метода в метод `Main`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Затем в любом месте в классе **Program** определите метод модерации текста:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Модерация изображений

В следующем коде используется клиент Content Moderator и объект [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) для анализа удаленных изображений на наличие содержимого для взрослых и содержимого непристойного характера.

> [!NOTE]
> Можно также проанализировать содержимое локального изображения. Методы и операции, работающие с локальными изображениями, см. в [справочной документации](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

### <a name="get-sample-images"></a>Получить образцы изображений

Определите входные и выходные файлы:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Затем в корне проекта создайте входной файл *ImageFiles.txt*. В этом файле вы добавите URL-адреса изображений для анализа (по одному URL-адресу в каждой строке). Вы можете использовать следующий пример изображений:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Передайте входные и выходные файлы в следующий вызов метода в методе `Main`. Этот метод будет определен позднее.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Определение вспомогательного класса

Добавьте следующее определение класса в класс **Program**. Этот внутренний класс обработает результаты модерации изображений.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Определение метода модерации изображений

Следующий метод выполняет перебор изображений по их URL-адресам в текстовом файле, создает экземпляр **EvaluationData** и анализирует изображение на наличие содержимого для взрослых и непристойного содержимого, текста и человеческих лиц. Затем он добавляет в список окончательный экземпляр **EvaluationData** и записывает в консоль полный список возвращенных данных.

#### <a name="iterate-through-image-urls"></a>Перебор изображений по URL-адресам

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Анализ содержимого

Дополнительные сведения об атрибутах изображения, доступных в Content Moderator, см. в разделе [Основные принципы модерации изображений](./image-moderation-api.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Запись результатов модерации в файл

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Создание проверки

С помощью пакета SDK .NET для Content Moderator можно передать содержимое в [средство проверки](https://contentmoderator.cognitive.microsoft.com) для его проверки человеком. Чтобы узнать больше о средстве проверки, ознакомьтесь с разделом [Средство проверки Content Moderator](./review-tool-user-guide/human-in-the-loop.md).

В этом разделе метод использует класс [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) для создания проверки, получения ее идентификатора и уточнения сведений о ней после выполнения действий пользователем-модератором на веб-портале средства проверки. Все эти сведения записываются в выходной текстовый файл. Вызовите метод из метода `Main`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Получить образцы изображений

Объявите следующий массив в корне класса **Program**. Эта переменная ссылается на образец изображения, используемый для создания проверки.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Получение учетных данных проверки

Войдите в [средство проверки](https://contentmoderator.cognitive.microsoft.com), чтобы узнать имя своей группы проверки. Затем назначьте его соответствующей переменной в классе **Program**. Вы можете также настроить конечную точку обратного вызова для получения сведений о действиях проверки.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Определение вспомогательного класса

Добавьте следующее определение класса в свой класс **Program**. Этот класс будет использоваться для представления отдельного экземпляра проверки, отправляемого в средство проверки.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Определение вспомогательного метода

Добавьте следующий метод в класс **Program**. Этот метод будет записывать результаты запросов проверки в выходной текстовый файл.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Определение метода создания проверки

Теперь можно приступать к определению метода, который будет создавать проверку и выполнять ее запрос. Добавьте новый метод **CreateReviews** и определите следующие локальные переменные.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Действия после проверки в средстве проверки

Затем добавьте следующий код для перебора заданных образцов изображений, добавления метаданных и последующей их передачи в средство проверки одним пакетом. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

Объект, возвращенный из вызова API, будет содержать уникальные значения идентификатора для каждого переданного изображения. Следующий код анализирует эти идентификаторы, а затем использует их, чтобы запросить Content Moderator о состоянии каждого изображения в пакете.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Получение сведений о проверке

Следующий код вынуждает программу ожидать ввода данных пользователем. Когда вы приступите к этому шагу во время выполнения, можно перейти к [средству проверки](https://contentmoderator.cognitive.microsoft.com), убедиться, что образец изображения был отправлен, и начать взаимодействовать с ним. Сведения о том, как взаимодействовать с проверкой, см. в статье [Руководство по проверкам](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). По завершении можно нажать любую клавишу, чтобы продолжить выполнение программы и получить результаты процесса проверки.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

Если в этом сценарии использовалась конечная точка обратного вызова, в ней должно быть получено событие в таком формате:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение из каталога приложения с помощью команды `dotnet run`.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация


Из этого краткого руководстве вы узнали, как модерировать содержимое с помощью библиотеки .NET для Content Moderator. Теперь узнайте больше о модерации изображений или другого мультимедиа, прочитав концептуальное руководство.

> [!div class="nextstepaction"]
> [Принципы модерации изображений](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Что такое Azure Content Moderator?](./overview.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs).
