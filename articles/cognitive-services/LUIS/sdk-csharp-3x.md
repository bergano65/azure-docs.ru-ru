---
title: Краткое руководство. Клиентская библиотека для разработки Распознавания речи (LUIS) версии 3 для .NET
titleSuffix: Azure Cognitive Services
description: Начните работу с клиентской библиотекой LUIS версии 3 для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0a6fd3921f301b269adad6c264b6b687462260e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74808022"
---
# <a name="quickstart-language-understanding-luis-client-library-3x-for-net"></a>Краткое руководство. Клиентская библиотека Распознавания речи (LUIS) версии 3 для .NET

Начните работу с клиентской библиотекой LUIS версии 3 для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.  Распознавание речи (LUIS) позволяет применять пользовательскую аналитику машинного обучения к тексту пользователя в разговорном стиле и на естественном языке, чтобы предсказать общий смысл и извлечь соответствующую подробную информацию.

Использование клиентской библиотеки Распознавания речи (LUIS) версии 3 для .NET.

* Создание запроса на опубликованную конечную точку

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [Пакет для разработки (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [Образцы кода (C#)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Предварительные требования

* Учетную запись на портале LUIS [можно создать бесплатно](https://www.luis.ai).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).


## <a name="setting-up"></a>Настройка

### <a name="get-your-language-understanding-luis-starter-key"></a>Получение начального ключа LUIS

Получите [ключ для начала работы](luis-how-to-azure-subscription.md#starter-key), создав ресурс разработки LUIS. Сохраните ключ и регион ключа для следующего шага.

### <a name="create-an-environment-variable"></a>Создание переменной среды

Используя ключ и регион для ключа, создайте две переменные среды для проверки подлинности:

* `COGNITIVESERVICE_AUTHORING_KEY` — ключ ресурса для проверки подлинности запросов.
* `COGNITIVESERVICE_REGION` — регион, связанный с ключом. Пример: `westus`.

Используйте инструкции для своей операционной системы.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Добавив переменную среды, перезапустите окно консоли.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

После добавления переменной среды запустите `source ~/.bashrc` из окна консоли, чтобы применить изменения.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Измените `.bash_profile` и добавьте переменную среды:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

После добавления переменной среды запустите `source .bash_profile` из окна консоли, чтобы применить изменения.
***

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе.

1. В окне консоли (cmd, PowerShell или Bash) выполните команду dotnet `new`, чтобы создать консольное приложение с именем `language-understanding-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Измените каталог на созданную папку приложения.

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


### <a name="install-the-sdk"></a>Установка пакета SDK

В каталоге приложения установите клиентскую библиотеку для разработки LUIS для .NET с помощью следующей команды:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.


## <a name="object-model"></a>Объектная модель

Клиент для разработки LUIS является объектом [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet), который проходит проверку подлинности в Azure и содержит ваш ключ разработки.

После создания используйте клиент для доступа к функциям, включая:

* Приложения — [создание](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [удаление](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [публикация](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet).
* Примеры речевых фрагментов — [добавление по пакетам](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [удаление по идентификатору](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet).
* Компоненты — управление [списками фраз](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet).
* Модель — управление [намерениями](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) и сущностями.
* Шаблоны — управление [шаблонами](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet).
* Обучение — [обучение](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) приложения и опрос [состояния обучения](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet).
* [Версии](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) — управление с помощью клонирования, экспорта и удаления.


## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки для разработки LUIS для .NET:

* [Создание приложения](#create-a-luis-app)
* [Добавление сущностей](#create-entities-for-the-app)
* [Добавление намерений](#create-intent-for-the-app)
* [Добавление примеров фрагментов речи](#add-example-utterance-to-intent)
* [Обучение приложения](#train-the-app)
* [Публикация приложения](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Добавление зависимостей

В каталоге проекта откройте файл *Program.cs* в предпочитаемом редакторе или интегрированной среде разработки. Замените существующий код `using` следующими директивами `using`.

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

1. Создайте переменные для управления вашим ключом разработки, извлеченным из переменной среды с именем `COGNITIVESERVICES_AUTHORING_KEY`. Если вы создали переменную среды после запуска приложения, то для доступа к переменной следует закрыть и перезагрузить редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена. Метод будет создано позже.

1. Создайте переменные для хранения вашего региона разработки и конечной точки. Регион ключа разработки зависит от того, где вы создаете код. Существует [три региона разработки](luis-reference-regions.md):

    * Австралия — `australiaeast`.
    * Европа — `westeurope`.
    * США и другие регионы — `westus` (по умолчанию).

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Теперь создайте объект [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) с помощью ключа и используйте его со своей конечной точкой, чтобы создать объект [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet).

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Создание приложения LUIS

1. Создайте приложение LUIS, которое будет содержать модель обработки естественного языка (NLP), содержащую намерения, сущности и примеры речевых фрагментов.

1. Создайте [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Название, язык и региональные параметры являются обязательными свойствами.

1. Вызовите метод [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet). Ответ — это идентификатор приложения.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Создание намерения для приложения
Основным объектом в модели приложения LUIS является намерение. Намерение совпадает с группировкой _намерений_ пользовательских речевых фрагментов. Пользователь может задать вопрос или сделать инструкцию в поисках конкретного _намеренного_ ответа от бота (или другого клиентского приложения). Примерами намерений являются бронирование рейса, запрос о погоде в городе назначения и запрос контактной информации для обслуживания клиентов.

Создайте [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) с именем уникального намерения, затем передайте идентификатор приложения, идентификатор версии и ModelCreateObject в метод [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet). Ответ — это идентификатор намерения.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Создание сущностей для приложения

Хотя сущности не требуются, они встречаются в большинстве приложений. Сущность извлекает информацию из речевого фрагмента пользователя, необходимую для выполнения намерения пользователя. Существует несколько типов [готовых](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) и пользовательских объектов, каждый из которых имеет собственные модели объекта преобразования данных (DTO).  Обычные готовые сущности, добавляемые в ваше приложение, включают в себя [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md) и [ordinal](luis-reference-prebuilt-ordinal.md).

Этот метод **AddEntities** создал простую сущность `Location` с двумя ролями, простую сущность `Class`, сложную сущность `Flight` и добавил несколько готовых сущностей.

Важно знать, что объекты не отмечены намерением. Они могут и обычно относятся ко многим намерениям. Только примеры пользовательских речевых фрагментов помечены для определенного единственного намерения.

Методы создания сущностей являются частью класса [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet). Каждый тип сущности имеет собственную модель объекта преобразования данных (DTO), обычно содержащую слово `model` в пространстве имен [моделей](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet).

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Добавление примера высказывания в намерение

Чтобы определить намерение высказывания и извлечь сущности, приложению нужны примеры речевых фрагментов. Примеры должны быть нацелены на конкретное единственное намерение и должны отмечать все пользовательские объекты. Готовые объекты не нужно отмечать.

Добавьте примеры речевых фрагментов, создав список объектов [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet), по одному объекту для каждого примера высказывания. Каждый пример должен пометить все сущности словарем пар "имя — значение" имени и значения сущности. Значение сущности должно быть точно таким, как оно указано в тексте примера высказывания.

Вызовите [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) с идентификатором приложения, идентификатором версии и списком примеров. Вызов реагирует на список результатов. Вам нужно проверить результат каждого примера, чтобы убедиться, что он успешно добавлен в модель.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

Методы **CreateUtterance** и **CreateLabel** — это служебные методы, помогающие создавать объекты.

## <a name="train-the-app"></a>Обучение приложения

После создания модели приложение LUIS необходимо обучить этой версии модели. Обученную модель можно использовать в [контейнере](luis-container-howto.md) или [опубликовать](luis-how-to-publish-app.md) в промежуточных или рабочих слотах.

Методу [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) требуется идентификатор приложения и идентификатор версии.

Очень маленькая модель, такая как в этом кратком руководстве, будет обучаться очень быстро. Обучение приложений рабочего уровня должно включать в себя вызов опроса с помощью метода [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_), чтобы определить, прошло ли обучение успешно. Ответ представляет собой список объектов [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) с отдельным состоянием для каждого объекта. Все объекты должны быть успешно обучены, чтобы обучение считалось завершенным.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Публикация приложения LUIS

Опубликуйте LUIS, используя метод [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet). При этом публикуется текущая обученная версия в указанный слот в конечной точке. Ваше клиентское приложение использует эту конечную точку для отправки пользовательских высказываний для прогнозирования намерения и извлечения сущности.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с помощью команды `dotnet run` из каталога приложения.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите выполнить очистку, вы можете удалить приложение LUIS. Удаление приложения выполняется с помощью метода [Apps.DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet). Вы также можете удалить приложение с [портала LUIS](https://www.luis.ai).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Краткое руководство. Запрос конечной точки прогнозирования с помощью пакета SDK .NET для C#](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [Что такое служба "Распознавание речи" (LUIS)?](what-is-luis.md)
* [Новые возможности](whats-new.md)
* [Намерения](luis-concept-intent.md), [сущности](luis-concept-entity-types.md), [примеры речевых фрагментов](luis-concept-utterance.md) и [готовые сущности](luis-reference-prebuilt-entities.md).
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs).
