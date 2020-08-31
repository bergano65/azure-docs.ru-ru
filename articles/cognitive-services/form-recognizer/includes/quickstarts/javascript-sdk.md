---
title: Краткое руководство. Клиентская библиотека Распознавателя документов для JavaScript
description: В этом кратком руководстве описано, как приступить к работе с клиентской библиотекой службы "Распознаватель документов" для JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/21/2020
ms.author: pafarley
ms.openlocfilehash: 1c24eba79c26c4540e9d97a3e2b6646fd0b5439c
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864753"
---
> [!IMPORTANT]
> * Пакет SDK Распознавателя документов в настоящее время предназначен для версии 2.0 службы "Распознаватель документов".
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. См. справочную документацию ниже. 

[Справочная документация](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Пакет (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Примеры](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Набор данных для обучения в хранилище BLOB-объектов Azure. Советы и варианты для объединения данных для обучения см. в статье о [создании обучающего набора данных для пользовательской модели](../../build-training-data-set.md). При работе с этим кратким руководством можно использовать файлы в папке **Train** из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2090451).
* Текущая версия [Node.js](https://nodejs.org/)
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Создание ресурса Распознавателя документов"  target="_blank">созданию ресурса Распознавателя документов <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Распознавателя документов потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

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
const { FormRecognizerClient, FormTrainingClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите пакет NPM `ai-form-recognizer`.

```console
npm install @azure/ai-form-recognizer
```

Файл `package.json` этого приложения будет дополнен зависимостями.

## <a name="object-model"></a>Объектная модель 

С помощью Распознавателя документов можно создавать клиенты двух разных типов. Первый, `FormRecognizerClient`, используется для запроса из службы распознанных полей и содержимого форм. Второй, `FormTrainingClient`, используется для создания настраиваемых моделей и управления ими. Эти модели можно использовать для улучшения распознавания. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` предоставляет операции для перечисленных ниже целей.

 * Распознавание полей форм и содержимого с помощью настраиваемых моделей, обученных для распознавания пользовательских форм. Эти значения возвращаются в коллекцию объектов `RecognizedForm`.
 * Распознавание содержимого формы, в том числе таблиц, строк и слов, без необходимости обучения модели. Содержимое форм возвращается в коллекцию объектов `FormPage`.
 * Распознавание общих полей в квитанциях с помощью предварительно обученной модели для обработки квитанций в службе "Распознаватель документов". Эти поля и метаданные возвращаются в коллекцию `RecognizedReceipt`.

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` предоставляет операции для перечисленных ниже целей.

* Обучение настраиваемых моделей для распознавания всех полей и значений в пользовательских формах. Возвращаемый объект `CustomFormModel` задает типы форм, которые будет распознавать модель, и поля, которые она будет извлекать для каждого из этих типов. Более подробное описание процесса создания набора данных для обучения см. в [документации службы по обучению модели без добавления меток](#train-a-model-without-labels).
* Обучение пользовательских моделей для распознавания конкретных полей и значений, указываемых путем добавления меток к пользовательским формам. Возвращаемый объект `CustomFormModel` задает поля, которые будут извлечены моделью, а также предполагаемую точность для каждого поля. Более подробное объяснение применения меток к набору данных для обучения см. в [документации службы по обучению модели с добавлением меток](#train-a-model-with-labels).
* Управление моделями, созданными в учетной записи.
* Копирование настраиваемой модели из одного ресурса Распознавателя документов в другой.

Обратите внимание, что модель можно также обучить с помощью графического пользовательского интерфейса, например [средства разметки Распознавателя документов](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Распознавателя документов для JavaScript:

* [аутентификация клиента](#authenticate-the-client);
* [распознавание содержимого формы](#recognize-form-content);
* [распознавание квитанций](#recognize-receipts);
* [обучение пользовательской модели](#train-a-custom-model);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [управление пользовательскими моделями](#manage-your-custom-models).

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте в приложении переменные для конечной точки и ключа ресурса Azure. 

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = "<paste-your-form-recognizer-endpoint-here>";
const apiKey = "<paste-your-form-recognizer-key-here>";
```

Затем выполните проверку подлинности клиентского объекта с использованием переменных подписки, которые вы определили. Здесь применяется объект `AzureKeyCredential`, чтобы при необходимости ключ API можно было обновить, не создавая новых клиентских объектов. Также будет создан объект клиента бучения.

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="assets-for-testing"></a>Ресурсы для тестирования

Во фрагментах кода в этом руководстве используются удаленные формы, доступ к которым осуществляется по URL-адресам. Если вместо этого вы планируете работать с локальными документами, воспользуйтесь соответствующими методами, которые описаны в [справочной документации](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) и [образцах](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Также потребуется добавить URL-адреса данных для обучения и тестирования.
* Чтобы получить подписанный URL-адрес данных для обучения пользовательской модели, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Получить подписанный URL-адрес**. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Используйте пример формы и изображения квитанций, включенные в приведенные ниже примеры. Вы можете также скачать их с [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) или выполнить описанные выше действия, чтобы получить URL-адрес SAS для отдельного документа в хранилище BLOB-объектов. 

> [!NOTE]
> Во фрагментах кода в этом руководстве используются удаленные формы, доступ к которым осуществляется по URL-адресам. Если вместо этого вы планируете работать с локальными документами, воспользуйтесь соответствующими методами, которые описаны в [справочной документации](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Распознавание содержимого формы

С помощью Распознавателя документов можно распознавать таблицы, строки и слова в документах без предварительного обучения модели. Для распознавания содержимого файла c определенным универсальным кодом ресурса URI используйте метод `beginRecognizeContentFromUrl`.

```javascript
async function recognizeContent() {
    const formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    const poller = await client.beginRecognizeContentFromUrl(formUrl);
    const pages = await poller.pollUntilDone();

    if (!pages || pages.length === 0) {
        throw new Error("Expecting non-empty list of pages!");
    }

    for (const page of pages) {
        console.log(
            `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
        );
        for (const table of page.tables) {
            for (const cell of table.cells) {
                console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
            }
        }
    }
}

recognizeContent().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Выходные данные

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="recognize-receipts"></a>Распознавание квитанций

В этом разделе объясняется, как с помощью предварительно обученной модели распознавать используемые в США квитанции и извлекать из них содержимое стандартных полей.

Для распознавания квитанции с определенным универсальным кодом ресурса (URI) используйте метод `beginRecognizeReceiptsFromUrl`. В следующем блоке кода обрабатывается квитанция с указанным универсальным кодом ресурса (URI) и в консоль выводятся все ее основные поля с соответствующими значениями.

```javascript
async function recognizeReceipt() {
    receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png";
    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeReceiptsFromUrl(receiptUrl, {
        onProgress: (state) => { console.log(`status: ${state.status}`); }
    });

    const receipts = await poller.pollUntilDone();

    if (!receipts || receipts.length <= 0) {
        throw new Error("Expecting at lease one receipt in analysis result");
    }

    const receipt = receipts[0];
    console.log("First receipt:");
    const receiptTypeField = receipt.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
    const itemsField = receipt.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
    const totalField = receipt.fields["Total"];
    if (totalField.valueType === "number") {
        console.log(`  Total: '${totalField.value || "<missing>"}', with confidence of ${totalField.confidence}`);
    }
}

recognizeReceipt().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Выходные данные

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="train-a-custom-model"></a>Обучение пользовательской модели

В этом разделе демонстрируется, как обучить модель на основе собственных данных. Обученная модель выводит структурированные данные, которые содержат связи типа "ключ/значение" из исходного документа формы. После обучения модели вы можете проверить ее, повторно обучить и, в конечном итоге, применить с целью надежного извлечения данных из нескольких форм в соответствии со своими потребностями.

> [!NOTE]
> Также можно обучить модель с графическим пользовательским интерфейсом, такой как [пример средства разметки Распознавателя документов](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Обучение моделей без меток

Вы можете обучить свою модель распознавать все поля и значения в ваших формах без разметки вручную документов, которые будете использовать для обучения.

Приведенная ниже функция обучает модель на основе заданного набора документов и выводит ее статус в консоль. 

```javascript
async function trainModel() {

    const containerSasUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await trainingClient.beginTraining(containerSasUrl, false, {
        onProgress: (state) => { console.log(`training status: ${state.status}`); }
    });
    const model = await poller.pollUntilDone();

    if (!model) {
        throw new Error("Expecting valid training result!");
    }

    console.log(`Model ID: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log(`Training started on: ${model.trainingStartedOn}`);
    console.log(`Training completed on: ${model.trainingCompletedOn}`);

    if (model.submodels) {
        for (const submodel of model.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
    // Training document information
    if (model.trainingDocuments) {
        for (const doc of model.trainingDocuments) {
            console.log(`Document name: ${doc.name}`);
            console.log(`Document status: ${doc.status}`);
            console.log(`Document page count: ${doc.pageCount}`);
            console.log(`Document errors: ${doc.errors}`);
        }
    }
}

trainModel().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Выходные данные

Это выходные данные для модели, обученной по данным для обучения, доступным в [пакете SDK для Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training). Данный пример выходных данных усечен для удобства чтения.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>Обучение моделей с метками

Модели также можно обучать на документах, которые размечены вручную. В некоторых сценариях обучение по такой схеме приводит к лучшим результатам. Для обучения с метками в контейнере хранилища BLOB-объектов вместе с документами, используемыми для обучения, должны находиться особые файлы с информацией о метках (`\<filename\>.pdf.labels.json`). Эти файлы можно создать в пользовательском интерфейсе [средства разметки Распознавателя документов](../../quickstarts/label-tool.md). Создав файлы, вызовите метод `beginTraining` и задайте для параметра `uselabels` значение `true`.

```javascript
async function trainModelLabels() {

    const containerSasUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await trainingClient.beginTraining(containerSasUrl, true, {
        onProgress: (state) => { console.log(`training status: ${state.status}`); }
    });
    const model = await poller.pollUntilDone();

    if (!model) {
        throw new Error("Expecting valid training result!");
    }

    console.log(`Model ID: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log(`Training started on: ${model.trainingStartedOn}`);
    console.log(`Training completed on: ${model.trainingCompletedOn}`);

    if (model.submodels) {
        for (const submodel of model.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
    // Training document information
    if (model.trainingDocuments) {
        for (const doc of model.trainingDocuments) {
            console.log(`Document name: ${doc.name}`);
            console.log(`Document status: ${doc.status}`);
            console.log(`Document page count: ${doc.pageCount}`);
            console.log(`Document errors: ${doc.errors}`);
        }
    }
}

trainModelLabels().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Выходные данные 

Это выходные данные для модели, обученной по данным для обучения, доступным в [пакете SDK для Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training). Данный пример выходных данных усечен для удобства чтения.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>анализ документов с помощью пользовательской модели;

В этом разделе показано, как извлекать данные вида "ключ/значение" и другое содержимое из пользовательских форм с помощью обученных на них моделей.

> [!IMPORTANT]
> Для реализации этого сценария у вас уже должна быть обученная модель, идентификатор которой нужно будет передать указанному ниже методу. См. раздел [Обучение модели](#train-a-model-without-labels).

Используйте метод `beginRecognizeCustomFormsFromUrl`. Возвращаемое значение представляет собой коллекцию объектов `RecognizedForm`: по одному для каждой страницы обработанного документа.

```javascript
async function recognizeCustom() {
    // Model ID from when you trained your model.
    const modelId = "<modelId>";
    const formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";

    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeCustomForms(modelId, formUrl, {
        onProgress: (state) => { console.log(`status: ${state.status}`); }
    });
    const forms = await poller.pollUntilDone();

    console.log("Forms:");
    for (const form of forms || []) {
        console.log(`${form.formType}, page range: ${form.pageRange}`);
        console.log("Pages:");
        for (const page of form.pages || []) {
            console.log(`Page number: ${page.pageNumber}`);
            console.log("Tables");
            for (const table of page.tables || []) {
                for (const cell of table.cells) {
                    console.log(`cell (${cell.rowIndex},${cell.columnIndex}) ${cell.text}`);
                }
            }
        }

        console.log("Fields:");
        for (const fieldName in form.fields) {
            // each field is of type FormField
            const field = form.fields[fieldName];
            console.log(
                `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
            );
        }
    }
}

recognizeCustom().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Выходные данные

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Управление пользовательскими моделями

В этом разделе показано, как управлять пользовательскими моделями, которые хранятся в вашей учетной записи. Приведенный ниже код выполняет все задачи по управлению моделями в одной функции (в качестве примера). 

### <a name="get-list-of-models-in-account"></a>Получение списка моделей в учетной записи

Следующий блок кода предоставляет полный список доступных в учетной записи моделей, в том числе сведения о дате и времени их создания и их текущем состоянии.

```javascript
async function listModels() {
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    // returns an async iteratable iterator that supports paging
    const result = client.listCustomModels();
    let i = 0;
    for await (const modelInfo of result) {
        console.log(`model ${i++}:`);
        console.log(modelInfo);
    }
}

listModels().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Выходные данные

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids"></a>Получение списка идентификаторов моделей

Этот блок кода предоставляет список моделей и их идентификаторов.

```javascript
async function listModelIds(){
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
    // using `iter.next()`
    i = 1;
    let iter = client.listCustomModels();
    let modelItem = await iter.next();
    while (!modelItem.done) {
        console.log(`model ${i++}: ${modelItem.value.modelId}`);
        modelItem = await iter.next();
    }
}
```

### <a name="output"></a>Выходные данные

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-list-of-model-ids-by-page"></a>Получение списка идентификаторов моделей с разбиением на страницы

Этот блок кода предоставляет список моделей и их идентификаторов с разбиением на страницы.

```javascript
async function listModelsByPage(){
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
    // using `byPage()`
    i = 1;
    for await (const response of client.listCustomModels().byPage()) {
        for (const modelInfo of response.modelList) {
            console.log(`model ${i++}: ${modelInfo.modelId}`);
        }
    }
}

listModelsByPage().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Выходные данные

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
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

### <a name="output"></a>Выходные данные

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Выполнение приложения

С помощью следующей команды вы можете запустить приложение в любое время с использованием любого числа функций, о которых вы узнали из этого краткого руководства.

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

## <a name="see-also"></a>См. также

* [Что такое Распознаватель документов?](../../overview.md)
