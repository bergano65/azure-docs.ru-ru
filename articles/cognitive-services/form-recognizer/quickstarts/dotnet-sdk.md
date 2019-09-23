---
title: Краткое руководство. Клиентская библиотека Распознавателя документов для .NET | Документация Майкрософт
description: Здесь приведены сведения о том, как начать работу с клиентской библиотекой Распознавателя документов для .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: ce1cdadcdc69fb5539394aa9bf402aa9463311e9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057661"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Краткое руководство. Клиентская библиотека Распознавателя документов для .NET

Здесь приведены сведения о том, как начать работу с клиентской библиотекой Распознавателя документов для .NET. Распознаватель документов — это служба когнитивных вычислений, использующая технологию машинного обучения для идентификации и извлечения пар "ключ — значение" и данных таблиц из документов-форм. Затем эта служба выводит структурированные данные, которые включают в себя связи в исходном файле. Выполните приведенные здесь действия, чтобы установить пакет SDK и протестировать пример кода для выполнения базовых задач.

Клиентскую библиотеку Распознавателя документов для .NET можно использовать для следующих задач:

* [обучение пользовательской модели Распознавателя документов](#train-a-custom-model);
* [получение списка извлеченных ключей](#get-a-list-of-extracted-keys);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [получение списка пользовательских моделей](#get-a-list-of-custom-models).
* [удаление пользовательской модели](#delete-a-custom-model).

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Набор данных для обучения в хранилище BLOB-объектов Azure. Советы и варианты для объединения данных для обучения см. в статье [Build a training data set for a custom model](../build-training-data-set.md) (Создание обучающего набора данных для пользовательской модели). 

## <a name="setting-up"></a>Настройка

### <a name="create-a-form-recognizer-azure-resource"></a>Создание ресурса Распознавателя документов Azure

[!INCLUDE [create resource](../includes/create-resource.md)]

После получения ключа из своего ресурса или пробной подписки [задайте переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа с именем `FORM_RECOGNIZER_KEY`.

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `formrecognizer-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
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

В каталоге проекта откройте файл _Program.cs_ в предпочитаемом редакторе или интегрированной среде разработки. Добавьте следующие операторы `using` :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Затем добавьте следующий код в метод **Main** приложения. Вы зададите эту асинхронную задачу позже.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В каталоге приложения установите клиентскую библиотеку Распознавателя документов для .NET с помощью следующей команды:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.

## <a name="object-model"></a>Объектная модель

Приведенные ниже классы обработают основные функции пакета SDK Распознавателя документов.

|ИМЯ|ОПИСАНИЕ|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Этот класс необходим для всех функций Распознавателя документов. Вы создаете его экземпляр с информацией о подписке и используете его для создания экземпляров других классов.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Этот класс используется для обучения пользовательской модели Распознавателя документов по собственным входным данным для обучения. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Этот класс предоставляет результаты операции обучения пользовательской модели, включая идентификатор модели, который затем можно использовать для анализа документов. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Этот класс предоставляет результаты операции анализа пользовательской модели. Он возвращает список экземпляров **ExtractedPage**. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Этот класс представляет все данные, извлеченные из одного документа.|

## <a name="code-examples"></a>Примеры кода

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Распознавателя документов для .NET:

* [аутентификация клиента](#authenticate-the-client);
* [обучение пользовательской модели Распознавателя документов](#train-a-custom-model);
* [получение списка извлеченных ключей](#get-a-list-of-extracted-keys);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [получение списка пользовательских моделей](#get-a-list-of-custom-models).
* [удаление пользовательской модели](#delete-a-custom-model).

## <a name="define-variables"></a>Определение переменных

Перед определением методов добавьте следующие определения переменных в начало класса **Program**. Вам придется самостоятельно ввести некоторые переменные. 

* Значение конечной точки службы можно найти в разделе **Обзор** на портале Azure. 
* Чтобы получить подписанный URL-адрес данных для обучения, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Получить подписанный URL-адрес**. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Ниже метода `Main` определите задачу, на которую ссылается `Main`. В этом фрагменте выполняется проверка подлинности клиентского объекта с использованием переменных подписки, указанных выше. Другие методы будут определены позже.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Обучение пользовательской модели

Следующий метод использует клиентский объект Распознавателя форм для обучения новой модели распознавания по документам, хранящимся в контейнере больших двоичных объектов Azure. Он использует вспомогательный метод для отображения информации о новой обученной модели (представленной объектом [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview)) и возвращает идентификатор модели.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Следующий вспомогательный метод отображает сведения о модели Распознавателя документов.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Получение списка извлеченных ключей

После завершения обучения пользовательская модель сохранит список ключей, извлеченных из документов обучения. В будущих документах формы будут содержаться эти ключи, и поэтому в операции анализа будут извлекаться соответствующие значения. Используйте следующий метод, чтобы получить список извлеченных ключей и вывести их на консоль. Это хороший способ убедиться в эффективности процесса обучения.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>анализ документов с помощью пользовательской модели;

Этот метод использует клиент Распознавателя форм и идентификатор модели для анализа PDF-документа и извлечения данных пар "ключ — значение". Он использует вспомогательный метод для вывода результатов (представленных объектом [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)).

> [!NOTE]
> Следующий метод анализирует форму формата PDF. Аналогичные методы, которые анализируют формы форматов JPEG и PNG, см. в полном примере кода на [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Следующий вспомогательный метод отображает сведения об операциях Analyze.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Получение списка пользовательских моделей

Можно вернуть список всех обученных моделей, принадлежащих вашей учетной записи, а также получить сведения о времени их создания. Список моделей представлен в объекте [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Удаление пользовательской модели

Если вы хотите удалить пользовательскую модель из учетной записи, используйте метод, приведенный ниже.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Выполнение приложения

Перейдите к каталогу приложения и запустите приложение с помощью команды `dotnet run`.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Кроме того, если вы обучили пользовательскую модель, которую необходимо удалить из учетной записи, запустите метод в области [Удаление пользовательской модели](#delete-a-custom-model).

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве описано использование клиентской библиотеки Распознавателя документов .NET для обучения пользовательской модели и анализа документов. Теперь следует изучить советы по созданию лучшего набора данных для обучения и созданию более точных моделей.

> [!div class="nextstepaction"]
>[Создание набора данных для обучения](../build-training-data-set.md)

* [Что такое Распознаватель документов?](../overview.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
