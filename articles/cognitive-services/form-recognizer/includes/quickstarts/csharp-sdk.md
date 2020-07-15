---
title: Краткое руководство. Клиентская библиотека Распознавателя документов для .NET
description: В этом кратком руководстве описано, как приступить к работе с клиентской библиотекой службы "Распознаватель документов" для .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 2b46e115b6360b161a1b2ad9b176f3afbfaf27d0
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277741"
---
[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Пакет (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Примеры](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Набор данных для обучения в хранилище BLOB-объектов Azure. Советы и варианты для объединения данных для обучения см. в статье о [создании обучающего набора данных для пользовательской модели](../../build-training-data-set.md). При работе с этим кратким руководством можно использовать файлы в папке **Train** из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2090451).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Настройка

### <a name="create-a-form-recognizer-azure-resource"></a>Создание ресурса Распознавателя документов Azure

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Создание переменных среды

[!INCLUDE [environment-variables](../environment-variables.md)]

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

В каталоге проекта откройте файл *Program.cs* в предпочитаемом редакторе или интегрированной среде разработки. Затем добавьте следующие `using` директивы:

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.IO;
using System.Threading.Tasks;
```

Затем добавьте следующий код в метод **Main** приложения. Вы зададите эту асинхронную задачу позже. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В каталоге приложения установите клиентскую библиотеку Распознавателя документов для .NET с помощью следующей команды:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Распознавателя документов для .NET:

* [аутентификация клиента](#authenticate-the-client);
* [распознавание содержимого формы](#recognize-form-content);
* [распознавание квитанций](#recognize-receipts);
* [обучение пользовательской модели](#train-a-custom-model);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [управление пользовательскими моделями](#manage-your-custom-models).


## <a name="authenticate-the-client"></a>Аутентификация клиента

Ниже метода `Main` определите задачу, на которую ссылается `Main`. В этом фрагменте выполняется проверка подлинности двух клиентских объектов с использованием переменных подписки, указанных выше. Здесь применяется объект **AzureKeyCredential**, чтобы при необходимости ключ API можно было обновить, не создавая новых клиентских объектов.

```csharp
static async Task RunFormRecognizerClient()
{ 
    string endpoint = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_ENDPOINT");
    string apiKey = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_KEY");
    var credential = new AzureKeyCredential(apiKey);
    
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    var recognizerClient = new FormRecognizerClient(new Uri(endpoint), credential);
```

### <a name="call-client-specific-methods"></a>Вызов клиентских методов

В следующем блоке кода с помощью клиентских объектов вызываются методы для каждой из ключевых задач в пакете SDK Распознавателя документов. Эти методы будут определены позже.

Также потребуется добавить URL-адреса данных для обучения и тестирования. 
* Чтобы получить подписанный URL-адрес данных для обучения пользовательской модели, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Получить подписанный URL-адрес**. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Получить URL-адрес формы для тестирования (отдельного документа в хранилище больших двоичных объектов) можно с помощью описанных выше действий. Также можно взять URL-адрес документа, расположенного в другом месте.
* С помощью описанного выше метода также получите URL-адрес изображения квитанции (либо воспользуйтесь URL-адресом примера такого изображения).

> [!NOTE]
> Во фрагментах кода в этом руководстве используются удаленные формы, доступ к которым осуществляется по URL-адресам. Если вместо этого вы планируете работать с локальными документами, воспользуйтесь соответствующими методами, которые описаны в [справочной документации](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre).

```csharp
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    Console.WriteLine("Get form content...");
    await GetContent(recognizerClient, formUrl);

    Console.WriteLine("Analyze receipt...");
    await AnalyzeReceipt(recognizerClient, receiptUrl);

    Console.WriteLine("Train Model with training data...");
    Guid modelId = await TrainModel(trainingClient, trainingDataUrl);

    Console.WriteLine("Analyze PDF form...");
    await AnalyzePdfForm(recognizerClient, modelId, formUrl);

    Console.WriteLine("Manage models...");
    await ManageModels(trainingClient, trainingDataUrl) ;
}
```

## <a name="recognize-form-content"></a>Распознавание содержимого формы

С помощью Распознавателя документов можно распознавать таблицы, строки и слова в документах без предварительного обучения модели.

Для распознавания содержимого файла c определенным универсальным кодом ресурса URI используйте метод **StartRecognizeContentFromUri**.

```csharp
private static async Task<Guid> GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<FormPageCollection> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

Возвращаемое значение представляет собой коллекцию объектов **FormPage**: по одному для каждой страницы обработанного документа. Приведенный ниже код обрабатывает эти объекты в цикле и выводит извлеченные из документа пары ключей и значений, а также табличные данные.

```csharp
    foreach (FormPage page in formPages.Value)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count}" + 
            $" lines.");
    
        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count}" + 
                $" word{(line.Words.Count > 1 ? "s" : "")}," +
                $" and text: '{line.Text}'.");
        }
    
        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and" +
                $" {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})" +
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Распознавание квитанций

В этом разделе объясняется, как с помощью предварительно обученной модели распознавать используемые в США квитанции и извлекать из них содержимое стандартных полей.

Для распознавания квитанции с определенным универсальным кодом ресурса (URI) используйте метод **StartRecognizeReceiptsFromUri**. Возвращаемое значение представляет собой коллекцию объектов **RecognizedReceipt**: по одному для каждой страницы обработанного документа. В следующем блоке кода обрабатывается квитанция с указанным универсальным кодом ресурса (URI) и в консоль выводятся все ее основные поля с соответствующими значениями.

```csharp
private static async Task<Guid> AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    RecognizedReceiptCollection receipts = await recognizerClient.StartRecognizeReceiptsFromUri(new Uri(receiptUri))
    .WaitForCompletionAsync();

    foreach (RecognizedReceipt receipt in receipts)
    {
    FormField merchantNameField;
    if (receipt.RecognizedForm.Fields.TryGetValue("MerchantName", out merchantNameField))
    {
        if (merchantNameField.Value.Type == FieldValueType.String)
        {
            string merchantName = merchantNameField.Value.AsString();

            Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
        }
    }

    FormField transactionDateField;
    if (receipt.RecognizedForm.Fields.TryGetValue("TransactionDate", out transactionDateField))
    {
        if (transactionDateField.Value.Type == FieldValueType.Date)
        {
            DateTime transactionDate = transactionDateField.Value.AsDate();

            Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
        }
    }
```

Следующий блок кода обрабатывает в цикле отдельные объекты, обнаруженные в квитанции, и выводит их данные в консоль.

```csharp
    FormField itemsField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Items", out itemsField))
    {
        if (itemsField.Value.Type == FieldValueType.List)
        {
            foreach (FormField itemField in itemsField.Value.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.Value.Type == FieldValueType.Dictionary)
                {
                    IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                    FormField itemNameField;
                    if (itemFields.TryGetValue("Name", out itemNameField))
                    {
                        if (itemNameField.Value.Type == FieldValueType.String)
                        {
                            string itemName = itemNameField.Value.AsString();

                            Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                        }
                    }

                    FormField itemTotalPriceField;
                    if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                    {
                        if (itemTotalPriceField.Value.Type == FieldValueType.Float)
                        {
                            float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                            Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                        }
                    }
                }
            }
        }
    }
```

Наконец, последний блок кода выводит в консоль итоговую сумму из квитанции.

```csharp
    FormField totalField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Total", out totalField))
    {
        if (totalField.Value.Type == FieldValueType.Float)
        {
            float total = totalField.Value.AsFloat();

            Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
        }
    }
}
```

## <a name="train-a-custom-model"></a>Обучение пользовательской модели

В этом разделе демонстрируется, как обучить модель на основе собственных данных. Обученная модель выводит структурированные данные, которые содержат связи типа "ключ/значение" из исходного документа формы. После обучения модели вы можете проверить ее, повторно обучить и, в конечном итоге, применить с целью надежного извлечения данных из нескольких форм в соответствии со своими потребностями.

> [!NOTE]
> Также можно обучить модель с графическим пользовательским интерфейсом, такой как [пример средства разметки Распознавателя документов](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Обучение моделей без меток

Вы можете обучить свою модель распознавать все поля и значения в ваших формах без разметки вручную документов, которые будете использовать для обучения.

Приведенный ниже метод обучает модель на основе заданного набора документов и выводит ее статус в консоль. 

```csharp
private static async Task<Guid> TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: false).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

