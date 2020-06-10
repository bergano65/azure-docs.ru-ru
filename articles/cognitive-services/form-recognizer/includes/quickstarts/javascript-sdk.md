---
title: Краткое руководство. Клиентская библиотека Распознавателя документов для JavaScript
description: В этом кратком руководстве описано, как приступить к работе с клиентской библиотекой службы "Распознаватель документов" для JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.openlocfilehash: c24f82d48a1452cdb272abca178a9ba924aacc20
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997503"
---
[Справочная документация](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Пакет (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Примеры](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Набор данных для обучения в хранилище BLOB-объектов Azure. Советы и варианты для объединения данных для обучения см. в статье о [создании обучающего набора данных для пользовательской модели](../../build-training-data-set.md). При работе с этим кратким руководством можно использовать файлы в папке **Train** из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2090451).
* Текущая версия [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Настройка

### <a name="create-a-form-recognizer-azure-resource"></a>Создание ресурса Распознавателя документов Azure

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Создание переменных среды

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init
```

Создайте файл с именем `index.js`, откройте его и импортируйте следующие библиотеки:

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

Также загрузите файл с переменными среды.

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите пакеты NPM: `ai-form-recognizer` и `dotenv`.

```console
npm install @azure/ai-form-recognizer dotenv
```

Файл `package.json` этого приложения будет дополнен зависимостями.


<!-- 
    Object model
-->

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Распознавателя документов для JavaScript:

* [аутентификация клиента](#authenticate-the-client);
* [распознавание содержимого формы](#recognize-form-content);
* [распознавание квитанций](#recognize-receipts);
* [обучение пользовательской модели](#train-a-custom-model);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [управление пользовательскими моделями](#manage-your-custom-models).

## <a name="authenticate-the-client"></a>Аутентификация клиента

В функции `main` создайте переменные для конечной точки и ключа ресурса Azure. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку.

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

Затем выполните проверку подлинности клиентского объекта с использованием переменных подписки, которые вы определили. Здесь применяется объект **AzureKeyCredential**, чтобы при необходимости ключ API можно было обновить, не создавая новых клиентских объектов. Также будет создан объект клиента бучения.

```javascript
const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));

const trainingClient = client.getFormTrainingClient();
```

### <a name="call-client-specific-functions"></a>Вызов клиентских функций

В следующем блоке кода в `main` с помощью клиентских объектов вызываются функции для каждой из ключевых задач в пакете SDK Распознавателя документов. Эти функции будут определены позже.

Также потребуется добавить URL-адреса данных для обучения и тестирования.
* Чтобы получить подписанный URL-адрес данных для обучения пользовательской модели, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Получить подписанный URL-адрес**. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Получить URL-адрес формы для тестирования (отдельного документа в хранилище больших двоичных объектов) можно с помощью описанных выше действий. Также можно взять URL-адрес документа, расположенного в другом месте.
* С помощью описанного выше метода также получите URL-адрес изображения квитанции (либо воспользуйтесь URL-адресом примера такого изображения).

> [!NOTE]
> Во фрагментах кода в этом руководстве используются удаленные формы, доступ к которым осуществляется по URL-адресам. Если вместо этого вы планируете работать с локальными документами, воспользуйтесь соответствующими методами, которые описаны в [справочной документации](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview).


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>Распознавание содержимого формы

С помощью Распознавателя документов можно распознавать таблицы, строки и слова в документах без предварительного обучения модели. Для распознавания содержимого файла c определенным универсальным кодом ресурса URI используйте метод **beginRecognizeContentFromUrl**.

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

Возвращаемое значение представляет собой коллекцию объектов **FormPage**: по одному для каждой страницы обработанного документа. Приведенный ниже код обрабатывает эти объекты в цикле и выводит извлеченные из документа пары ключей и значений, а также табличные данные.

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Распознавание квитанций

В этом разделе объясняется, как с помощью предварительно обученной модели распознавать используемые в США квитанции и извлекать из них содержимое стандартных полей.

Для распознавания квитанции с определенным универсальным кодом ресурса (URI) используйте метод **beginRecognizeReceiptsFromUrl**. Возвращаемое значение представляет собой коллекцию объектов **RecognizedReceipt**: по одному для каждой страницы обработанного документа. В следующем блоке кода обрабатывается квитанция с указанным универсальным кодом ресурса (URI) и в консоль выводятся все ее основные поля с соответствующими значениями.

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const usReceipt = response.receipts[0];
    console.log("First receipt:");
    console.log(`Receipt type: ${usReceipt.receiptType}`);
    console.log(
        `Merchant Name: ${usReceipt.merchantName.value} (confidence: ${usReceipt.merchantName.confidence})`
    );
    console.log(
        `Transaction Date: ${usReceipt.transactionDate.value} (confidence: ${usReceipt.transactionDate.confidence})`
    );
    console.log("Receipt items:");
    console.log(`  name\tprice\tquantity\ttotalPrice`);
```

Следующий блок кода обрабатывает в цикле отдельные объекты, обнаруженные в квитанции, и выводит их данные в консоль.

