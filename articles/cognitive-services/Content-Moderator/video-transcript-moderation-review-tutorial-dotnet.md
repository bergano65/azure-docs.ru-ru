---
title: Руководство. Модерация видео и расшифровок речи с использованием .NET — Content Moderator
titlesuffix: Azure Cognitive Services
description: В этом руководстве содержатся сведения о создании решения модерации видео и расшифровок с использованием автоматизированной модерации и создания проверок человеком в процессе.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 504f79186eb69fb6e6c23c1a0cd9dfd7584bb128
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904343"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Руководство. Модерация видео и расшифровок речи

В этом руководстве содержатся сведения о создании полного решения для модерации видео и расшифровок с использованием автоматизированной модерации и создания проверок с вовлечением в процесс человека.

В этом учебнике описаны следующие процедуры.

> [!div class="checklist"]
> - сжатие входных видеоданных для ускорения обработки;
> - модерация видео для получения снимков и кадров с аналитическими сведениями;
> - использование меток времени кадров для создания эскизов (изображений);
> - отправка меток времени и эскизов для создания проверок видео;
> - преобразование речи видео в текст (расшифровка) с помощью API индексатора мультимедийных данных;
> - модерация расшифровки с помощью службы модерации текста;
> - добавление прошедших модерацию расшифровок в проверку видео.

## <a name="prerequisites"></a>Предварительные требования