Возвращаемый объект **CustomFormModel** содержит сведения о типах форм, с которыми работает модель, и полях, которые она способна извлечь из формы каждого типа. Приведенный ниже блок кода выводит эту информацию в консоль.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

Наконец, этот метод возвращает уникальный идентификатор модели.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>Обучение моделей с метками

Модели также можно обучать на документах, которые размечены вручную. В некоторых сценариях обучение по такой схеме приводит к лучшим результатам. Для обучения с метками в контейнере больших двоичных объектов вместе с документами, используемыми для обучения, должны находиться особые файлы с информацией о метках ( *\<filename\>.pdf.labels.json*). Эти файлы можно создать в пользовательском интерфейсе [средства разметки Распознавателя документов](../../quickstarts/label-tool.md). Создав файлы, вызовите метод **StartTrainingAsync** и задайте для параметра *uselabels* значение `true`.

```csharp
private static async Task<Guid> TrainModelWithLabelsAsync(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
    .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: true).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

Возвращаемый объект **CustomFormModel** содержит поля, которые модель способна извлечь из документа, с указанием ориентировочной точности для каждого из них. Приведенный ниже блок кода выводит эту информацию в консоль.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Accuracy != null)
            {
                Console.Write($", Accuracy: {field.Accuracy}");
            }
            Console.WriteLine("");
        }
    }
    return model.ModelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>анализ документов с помощью пользовательской модели;

В этом разделе показано, как извлекать данные вида "ключ/значение" и другое содержимое из пользовательских форм с помощью обученных на них моделей.

> [!IMPORTANT]
> Для реализации этого сценария у вас уже должна быть обученная модель, идентификатор которой нужно будет передать указанному ниже методу. См. раздел [Обучение модели](#train-a-model-without-labels).

Используйте метод **StartRecognizeCustomFormsFromUri**. Возвращаемое значение представляет собой коллекцию объектов **RecognizedForm**: по одному для каждой страницы обработанного документа.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient formClient, Guid modelId, string pdfFormFile)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId, new Uri(formUri))
        .WaitForCompletionAsync();
```