```csharp
    for (const item of usReceipt.items) {
        const name = `${optionalToString(item.name.value)} (confidence: ${optionalToString(
            item.name.confidence
        )})`;
        const price = `${optionalToString(item.price.value)} (confidence: ${optionalToString(
            item.price.confidence
        )})`;
        const quantity = `${optionalToString(item.quantity.value)} (confidence: ${optionalToString(
            item.quantity.confidence
        )})`;
        const totalPrice = `${optionalToString(item.totalPrice.value)} (confidence: ${optionalToString(
            item.totalPrice.confidence
        )})`;
        console.log(`  ${name}\t${price}\t${quantity}\t${totalPrice}`);
    }
```

В этой функции применяется вспомогательная функция `optionalToString`. Определите эту функцию в корневом разделе своего скрипта:

```javascript
function optionalToString(value) {
  return `${value || "<missing>"}`;
}
```


## <a name="train-a-custom-model"></a>Обучение пользовательской модели

В этом разделе демонстрируется, как обучить модель на основе собственных данных. Обученная модель выводит структурированные данные, которые содержат связи типа "ключ/значение" из исходного документа формы. После обучения модели вы можете проверить ее, повторно обучить и, в конечном итоге, применить с целью надежного извлечения данных из нескольких форм в соответствии со своими потребностями.

> [!NOTE]
> Также можно обучить модель с графическим пользовательским интерфейсом, такой как [пример средства разметки Распознавателя документов](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Обучение моделей без меток

Вы можете обучить свою модель распознавать все поля и значения в ваших формах без разметки вручную документов, которые будете использовать для обучения.

Приведенная ниже функция обучает модель на основе заданного набора документов и выводит ее статус в консоль. 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.createdOn}`);
    console.log(`Last modified: ${response.lastModified}`);
```

Возвращаемый объект **CustomFormModel** содержит сведения о типах форм, с которыми работает модель, и полях, которые она способна извлечь из формы каждого типа. Приведенный ниже блок кода выводит эту информацию в консоль.

```javascript
    if (response.models) {
        for (const submodel of response.models) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

Наконец, этот метод возвращает уникальный идентификатор модели.

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>Обучение моделей с метками

Модели также можно обучать на документах, которые размечены вручную. В некоторых сценариях обучение по такой схеме приводит к лучшим результатам. Для обучения с метками в контейнере больших двоичных объектов вместе с документами, используемыми для обучения, должны находиться особые файлы с информацией о метках ( *\<filename\>.pdf.labels.json*). Эти файлы можно создать в пользовательском интерфейсе [средства разметки Распознавателя документов](../../quickstarts/label-tool.md). Создав файлы, вызовите метод **beginTraining** и задайте для параметра *uselabels* значение `true`.

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>анализ документов с помощью пользовательской модели;

В этом разделе показано, как извлекать данные вида "ключ/значение" и другое содержимое из пользовательских форм с помощью обученных на них моделей.

> [!IMPORTANT]
> Для реализации этого сценария у вас уже должна быть обученная модель, идентификатор которой нужно будет передать указанному ниже методу. См. раздел [Обучение модели](#train-a-model-without-labels).

Используйте метод **beginRecognizeFormsFromUrl**. Возвращаемое значение представляет собой коллекцию объектов **RecognizedForm**: по одному для каждой страницы обработанного документа.

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

Следующий код выводит в консоль результаты анализа. Он отображает каждое из распознанных полей с соответствующим значением и коэффициентом достоверности.

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>Управление пользовательскими моделями

В этом разделе показано, как управлять пользовательскими моделями, которые хранятся в вашей учетной записи. Приведенный ниже код выполняет все задачи по управлению моделями в одной функции (в качестве примера). Сначала скопируйте сигнатуру функции ниже:

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Проверка числа моделей в учетной записи ресурсов FormRecognizer

Приведенный ниже блок кода проверяет, сколько моделей сохранено в вашей учетной записи Распознавателя документов, и сравнивает это значение с ограничением для данной учетной записи.

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.count} custom models, and we can have at most ${accountProperties.limit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Вывод списка моделей, которые хранятся в учетной записи ресурсов

Приведенный ниже блок кода выводит в консоль список текущих моделей в вашей учетной записи с подробными сведениями о них. Он также сохраняет ссылку на первую модель.

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Получение определенной модели по ее идентификатору

Следующий блок кода получает подробные сведения о модели по ее идентификатору, сохраненному в предыдущем разделе.

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>Удаление модели из учетной записи ресурсов

Модель можно удалить из учетной записи ресурсов по ее идентификатору. Этот фрагмент кода удаляет модель, которая использовалась в предыдущем разделе.

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```


## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `node` для файла quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="enable-logs"></a>Включение журналов
При использовании этой библиотеки с помощью приведенной ниже переменной среды можно настроить ведение журналов отладки.

```
export DEBUG=azure*
```

Дополнительные сведения о том, как включить журналы, см. в [документации к пакету @azure/logger](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).


## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описано использование клиентской библиотеки Распознавателя документов на языке Python для обучения модели и анализа документов различными способами. Теперь следует изучить советы по созданию лучшего набора данных для обучения и созданию более точных моделей.

> [!div class="nextstepaction"]
> [Создание набора данных для обучения](../../build-training-data-set.md)

* [Что такое Распознаватель документов?](../../overview.md)
* Пример кода из этого руководства, а также другие ресурсы можно найти на [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).