---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 4bf86c616420bb049e1d7a82ad0e942e6eb7b36f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369292"
---
С помощью клиентской библиотеки прогнозирования для .NET в службе "Распознавание речи" (LUIS) можно выполнить следующие задачи.

* Получение прогноза по слоту
* Прогнозирование по версии

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Пакет для прогнозирования (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [Образцы кода (C#)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Предварительные требования

* Учетную запись на портале LUIS [можно создать бесплатно](https://www.luis.ai).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Идентификатор приложения LUIS. Используйте общедоступный идентификатор приложения для Интернета вещей `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. Пользовательский запрос, используемый в коде из этого краткого руководства, относится только к этому приложению.

## <a name="setting-up"></a>Настройка

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

В каталоге приложения установите клиентскую библиотеку для среды выполнения прогнозирования LUIS для .NET с помощью следующей команды:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.

## <a name="object-model"></a>Объектная модель

Клиент для среды выполнения прогнозирования LUIS представляет собой объект [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet), который проходит проверку подлинности в Azure и содержит ваш ключ ресурса.

После создания используйте клиент для доступа к функциям, включая:

* Прогнозирование по [слоту промежуточного хранения или рабочему слоту](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Прогнозирование по [версии](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки для среды выполнения прогнозирования LUIS для .NET:

* [Прогнозирование по слоту](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Добавление зависимостей

В каталоге проекта откройте файл *Program.cs* в предпочитаемом редакторе или интегрированной среде разработки. Замените существующий код `using` следующими директивами `using`.

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

1. Создайте переменные для ключа, имени ресурса, идентификатора приложения и слота публикации. Укажите для идентификатора приложения общедоступное приложение Интернета вещей.

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Создайте объект [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) с помощью ключа и используйте его со своей конечной точкой, чтобы создать объект [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet).

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Получение прогноза из среды выполнения

Добавьте следующий метод, который создает запрос к среде выполнения прогнозирования.

Речевой фрагмент пользователя является частью объекта [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet).

Метод **GetSlotPredictionAsync** принимает для выполнения запроса несколько обязательных параметров, в том числе идентификатор приложения, имя слота и объект запроса прогнозирования. Есть еще несколько необязательных параметров, например для режимов подробного протоколирования, отображения всех намерений и ведения журнала.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Основной код для прогнозирования

Используйте следующий метод Main, чтобы скомбинировать переменные и методы для получения прогноза.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с помощью команды `dotnet run` из каталога приложения.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с прогнозированием очистите результаты, полученные при выполнении этого краткого руководства, удалив файл program.cs и все подкаталоги.