Следующий код выводит в консоль результаты анализа. Он отображает каждое из распознанных полей с соответствующим значением и коэффициентом достоверности.

```csharp
    foreach (RecognizedForm form in forms.Value)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");
    
            if (field.LabelText != null)
            {
                Console.WriteLine($"    Label: '{field.LabelText.Text}");
            }
    
            Console.WriteLine($"    Value: '{field.ValueText.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

## <a name="manage-your-custom-models"></a>Управление пользовательскими моделями

В этом разделе показано, как управлять пользовательскими моделями, которые хранятся в вашей учетной записи. Приведенный ниже код выполняет все задачи по управлению моделями в одном методе (в качестве примера). Сначала скопируйте сигнатуру метода ниже:

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Проверка числа моделей в учетной записи ресурсов FormRecognizer

Приведенный ниже блок кода проверяет, сколько моделей сохранено в вашей учетной записи Распознавателя документов, и сравнивает это значение с ограничением для данной учетной записи.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Вывод списка моделей, которые хранятся в учетной записи ресурсов

Приведенный ниже блок кода выводит в консоль список текущих моделей в вашей учетной записи с подробными сведениями о них.

```csharp
    // List the first ten or fewer models currently stored in the account.
    Pageable<CustomFormModelInfo> models = trainingClient.GetModelInfos();
    
    foreach (CustomFormModelInfo modelInfo in models.Take(10))
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Created On: {modelInfo.CreatedOn}");
        Console.WriteLine($"    Last Modified: {modelInfo.LastModified}");
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Получение определенной модели по ее идентификатору

Приведенный ниже блок кода обучает новую модель (как описано в разделе [Обучение модели](#train-a-model-without-labels)), а затем получает вторую ссылку на нее по ее идентификатору.

```csharp
    // Create a new model to store in the account
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingFileUrl)).WaitForCompletionAsync();
    
    // Get the model that was just created
    CustomFormModel modelCopy = trainingClient.GetCustomModel(model.ModelId);
    
    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following" +
        " form types:");
    
    foreach (CustomFormSubModel subModel in modelCopy.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

### <a name="delete-a-model-from-the-resource-account"></a>Удаление модели из учетной записи ресурсов

Модель можно удалить из учетной записи ресурсов по ее идентификатору.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение из каталога приложения с помощью команды `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Устранение неполадок

Ошибки, возвращаемые службой при взаимодействии с клиентской библиотекой Распознавателя документов Cognitive Services с помощью .NET SDK, приводят к исключению `RequestFailedException`. В сообщении об ошибке указывается тот же код состояния HTTP, что был бы возвращен запросом API REST.

Например, если отправить изображение квитанции с недействительным URI, служба вернет ошибку `400` "Недопустимый запрос".

```csharp Snippet:FormRecognizerBadRequest
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();

}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

В журнале вы также найдете дополнительную информацию, такую как идентификатор запроса клиента данной операции.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описано использование клиентской библиотеки .NET Распознавателя документов для обучения модели и анализа документов различными способами. Теперь следует изучить советы по созданию лучшего набора данных для обучения и созданию более точных моделей.

> [!div class="nextstepaction"]
> [Создание набора данных для обучения](../../build-training-data-set.md)

* [Что такое Распознаватель документов?](../../overview.md)
* Пример кода из этого руководства, а также другие ресурсы можно найти на [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
