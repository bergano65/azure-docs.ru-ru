---
title: Краткое руководство. Клиентская библиотека Распознавателя документов для Python
description: В этом кратком руководстве описано, как приступить к работе с клиентской библиотекой службы "Распознаватель документов" для Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/21/2020
ms.author: pafarley
ms.openlocfilehash: b178a0b347888f22d9a3c0ee88a203e377cb15be
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864807"
---
> [!IMPORTANT]
> * Пакет SDK Распознавателя документов в настоящее время предназначен для версии 2.0 службы "Распознаватель документов".
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. См. справочную документацию ниже. 

[Справочная документация](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Пакет (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Примеры](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Набор данных для обучения в хранилище BLOB-объектов Azure. Советы и варианты для объединения данных для обучения см. в статье о [создании обучающего набора данных для пользовательской модели](../../build-training-data-set.md). При работе с этим кратким руководством можно использовать файлы в папке **Train** из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2090451).
* [Python 2.7, 3.5 или более поздней версии](https://www.python.org/)
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Создание ресурса Распознавателя документов"  target="_blank">созданию ресурса Распознавателя документов <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Распознавателя документов потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="object-model"></a>Объектная модель 

С помощью Распознавателя документов можно создавать клиенты двух разных типов. Первый, `form_recognizer_client`, используется для запроса из службы распознанных полей и содержимого форм. Второй, `form_training_client`, используется для создания настраиваемых моделей и управления ими. Эти модели можно использовать для улучшения распознавания. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client` предоставляет операции для перечисленных ниже целей.

 * Распознавание полей форм и содержимого с помощью настраиваемых моделей, обученных для распознавания пользовательских форм. 
 * Распознавание содержимого формы, в том числе таблиц, строк и слов, без необходимости обучения модели. 
 * Распознавание общих полей в квитанциях с помощью предварительно обученной модели для обработки квитанций в службе "Распознаватель документов".

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client` предоставляет операции для перечисленных ниже целей.

* Обучение настраиваемых моделей для распознавания всех полей и значений в пользовательских формах. Более подробное описание процесса создания набора данных для обучения см. в [документации службы по обучению модели без добавления меток](#train-a-model-without-labels).
* Обучение пользовательских моделей для распознавания конкретных полей и значений, указываемых путем добавления меток к пользовательским формам. Более подробное объяснение применения меток к набору данных для обучения см. в [документации службы по обучению модели с добавлением меток](#train-a-model-with-labels).
* Управление моделями, созданными в учетной записи.
* Копирование настраиваемой модели из одного ресурса Распознавателя документов в другой.

Обратите внимание, что модель можно также обучить с помощью графического пользовательского интерфейса, например [средства разметки Распознавателя документов](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).

## <a name="setting-up"></a>Настройка

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

После установки Python можно установить последнюю версию клиентской библиотеки Распознавателя документов с помощью приведенной ниже команды.

```console
pip install azure-ai-formrecognizer
```

### <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте приложение Python в предпочитаемом редакторе или среде интегрированной разработки. Затем импортируйте приведенные ниже библиотеки. Обратите внимание, что мы импортируем библиотеки, необходимые для обучения и распознавания форм.

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import FormRecognizerClient
from azure.ai.formrecognizer import FormTrainingClient
from azure.core.credentials import AzureKeyCredential
```

Создайте переменные для конечной точки Azure и ключа ресурса. 

```python
endpoint = "<paste-your-form-recognizer-endpoint-here>"
key = "<paste-your-form-recognizer-key-here>"
```

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Распознавателя документов для Python:

* [аутентификация клиента](#authenticate-the-client);
* [распознавание содержимого формы](#recognize-form-content);
* [распознавание квитанций](#recognize-receipts);
* [обучение пользовательской модели](#train-a-custom-model);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [управление пользовательскими моделями](#manage-your-custom-models).


## <a name="authenticate-the-client"></a>Аутентификация клиента

В этом фрагменте выполняется проверка подлинности двух клиентских объектов с использованием переменных подписки, указанных выше. Здесь применяется объект `AzureKeyCredential`, чтобы при необходимости ключ API можно было обновить, не создавая новых клиентских объектов.

```python
form_recognizer_client = FormRecognizerClient(endpoint, AzureKeyCredential(key))
form_training_client = FormTrainingClient(endpoint, AzureKeyCredential(key))
```

## <a name="assets-for-testing"></a>Ресурсы для тестирования

Во фрагментах кода в этом руководстве используются удаленные формы, доступ к которым осуществляется по URL-адресам. Если вместо этого вы планируете работать с локальными документами, воспользуйтесь соответствующими методами, которые описаны в [справочной документации](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) и [образцах](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Также потребуется добавить URL-адреса данных для обучения и тестирования.
* Чтобы получить подписанный URL-адрес данных для обучения пользовательской модели, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Получить подписанный URL-адрес**. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Используйте пример формы и изображения квитанций, включенные в приведенные ниже примеры. Вы можете также скачать их с [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) или выполнить описанные выше действия, чтобы получить URL-адрес SAS для отдельного документа в хранилище BLOB-объектов. 

> [!NOTE]
> Во фрагментах кода в этом руководстве используются удаленные формы, доступ к которым осуществляется по URL-адресам. Если вместо этого вы планируете работать с локальными документами, воспользуйтесь соответствующими методами, которые описаны в [справочной документации](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Распознавание содержимого формы

С помощью Распознавателя документов можно распознавать таблицы, строки и слова в документах без предварительного обучения модели.

Для распознавания содержимого файла c определенным универсальным кодом ресурса URI используйте метод `begin_recognize_content`. Возвращаемое значение представляет собой коллекцию объектов `FormPage`: по одному для каждой страницы обработанного документа. Приведенный ниже код обрабатывает эти объекты в цикле и выводит извлеченные из документа пары ключей и значений, а также табличные данные.

```Python
formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf"

poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
page = poller.result()

table = page[0].tables[0] # page 1, table 1
print("Table found on page {}:".format(table.page_number))
for cell in table.cells:
    print("Cell text: {}".format(cell.text))
    print("Location: {}".format(cell.bounding_box))
    print("Confidence score: {}\n".format(cell.confidence))
```

### <a name="output"></a>Выходные данные

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0

...

```

## <a name="recognize-receipts"></a>Распознавание квитанций

В этом разделе объясняется, как с помощью предварительно обученной модели распознавать используемые в США квитанции и извлекать из них содержимое стандартных полей. Для распознавания квитанции с определенным URL-адресом используйте метод `begin_recognize_receipts_from_url`. 

```python
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"

poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
result = poller.result()

for receipt in result:
    for name, field in receipt.fields.items():
        if name == "Items":
            print("Receipt Items:")
            for idx, items in enumerate(field.value):
                print("...Item #{}".format(idx + 1))
                for item_name, item in items.value.items():
                    print("......{}: {} has confidence {}".format(item_name, item.value, item.confidence))
        else:
            print("{}: {} has confidence {}".format(name, field.value, field.confidence))
```

### <a name="output"></a>Выходные данные

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="train-a-custom-model"></a>Обучение пользовательской модели

В этом разделе демонстрируется, как обучить модель на основе собственных данных. Обученная модель выводит структурированные данные, которые содержат связи типа "ключ/значение" из исходного документа формы. После обучения модели вы можете проверить ее, повторно обучить и, в конечном итоге, применить с целью надежного извлечения данных из нескольких форм в соответствии со своими потребностями.

> [!NOTE]
> Также можно обучить модель с графическим пользовательским интерфейсом, такой как [пример средства разметки Распознавателя документов](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Обучение моделей без меток

Вы можете обучить свою модель распознавать все поля и значения в ваших формах без разметки вручную документов, которые будете использовать для обучения.

В следующем фрагменте кода с помощью клиента обучения с функцией `begin_training` выполняется обучение модели на основе заданного набора документов. Возвращаемый объект `CustomFormModel` содержит сведения о типах форм, с которыми работает модель, и полях, которые она способна извлечь из формы каждого типа. Приведенный ниже блок кода выводит эту информацию в консоль.

```python
# To train a model you need an Azure Storage account.
# Use the SAS URL to access your training files.
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"

poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=False)
model = poller.result()

print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Training started on: {}".format(model.training_started_on))
print("Training completed on: {}".format(model.training_completed_on))

print("\nRecognized fields:")
for submodel in model.submodels:
    print(
        "The submodel with form type '{}' has recognized the following fields: {}".format(
            submodel.form_type,
            ", ".join(
                [
                    field.label if field.label else name
                    for name, field in submodel.fields.items()
                ]
            ),
        )
    )

# Training result information
for doc in model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

### <a name="output"></a>Выходные данные

Это выходные данные для модели, обученной по данным для обучения, доступным в [пакете SDK для Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Обучение моделей с метками

Модели также можно обучать на документах, которые размечены вручную. В некоторых сценариях обучение по такой схеме приводит к лучшим результатам. Возвращаемый объект `CustomFormModel` содержит поля, которые модель способна извлечь из документа, с указанием ориентировочной точности для каждого из них. Приведенный ниже блок кода выводит эту информацию в консоль.

> [!IMPORTANT]
> Для обучения с метками в контейнере хранилища BLOB-объектов вместе с документами, используемыми для обучения, должны находиться особые файлы с информацией о метках (`\<filename\>.pdf.labels.json`). Эти файлы можно создать в пользовательском интерфейсе [средства разметки Распознавателя документов](../../quickstarts/label-tool.md). Создав файлы, вызовите функцию `begin_training` и задайте для параметра *use_training_labels* значение `true`.

```python
# To train a model you need an Azure Storage account.
# Use the SAS URL to access your training files.
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"

poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
model = poller.result()

print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Training started on: {}".format(model.training_started_on))
print("Training completed on: {}".format(model.training_completed_on))

print("\nRecognized fields:")
for submodel in model.submodels:
    print(
        "The submodel with form type '{}' has recognized the following fields: {}".format(
            submodel.form_type,
            ", ".join(
                [
                    field.label if field.label else name
                    for name, field in submodel.fields.items()
                ]
            ),
        )
    )

# Training result information
for doc in model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

### <a name="output"></a>Выходные данные

Это выходные данные для модели, обученной по данным для обучения, доступным в [пакете SDK для Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>анализ документов с помощью пользовательской модели;

В этом разделе показано, как извлекать данные вида "ключ/значение" и другое содержимое из пользовательских форм с помощью обученных на них моделей.

> [!IMPORTANT]
> Для реализации этого сценария у вас уже должна быть обученная модель, идентификатор которой нужно будет передать указанному ниже методу. См. раздел [Обучение модели](#train-a-model-without-labels).

Используйте метод `begin_recognize_custom_forms_from_url`. Возвращаемое значение представляет собой коллекцию объектов `RecognizedForm`: по одному для каждой страницы обработанного документа. Следующий код выводит в консоль результаты анализа. Он отображает каждое из распознанных полей с соответствующим значением и коэффициентом достоверности.

```python
# Model ID from when you trained your model.
model_id = "<your custom model id>"

poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model_id, form_url=formUrl)
result = poller.result()

for recognized_form in result:
    print("Form type: {}".format(recognized_form.form_type))
    for name, field in recognized_form.fields.items():
        print("Field '{}' has label '{}' with value '{}' and a confidence score of {}".format(
            name,
            field.label_data.text if field.label_data else name,
            field.value,
            field.confidence
        ))
```

### <a name="output"></a>Выходные данные

При использовании модели из предыдущего примера предоставляются приведенные ниже выходные данные.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>Управление пользовательскими моделями

В этом разделе показано, как управлять пользовательскими моделями, которые хранятся в вашей учетной записи. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Проверка числа моделей в учетной записи ресурсов FormRecognizer

Приведенный ниже блок кода проверяет, сколько моделей сохранено в вашей учетной записи Распознавателя документов, и сравнивает это значение с ограничением для данной учетной записи.

```python
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="output"></a>Выходные данные

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Вывод списка моделей, которые хранятся в учетной записи ресурсов

Приведенный ниже блок кода выводит в консоль список текущих моделей в вашей учетной записи с подробными сведениями о них. Он также сохраняет ссылку на первую модель.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_custom_models()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="output"></a>Выходные данные

Это пример выходных данных для тестовой учетной записи.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Получение определенной модели по ее идентификатору

Следующий блок кода получает подробные сведения о модели по ее идентификатору, сохраненному в предыдущем разделе.

```python
model_id = "<model_id from the Train a Model sample>"

custom_model = form_training_client.get_custom_model(model_id=model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Training started on: {}".format(custom_model.training_started_on))
print("Training completed on: {}".format(custom_model.training_completed_on))
```

### <a name="output"></a>Выходные данные

Это пример выходных данных для настраиваемой модели, созданной в предыдущем примере.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Удаление модели из учетной записи ресурсов

Модель можно удалить из учетной записи ресурсов по ее идентификатору. Этот фрагмент кода удаляет модель, которая использовалась в предыдущем разделе.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
```

## <a name="run-the-application"></a>Выполнение приложения

С помощью следующей команды вы можете запустить приложение в любое время с использованием любого числа функций, о которых вы узнали из этого краткого руководства.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="general"></a>Общие сведения

Клиентская библиотека Распознавателя документов вызывает исключения, определенные в [Azure Core](https://aka.ms/azsdk-python-azure-core).

## <a name="logging"></a>Logging

Эта библиотека использует для ведения журнала [соответствующую стандартную библиотеку](https://docs.python.org/3/library/logging.html). На уровне информации (INFO) в журнал заносится основная информация о сеансах HTTP (URL-адреса, заголовки и т. д.).

С помощью аргумента-ключевого слова `logging_enable` можно включить в клиенте ведение журнала на уровне отладки (DEBUG), на котором фиксируются сведения о телах запросов и ответов, а также заголовки без изменений:

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

Аналогичным образом с помощью параметра `logging_enable` можно включить подробное журналирование для отдельной операции (даже если этот режим не включен в клиенте):

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описано использование клиентской библиотеки Распознавателя документов на языке Python для обучения модели и анализа документов различными способами. Теперь следует изучить советы по созданию лучшего набора данных для обучения и созданию более точных моделей.

> [!div class="nextstepaction"]
> [Создание набора данных для обучения](../../build-training-data-set.md)

## <a name="see-also"></a>См. также

* [Что такое Распознаватель документов?](../../overview.md)
