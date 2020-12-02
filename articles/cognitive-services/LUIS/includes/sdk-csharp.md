---
title: включить файл
description: включить файл
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: a79f164a1733c46a4cca807403a7ead8df446f0a
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303678"
---
С помощью клиентских библиотек Распознавания речи (LUIS) для .NET вы можете:
* Создание приложения
* добавить намерение, сущность, по которой выполнено машинное обучение, с примером речевого фрагмента;
* Обучение и публикация приложения.
* Среда выполнения прогнозирующих запросов

[Справочная документация](/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Разработка](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) и [Прогнозирование](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) Исходный код библиотеки | [Разработка](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) и [Прогнозирование](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) NuGet | [Пример C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>Обязательные условия

* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) и [.NET Core CLI](/dotnet/core/tools/).
* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Получив подписку Azure, [создайте ресурс LUIS](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) на портале Azure, чтобы получить ключ и конечную точку. Дождитесь, пока закончится развертывание, и нажмите кнопку **Перейти к ресурсу**.
    * Для подключения приложения к LUIS для разработки потребуется ключ и конечная точка для [созданного](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве. Вы можете использовать ценовую категорию "Бесплатный" (`F0`), чтобы поработать со службой.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе.

1. В окне консоли (cmd, PowerShell или Bash) выполните команду dotnet `new`, чтобы создать консольное приложение с именем `language-understanding-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Измените каталог на созданную папку приложения.

    ```dotnetcli
    cd language-understanding-quickstart
    ```

1. Чтобы создать приложение, выполните следующую команду:

    ```dotnetcli
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

### <a name="install-the-nuget-libraries"></a>Установка библиотек NuGet

В каталоге приложения установите клиентские библиотеки LUIS для .NET с помощью следующих команд:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>Разработка объектной модели

Клиент для разработки LUIS является объектом [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet), который проходит проверку подлинности в Azure и содержит ваш ключ разработки.

## <a name="code-examples-for-authoring"></a>Примеры кода для разработки

После создания используйте клиент для доступа к функциям, включая:

* Приложения — [создание](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [удаление](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [публикация](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet).
* Примеры речевых фрагментов — [добавление](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions?view=azure-dotnet), [удаление по идентификатору](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet).
* Компоненты — управление [списками фраз](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet).
* Модель — управление [намерениями](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) и сущностями.
* Шаблоны — управление [шаблонами](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet).
* Обучение — [обучение](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) приложения и опрос [состояния обучения](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet).
* [Версии](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) — управление с помощью клонирования, экспорта и удаления.

## <a name="prediction-object-model"></a>Объектная модель прогнозирования

Клиент для среды выполнения прогнозирования LUIS представляет собой объект [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet), который проходит проверку подлинности в Azure и содержит ваш ключ ресурса.

## <a name="code-examples-for-prediction-runtime"></a>Примеры кода для среды выполнения прогнозирования

После создания используйте клиент для доступа к функциям, включая:

* Прогнозирование по [слоту промежуточного хранения или рабочему слоту](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Прогнозирование по [версии](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Добавление зависимостей

В каталоге проекта откройте файл *Program.cs* в предпочитаемом редакторе или интегрированной среде разработки. Замените существующий код `using` следующими директивами `using`.

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Добавление стандартного кода

1. Измените подпись метода `Main`, чтобы разрешить асинхронные вызовы:

    ```csharp
    public static async Task Main()
    ```

1. Добавьте остальную часть кода в метод `Main` для класса `Program` (если не указано иное).

## <a name="create-variables-for-the-app"></a>Создание переменных для приложения

Создайте два набора переменных, один из которых вам нужно изменить, а второй оставить таким, как показано в примере кода. 

1. Создайте переменные для хранения ключа разработки и имен ресурсов.

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. Создайте переменные для хранения конечных точек, имени приложения, версии и имени намерения.

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Теперь создайте объект [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) с помощью ключа и используйте его со своей конечной точкой, чтобы создать объект [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet).

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Создание приложения LUIS

Приложение LUIS содержит модель обработки естественного языка (NLP), в том числе намерения, сущности и примеры речевых фрагментов.

Создайте [ApplicationCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Название, язык и региональные параметры являются обязательными свойствами. Вызовите метод [Apps.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet). Ответ — это идентификатор приложения.

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Создание намерения для приложения
Основным объектом в модели приложения LUIS является намерение. Намерение совпадает с группировкой _намерений_ пользовательских речевых фрагментов. Пользователь может задать вопрос или сделать инструкцию в поисках конкретного _намеренного_ ответа от бота (или другого клиентского приложения). Примерами намерений являются бронирование рейса, запрос о погоде в городе назначения и запрос контактной информации для обслуживания клиентов.

Создайте [ModelCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) с именем уникального намерения, затем передайте идентификатор приложения, идентификатор версии и ModelCreateObject в метод [Model.AddIntentAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet). Ответ — это идентификатор намерения.

Значение `intentName` жестко запрограммировано в `OrderPizzaIntent` как одна из переменных, описанных в разделе [Создание переменных для приложения](#create-variables-for-the-app).

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Создание сущностей для приложения

Хотя сущности не требуются, они встречаются в большинстве приложений. Сущность извлекает информацию из речевого фрагмента пользователя, необходимую для выполнения намерения пользователя. Существует несколько типов [готовых](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) и пользовательских объектов, каждый из которых имеет собственные модели объекта преобразования данных (DTO).  Обычные готовые сущности, добавляемые в ваше приложение, включают в себя [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) и [ordinal](../luis-reference-prebuilt-ordinal.md).

Важно знать, что объекты не отмечены намерением. Они могут и обычно относятся ко многим намерениям. Только примеры пользовательских речевых фрагментов помечены для определенного единственного намерения.

Методы создания сущностей являются частью класса [Model](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet). Каждый тип сущности имеет собственную модель объекта преобразования данных (DTO), обычно содержащую слово `model` в пространстве имен [моделей](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet).

Код создания сущности создает сущность машинного обучения с вложенными сущностями и функциями, применяемыми к вложенным сущностям `Quantity`.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Снимок экрана: часть страницы портала с созданной сущностью машинного обучения с вложенными сущностями и функциями, применяемыми к вложенным сущностям Quantity.":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

Используйте следующий метод для класса, чтобы определить идентификатор вложенной сущности Quantity для назначения ей функций.

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Добавление примера высказывания в намерение

Чтобы определить намерение высказывания и извлечь сущности, приложению нужны примеры речевых фрагментов. Примеры должны быть нацелены на конкретное единственное намерение и должны отмечать все пользовательские объекты. Готовые объекты не нужно отмечать.

Добавьте примеры речевых фрагментов, создав список объектов [ExampleLabelObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet), по одному объекту для каждого примера высказывания. Каждый пример должен пометить все сущности словарем пар "имя — значение" имени и значения сущности. Значение сущности должно быть точно таким, как оно указано в тексте примера высказывания.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Снимок экрана: часть страницы с помеченным на портале примером речевого фрагмента.":::

Вызовите [Examples.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions?view=azure-dotnet) с идентификатором приложения, идентификатором версии и примером.

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Обучение приложения

После создания модели приложение LUIS необходимо обучить этой версии модели. Обученную модель можно использовать в [контейнере](../luis-container-howto.md) или [опубликовать](../luis-how-to-publish-app.md) в промежуточных или рабочих слотах.

Методу [Train.TrainVersionAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) требуется идентификатор приложения и идентификатор версии.

Очень маленькая модель, такая как в этом кратком руководстве, будет обучаться очень быстро. Обучение приложений рабочего уровня должно включать в себя вызов опроса с помощью метода [GetStatusAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_), чтобы определить, прошло ли обучение успешно. Ответ представляет собой список объектов [ModelTrainingInfo](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) с отдельным состоянием для каждого объекта. Все объекты должны быть успешно обучены, чтобы обучение считалось завершенным.

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Публикация приложения в рабочий слот

Опубликуйте LUIS, используя метод [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet). При этом публикуется текущая обученная версия в указанный слот в конечной точке. Ваше клиентское приложение использует эту конечную точку для отправки пользовательских высказываний для прогнозирования намерения и извлечения сущности.

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Аутентификация клиента среды выполнения прогнозирования

Используйте объект [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) с помощью ключа и примените его со своей конечной точкой, чтобы создать объект [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet).

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>Получение прогноза из среды выполнения

Добавьте следующий код, который создает запрос к среде выполнения прогнозирования.

Речевой фрагмент пользователя является частью объекта [PredictionRequest](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet).

Метод **GetSlotPredictionAsync** принимает для выполнения запроса несколько обязательных параметров, в том числе идентификатор приложения, имя слота и объект запроса прогнозирования. Есть еще несколько необязательных параметров, например для режимов подробного протоколирования, отображения всех намерений и ведения журнала.

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с помощью команды `dotnet run` из каталога приложения.

```dotnetcli
dotnet run
```