- Зарегистрируйтесь на сайте ](https://contentmoderator.cognitive.microsoft.com/)Средства проверки Content Moderator[ и создайте пользовательские теги. Если на этом этапе вам нужна помощь, ознакомьтесь со статьей [Создание и использование тегов для модерации](Review-Tool-User-Guide/tags.md).

    ![Снимок экрана пользовательских тегов для модерации видео](images/video-tutorial-custom-tags.png)
- Для запуска примера приложения необходимы учетная запись Azure, ресурс Служб мультимедиа Azure, ресурс Azure Content Moderator и учетные данные Azure Active Directory. Инструкции о том, как их получить, см. в руководстве [Анализ содержимого видео для выявления нежелательного содержимого в C#](video-moderation-api.md).
- Скачайте [консольное приложение для просмотра видео](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) из GitHub.

## <a name="enter-credentials"></a>Ввод учетных данных

Измените файл `App.config` и добавьте имя клиента, конечные точки службы и ключи подписки Active Directory, помеченные `#####`. Вам понадобятся следующие сведения:

    |Ключ|ОПИСАНИЕ|
    |-|-|
    |`AzureMediaServiceRestApiEndpoint`|Конечная точка для API Служб мультимедиа Azure (AMS)|
    |`ClientSecret`|Ключи подписки для Службы мультимедиа Azure|
    |`ClientId`|Идентификатор клиента для Служб мультимедиа Azure|
    |`AzureAdTenantName`|Имя клиента Active Directory, представляющего вашу организацию|
    |`ContentModeratorReviewApiSubscriptionKey`|Ключ подписки для API проверки Content Moderator|
    |`ContentModeratorApiEndpoint`|Конечная точка для API Content Moderator|
    |`ContentModeratorTeamId`|Идентификатор команды Content Moderator|

## <a name="examine-the-main-code"></a>Просмотр основного кода

Класс `Program` в `Program.cs` — главная точка входа в приложение модерации видео.

### <a name="methods-of-program-class"></a>Методы класса Program

|Метод|ОПИСАНИЕ|
|-|-|
|`Main`|Анализирует командную строку, собирает входные данные пользователя и начинает обработку|
|`ProcessVideo`|Сжимает, отправляет, выполняет модерацию и создает проверки видео|
|`CreateVideoStreamingRequest`|Создает поток для отправки видео|
|`GetUserInputs`|Собирает входные данные пользователя; используется при отсутствии параметров командной строки|
|`Initialize`|Инициализирует объекты, необходимые для процесса модерации|

### <a name="the-main-method"></a>Метод Main

`Main()` используется в начале выполнения, поэтому с него нужно начинать изучать процесс модерации видео.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` обрабатывает следующие аргументы командной строки:

- Путь к каталогу, содержащему файлы видео MPEG-4 для отправки на модерацию. Все файлы `*.mp4` в этом каталоге и его подкаталогах отправляются на модерацию.
- При необходимости логический флаг (true/false), указывающий, следует ли создавать расшифровки текста для модерации аудио.

Если аргументов командной строки нет, `Main()` вызывает `GetUserInputs()`. Этот метод предлагает пользователю ввести путь к одному видеофайлу и указать, нужно ли создать расшифровку текста.

> [!NOTE]
> В консольном приложении используется [API индексатора мультимедийных данных Azure](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) для создания расшифровок из звуковой дорожки отправленного видео. Результаты предоставляются в формате WebVTT. Дополнительные сведения об этом формате см. в статье [Web Video Text Tracks Format (WebVTT)](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Формат Web Video Text Tracks (WebVTT)).

### <a name="initialize-and-processvideo-methods"></a>Инициализация и методы ProcessVideo

Независимо от того, поступили ли параметры программы из командной строки или от интерактивного пользователя, `Main()` вызывает `Initialize()` для создания следующих экземпляров:

|Класс|ОПИСАНИЕ|
|-|-|
|`AMSComponent`|Сжимает файлы видео перед их отправкой на модерацию|
|`AMSconfigurations`|Связывается с помощью интерфейса с данными конфигурации приложения в `App.config`|
|`VideoModerator`| Отправка, кодирование, шифрование и модерация с помощью пакета SDK AMS|
|`VideoReviewApi`|Управляет проверками видео в службе Content Moderator|

Эти классы (помимо `AMSConfigurations`, который является простым) рассматриваются более подробно в следующих разделах данного руководства.

Наконец, видеофайлы обрабатываются поочередно путем вызова `ProcessVideo()` для каждого из них.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

Метод `ProcessVideo()` очень простой. Он выполняет следующие операции в таком порядке:

- сжимает видео;
- отправляет видео в ресурс Служб мультимедиа Azure;
- создает задание AMS для модерации видео;
- создает проверку видео в Content Moderator.

В следующих разделах более подробно рассматриваются некоторые отдельные процессы, вызываемые с помощью `ProcessVideo()`. 

## <a name="compress-the-video"></a>Сжатие видео

Чтобы свести к минимуму трафик, приложение преобразовывает видеофайлы в формат H.264 (AVC MPEG-4) и масштабирует их до максимальной ширины в 640 пикселей. Кодек H.264 рекомендуется в связи с его высокой производительностью (степенью сжатия). Сжатие выполняется с помощью бесплатной программы командной строки `ffmpeg`, находящейся в папке `Lib` решения Visual Studio. Входные файлы могут быть любого формата, поддерживаемого `ffmpeg`, включая наиболее часто используемые форматы видеофайлов и кодеки.

> [!NOTE]
> При запуске программы с помощью параметров командной строки вы указываете каталог, содержащий видеофайлы, которые необходимо отправить на модерацию. Обрабатываются все файлы в этом каталоге с расширением имени файла `.mp4`. Чтобы обработать другие расширения имени файла, добавьте в метод `Main()` в `Program.cs` необходимые расширения.

Код, который сжимает один видеофайл, — это класс `AmsComponent` в `AMSComponent.cs`. Метод, который выполняет эту функцию, — `CompressVideo()`, показан ниже.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

Код выполняет следующие действия.

- Проверяет наличие в конфигурации `App.config` всех необходимых данных.
- Проверяет наличие двоичного объекта `ffmpeg`.
- Создает выходной файл путем добавления `_c.mp4` к базовому имени файла (например, `Example.mp4` -> `Example_c.mp4`).
- Создает строковое значение командной строки для выполнения преобразования.
- Запускает процесс `ffmpeg` с помощью командной строки.
- Ожидает обработку видео.

> [!NOTE]
> Если вы уверены, что ваши видео уже сжаты с использованием H.264 и у вас есть соответствующие измерения, вы можете переписать `CompressVideo()`, чтобы пропустить сжатие.

Метод возвращает имя сжатого выходного файла.

## <a name="upload-and-moderate-the-video"></a>Отправка и модерация видео

Видео должно храниться в Службах мультимедиа Azure, прежде чем их можно будет обрабатывать с помощью службы модерации контента. В классе `Program` файла `Program.cs` есть краткий метод `CreateVideoStreamingRequest()`, который возвращает объект, представляющий собой запрос потоковой передачи, используемый для отправки видео.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

Результирующий объект `UploadVideoStreamRequest` определен в `UploadVideoStreamRequest.cs` (и его родительский элемент `UploadVideoRequest` в `UploadVideoRequest.cs`). Эти классы не показаны здесь. Они краткие и предназначены только для хранения данных сжатого видео и сведений о нем. Еще один класс, предназначенный только для данных, `UploadAssetResult` (`UploadAssetResult.cs`), используется для хранения результатов процесса отправки. Теперь эти строки можно изучить в `ProcessVideo()`:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Эти строки выполняют следующие задачи:

- Создают `UploadVideoStreamRequest` для передачи сжатого видео.
- Задают флаг `GenerateVTT` запроса, если пользователь запросил расшифровку текста.
- Вызывают `CreateAzureMediaServicesJobToModerateVideo()` для отправки и получения результата.

## <a name="examine-video-moderation-code"></a>Изучение кода модерации видео

Метод `CreateAzureMediaServicesJobToModerateVideo()` находится в `VideoModerator.cs`, содержащем основную часть кода, который взаимодействует со Службами мультимедиа Azure. В следующем фрагменте показан исходный код этого метода.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Этот код выполняет следующие задачи:

- Создает задание AMS для обработки, которую необходимо выполнить.
- Добавляет задачи для кодирования видеофайла, его модерации и создания расшифровки текста.
- Отправляет задание, передает файл и начинает обработку.
- Извлекает результаты модерации, расшифровку текста (если запрошено) и другие сведения.

## <a name="sample-video-moderation-output"></a>Пример выходных данных модерации видео

Результат задания модерации видео (см. [краткое руководство по модерации видео](video-moderation-api.md)) — это структура данных JSON, содержащая результаты модерации. Эти результаты содержат декомпозицию фрагментов (снимков) в видео, каждый из которых содержит события (клипы) с ключевыми кадрами, помеченными для проверки. Каждый кадр оценивается по вероятности нахождения в нем содержимого для взрослых или материалов непристойного характера. Ниже приведен пример ответа JSON.

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

Если флаг `GenerateVTT` установлен, также выполняется транскрибирование аудиофайла.

> [!NOTE]
> В консольном приложении используется [API индексатора мультимедийных данных Azure](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) для создания расшифровок из звуковой дорожки отправленного видео. Результаты предоставляются в формате WebVTT. Дополнительные сведения об этом формате см. в статье [Web Video Text Tracks Format (WebVTT)](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Формат Web Video Text Tracks (WebVTT)).

## <a name="create-a-the-human-in-the-loop-review"></a>Создание проверки с вовлечением в процесс человека

Процесс модерации возвращает список ключевых кадров из видео вместе с расшифровкой его звуковых дорожек. Следующим шагом является создание проверки в средстве проверки Content Moderator для модераторов. Возвращаясь к методу `ProcessVideo()` в `Program.cs`, вы видите вызов к методу `CreateVideoReviewInContentModerator()`. Этот метод находится в классе `videoReviewApi`, который находится в `VideoReviewAPI.cs` и приведен ниже.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` вызывает несколько методов для выполнения следующих задач:

> [!NOTE]
> В консольном приложении для создания эскизов используется библиотека [FFmpeg](https://ffmpeg.org/). Эти эскизы (изображения) соответствуют меткам времени кадра в [выходных данных модерации видео](#sample-video-moderation-response).

|Задача|Методы|Файл|
|-|-|-|
|Извлечение ключевых кадров из видео и создание изображений эскизов|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Проверка расшифровки текста, если доступно, на наличие аудио содержимого для взрослых или материалов непристойного характера|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Подготовка и отправка запроса на проверку видео человеком|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

На следующем снимке экрана показаны результаты выполнения предыдущих шагов.

![Представление по умолчанию проверки видео](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Обработка расшифровки

До этого момента код, представленный в этом руководстве, ориентирован на визуальное содержимое. Проверка речевого содержимого — это отдельный и необязательный процесс, для которого, как упоминалось выше, используется расшифровка, созданная из аудио. Теперь рассмотрим создание расшифровки и использование ее в процессе проверки. Задача создания расшифровки отводится службе [индексатора мультимедийных данных Azure](https://docs.microsoft.com/azure/media-services/media-services-index-content).

Приложение выполняет такие задачи:

|Задача|Методы|Файл|
|-|-|-|
|Определить, нужно ли создать расшифровки текста|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Если да, отправить задание транскрибирования в рамках модерации|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Получить локальную копию расшифровки|`GenerateTranscript()`|`VideoModerator.cs`|
|Пометить кадры видео, которые содержат аудио с неприемлемым содержимым|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Добавить результаты в проверку|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Конфигурация задачи

Перейдем к отправке задания транскрибирования. `CreateAzureMediaServicesJobToModerateVideo()` (уже описано) вызывает `ConfigureTranscriptTask()`.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

Конфигурация для задачи расшифровки читается из файла `MediaIndexerConfig.json` в папке `Lib` решения. Ресурсы AMS создаются для файла конфигурации, а также для выходных данных процесса транскрибирования. При запуске задания AMS эта задача создает расшифровку текста из звуковой дорожки видеофайла.

> [!NOTE]
> Пример приложения распознает только английскую речь (США).

### <a name="transcript-generation"></a>Создание расшифровки

Расшифровка публикуется в качестве ресурса AMS. Для проверки расшифровки нежелательного содержимого приложение загружает ресурс из Служб мультимедиа Azure. `CreateAzureMediaServicesJobToModerateVideo()` вызывает `GenerateTranscript()`, показанный здесь, чтобы получить файл.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

После необходимой установки AMS фактическая загрузка выполняется путем вызова `DownloadAssetToLocal()`, универсальной функции, которая копирует ресурс AMS в локальный файл.

## <a name="moderate-the-transcript"></a>Модерация расшифровки

Эта расшифровка сканируется и используется в ходе проверки. Создание проверки — это область действия `CreateVideoReviewInContentModerator()`, который вызывает `GenerateTextScreenProfanity()` для выполнения этой работы. В свою очередь, этот метод вызывает `TextScreen()`, который содержит основные функциональные возможности.

`TextScreen()` выполняет следующие задачи:

- Анализ расшифровки на наличие меток времени и субтитров.
- Отправка субтитров на модерацию текста.
- Пометка любого кадра, который может содержать данные с нежелательным речевым содержимым.

Давайте рассмотрим все эти задачи более подробно.

### <a name="initialize-the-code"></a>Инициализация кода

Сначала инициализируйте все переменные и коллекции.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Анализ расшифровки на наличие субтитров

Далее проанализируйте расшифровку в формате VTT на наличие субтитров и меток времени. Средство просмотра отображает эти субтитры на вкладке "Запись разговоров" на экране просмотра видео. Метки времени используются для синхронизации субтитров с соответствующими видеокадрами.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Модерация субтитров с помощью службы модерации текста

Далее мы сканируем субтитры проанализированного текста с помощью API для работы с текстами Content Moderator.

> [!NOTE]
> Ключ службы Content Moderator предусматривает ограничение частоты на количество запросов в секунду (RPS). Если превысить ограничение, пакет SDK порождает исключение с кодом ошибки 429.
>
> Для ключа бесплатного уровня предусмотрен лимит в один запрос в секунду.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Разбивка модерации текста

`TextScreen()` — это фундаментальный метод, поэтому его необходимо разбить.

1. Сначала этот метод считывает файл расшифровки по одной строке. Он пропускает пустые строки и строки, содержащие `NOTE` с оценкой надежности. Он извлекает метки времени и текстовые элементы из *подсказок* в файле. Подсказки представляют собой текст из звуковой дорожки и содержат сведения о времени начала и окончания. Подсказка начинается со строки метки времени со строкой `-->`. За ней следует одна или несколько строк текста.

1. Экземпляры `CaptionScreentextResult` (определенные в `TranscriptProfanity.cs`) используются для хранения сведений, извлеченных из каждой подсказки.  При обнаружении строки метки времени или достижении максимальной длины текста в 1024 символов в `csrList` добавляется новый `CaptionScreentextResult`. 

1. Далее метод отправляет каждую подсказку в API модерации текста. Он вызывает `ContentModeratorClient.TextModeration.DetectLanguageAsync()` и `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, определенные в сборке `Microsoft.Azure.CognitiveServices.ContentModerator`. Чтобы избежать ограничений по скорости, метод приостанавливается на секунду перед отправкой каждой подсказки.

1. После получения результатов из службы модерации текста метод анализирует их, чтобы проверить, не превышают ли они пороговые значения надежности. Эти значения устанавливаются в `App.config` как `OffensiveTextThreshold`, `RacyTextThreshold` и `AdultTextThreshold`. Наконец, нежелательные термины также сохраняются. Все кадры в диапазоне времени подсказки отмечаются как содержащие текст с оскорбительным содержимым, материалами непристойного характера или для взрослых.

1. `TextScreen()` возвращает экземпляр `TranscriptScreenTextResult`, содержащий результат модерации текста из видео в целом. Этот объект содержит флаги и оценки для различных типов нежелательного содержимого вместе со списком всех нежелательных терминов. Вызывающий объект, `CreateVideoReviewInContentModerator()`, вызывает `UploadScreenTextResult()`, чтобы вложить эти сведения в проверку и сделать ее доступной для рецензентов.

На следующем снимке экрана показан результат создания расшифровки и шаги модерации.

![Представление расшифровки модерации видео](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Выходные данные программы

В следующих выходных данных командной строки программы показаны различные задачи по мере их выполнения. Кроме того, результат модерации (в формате JSON) и расшифровка речи доступны в том же каталоге, что и исходные видеофайлы.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как настроить приложение, которое модерирует видеоконтент &mdash;, в том числе транскрипты &mdash;, и создает проверки в инструменте проверки. Теперь переходите к подробному изучению модерации видео.

> [!div class="nextstepaction"]
> [Модерация видео](./video-moderation-human-review.md)
