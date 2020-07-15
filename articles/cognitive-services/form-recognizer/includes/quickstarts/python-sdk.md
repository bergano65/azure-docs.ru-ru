---
title: Краткое руководство. Клиентская библиотека Распознавателя документов для Python
description: В этом кратком руководстве описано, как приступить к работе с клиентской библиотекой службы "Распознаватель документов" для Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: 3300bd53be36e34ff32267605352d6f80923724e
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277778"
---
[Справочная документация](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Пакет (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Примеры](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Набор данных для обучения в хранилище BLOB-объектов Azure. Советы и варианты для объединения данных для обучения см. в статье о [создании обучающего набора данных для пользовательской модели](../../build-training-data-set.md). При работе с этим кратким руководством можно использовать файлы в папке **Train** из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2090451).
* [Python 2.7, 3.5 или более поздней версии](https://www.python.org/)

## <a name="setting-up"></a>Настройка

### <a name="create-a-form-recognizer-azure-resource"></a>Создание ресурса Распознавателя документов Azure

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Создание переменных среды

[!INCLUDE [environment-variables](../environment-variables.md)]


### <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте приложение Python в предпочитаемом редакторе или среде интегрированной разработки. Затем импортируйте приведенные ниже библиотеки.

```python
import os
import azure.ai.formrecognizer
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import ResourceNotFoundError
```

Создайте переменные для конечной точки Azure и ключа ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку.

```python
endpoint = os.environ["FORM_RECOGNIZER_ENDPOINT"]
key = os.environ["FORM_RECOGNIZER_KEY"]
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

После установки Python вы можете установить клиентскую библиотеку с помощью следующей команды:

```console
pip install azure-ai-formrecognizer
```

<!-- 
tbd object model
-->

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Распознавателя документов для Python:

* [аутентификация клиента](#authenticate-the-client);
* [распознавание содержимого формы](#recognize-form-content);
* [распознавание квитанций](#recognize-receipts);
* [обучение пользовательской модели](#train-a-custom-model);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [управление пользовательскими моделями](#manage-your-custom-models).


## <a name="authenticate-the-client"></a>Аутентификация клиента

В этом фрагменте выполняется проверка подлинности двух клиентских объектов с использованием переменных подписки, указанных выше. Здесь применяется объект **AzureKeyCredential**, чтобы при необходимости ключ API можно было обновить, не создавая новых клиентских объектов.

```python
form_recognizer_client = FormRecognizerClient(endpoint=endpoint, credential=AzureKeyCredential(key))

form_training_client = FormTrainingClient(endpoint, AzureKeyCredential(key))
```

## <a name="define-variables"></a>Определение переменных

> [!NOTE]
> Во фрагментах кода в этом руководстве используются удаленные формы, доступ к которым осуществляется по URL-адресам. Если вместо этого вы планируете работать с локальными документами, воспользуйтесь соответствующими методами, которые описаны в [справочной документации](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) и [образцах](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Также потребуется добавить URL-адреса данных для обучения и тестирования.
* Чтобы получить подписанный URL-адрес данных для обучения пользовательской модели, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Получить подписанный URL-адрес**. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Получить URL-адрес формы для тестирования (отдельного документа в хранилище больших двоичных объектов) можно с помощью описанных выше действий. Также можно взять URL-адрес документа, расположенного в другом месте.
* С помощью описанного выше метода также получите URL-адрес изображения квитанции (либо воспользуйтесь URL-адресом примера такого изображения).

```python
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"
formUrl = "<SAS-URL-of-a-form-in-blob-storage>"
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"
```

## <a name="recognize-form-content"></a>Распознавание содержимого формы

С помощью Распознавателя документов можно распознавать таблицы, строки и слова в документах без предварительного обучения модели.

Для распознавания содержимого файла c определенным универсальным кодом ресурса URI используйте метод **begin_recognize_content**.

```Python
poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
contents = poller.result()
```

Возвращаемое значение представляет собой коллекцию объектов **FormPage**: по одному для каждой страницы обработанного документа. Приведенный ниже код обрабатывает эти объекты в цикле и выводит извлеченные из документа пары ключей и значений, а также табличные данные.

```python
for idx, content in enumerate(contents):
    print("----Recognizing content from page #{}----".format(idx))
    print("Has width: {} and height: {}, measured with unit: {}".format(
        content.width,
        content.height,
        content.unit
    ))
    for table_idx, table in enumerate(content.tables):
        print("Table # {} has {} rows and {} columns".format(table_idx, table.row_count, table.column_count))
        for cell in table.cells:
            print("...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                cell.row_index,
                cell.column_index,
                cell.text,
                format_bounding_box(cell.bounding_box)
            ))
    for line_idx, line in enumerate(content.lines):
        print("Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
            line_idx,
            len(line.words),
            line.text,
            format_bounding_box(line.bounding_box)
        ))
    print("----------------------------------------")
```

В приведенном выше коде с помощью вспомогательной функции `format_bounding_box` упрощается определение координат ограничивающего прямоугольника. Определите его отдельно:

```python
def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])
```

## <a name="recognize-receipts"></a>Распознавание квитанций

В этом разделе объясняется, как с помощью предварительно обученной модели распознавать используемые в США квитанции и извлекать из них содержимое стандартных полей. Для распознавания квитанции с определенным URL-адресом используйте метод **begin_recognize_receipts_from_url**. 

```python
poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
receipts = poller.result()
```

Возвращаемое значение представляет собой коллекцию объектов **RecognizedReceipt**: по одному для каждой страницы обработанного документа. В следующем блоке кода основные сведения о квитанции выводятся в консоль.

```python
for idx, receipt in enumerate(receipts):
    print("--------Recognizing receipt #{}--------".format(idx))
    receipt_type = receipt.fields.get("ReceiptType")
    if receipt_type:
        print("Receipt Type: {} has confidence: {}".format(receipt_type.value, receipt_type.confidence))
    merchant_name = receipt.fields.get("MerchantName")
    if merchant_name:
        print("Merchant Name: {} has confidence: {}".format(merchant_name.value, merchant_name.confidence))
    transaction_date = receipt.fields.get("TransactionDate")
    if transaction_date:
        print("Transaction Date: {} has confidence: {}".format(transaction_date.value, transaction_date.confidence))
```

Следующий блок кода обрабатывает в цикле отдельные объекты, обнаруженные в квитанции, и выводит их данные в консоль.


```python
    print("Receipt items:")
    for idx, item in enumerate(receipt.fields.get("Items").value):
        print("...Item #{}".format(idx))
        item_name = item.value.get("Name")
        if item_name:
            print("......Item Name: {} has confidence: {}".format(item_name.value, item_name.confidence))
        item_quantity = item.value.get("Quantity")
        if item_quantity:
            print("......Item Quantity: {} has confidence: {}".format(item_quantity.value, item_quantity.confidence))
        item_price = item.value.get("Price")
        if item_price:
            print("......Individual Item Price: {} has confidence: {}".format(item_price.value, item_price.confidence))
        item_total_price = item.value.get("TotalPrice")
        if item_total_price:
            print("......Total Item Price: {} has confidence: {}".format(item_total_price.value, item_total_price.confidence))
```

Наконец, последний блок кода выводит в консоль остальную ключевую информацию о квитанции.

```python
    subtotal = receipt.fields.get("Subtotal")
    if subtotal:
        print("Subtotal: {} has confidence: {}".format(subtotal.value, subtotal.confidence))
    tax = receipt.fields.get("Tax")
    if tax:
        print("Tax: {} has confidence: {}".format(tax.value, tax.confidence))
    tip = receipt.fields.get("Tip")
    if tip:
        print("Tip: {} has confidence: {}".format(tip.value, tip.confidence))
    total = receipt.fields.get("Total")
    if total:
        print("Total: {} has confidence: {}".format(total.value, total.confidence))
    print("--------------------------------------")
```


## <a name="train-a-custom-model"></a>Обучение пользовательской модели

В этом разделе демонстрируется, как обучить модель на основе собственных данных. Обученная модель выводит структурированные данные, которые содержат связи типа "ключ/значение" из исходного документа формы. После обучения модели вы можете проверить ее, повторно обучить и, в конечном итоге, применить с целью надежного извлечения данных из нескольких форм в соответствии со своими потребностями.

> [!NOTE]
> Также можно обучить модель с графическим пользовательским интерфейсом, такой как [пример средства разметки Распознавателя документов](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Обучение моделей без меток

Вы можете обучить свою модель распознавать все поля и значения в ваших формах без разметки вручную документов, которые будете использовать для обучения.

В следующем фрагменте кода с помощью клиента обучения с функцией **begin_train_model** выполняется обучение модели на основе заданного набора документов.

```python
poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=False)
model = poller.result()
```

Возвращаемый объект **CustomFormSubmodel** содержит сведения о типах форм, с которыми работает модель, и полях, которые она способна извлечь из формы каждого типа. Приведенный ниже блок кода выводит эту информацию в консоль.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.requested_on))
print("Last modified: {}".format(model.completed_on))

print("Recognized fields:")
# Looping through the submodels, which contains the fields they were trained on
for submodel in model.submodels:
    print("...The submodel has form type '{}'".format(submodel.form_type))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have label '{}'".format(
            name, field.label
        ))
```

### <a name="train-a-model-with-labels"></a>Обучение моделей с метками

Модели также можно обучать на документах, которые размечены вручную. В некоторых сценариях обучение по такой схеме приводит к лучшим результатам. 

> [!IMPORTANT]
> Для обучения с метками в контейнере больших двоичных объектов вместе с документами, используемыми для обучения, должны находиться особые файлы с информацией о метках ( *\<filename\>.pdf.labels.json*). Эти файлы можно создать в пользовательском интерфейсе [средства разметки Распознавателя документов](../../quickstarts/label-tool.md). Создав файлы, вызовите функцию **begin_training** и задайте для параметра *use_training_labels* значение `true`.

```python
poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
model = poller.result()
```

Возвращаемый объект **CustomFormSubmodel** содержит поля, которые модель может извлечь, с указанием ориентировочной точности для каждого из них. Приведенный ниже блок кода выводит эту информацию в консоль.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# looping through the submodels, which contains the fields they were trained on
# The labels are based on the ones you gave the training document.
for submodel in model.submodels:
    print("...The submodel with form type {} has accuracy '{}'".format(submodel.form_type, submodel.accuracy))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have name '{}' with an accuracy of {}".format(
            name, field.name, field.accuracy
        ))
```

## <a name="analyze-forms-with-a-custom-model"></a>анализ документов с помощью пользовательской модели;

В этом разделе показано, как извлекать данные вида "ключ/значение" и другое содержимое из пользовательских форм с помощью обученных на них моделей.

> [!IMPORTANT]
> Для реализации этого сценария у вас уже должна быть обученная модель, идентификатор которой нужно будет передать указанному ниже методу. См. раздел [Обучение модели](#train-a-model-without-labels).

Используйте метод **begin_recognize_custom_forms_from_url**. Возвращаемое значение представляет собой коллекцию объектов **RecognizedForm**: по одному для каждой страницы обработанного документа.

```python
# Make sure your form's type is included in the list of form types the custom model can recognize
poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model.model_id, form_url=formUrl)
forms = poller.result()
```

Следующий код выводит в консоль результаты анализа. Он отображает каждое из распознанных полей с соответствующим значением и коэффициентом достоверности.

```python
for idx, form in enumerate(forms):
    print("--------Recognizing Form #{}--------".format(idx))
    print("Form {} has type {}".format(idx, form.form_type))
    for name, field in form.fields.items():
        # each field is of type FormField
        # The value of the field can also be a FormField, or a list of FormFields
        # In our sample, it is just a FormField.
        print("...Field '{}' has value '{}' with a confidence score of {}".format(
            name, field.value, field.confidence
        ))
        # label data is populated if you are using a model trained with unlabeled data, since the service needs to make predictions for
        # labels if not explicitly given to it.
        if field.label_data:
            print("...Field '{}' has label '{}' with a confidence score of {}".format(
                name,
                field.label_data.text,
                field.confidence
            ))
    print("-----------------------------------")
```

## <a name="manage-your-custom-models"></a>Управление пользовательскими моделями

В этом разделе показано, как управлять пользовательскими моделями, которые хранятся в вашей учетной записи. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Проверка числа моделей в учетной записи ресурсов FormRecognizer

Приведенный ниже блок кода проверяет, сколько моделей сохранено в вашей учетной записи Распознавателя документов, и сравнивает это значение с ограничением для данной учетной записи.

```python
# First, we see how many custom models we have, and what our limit is
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
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

### <a name="get-a-specific-model-using-the-models-id"></a>Получение определенной модели по ее идентификатору

Следующий блок кода получает подробные сведения о модели по ее идентификатору, сохраненному в предыдущем разделе.

```python
# Now we'll get the first custom model in the paged list
custom_model = form_training_client.get_custom_model(model_id=first_model.model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Created on: {}".format(custom_model.requested_on))
print("Last modified: {}".format(custom_model.completed_on))
```

### <a name="delete-a-model-from-the-resource-account"></a>Удаление модели из учетной записи ресурсов

Модель можно удалить из учетной записи ресурсов по ее идентификатору. Этот фрагмент кода удаляет модель, которая использовалась в предыдущем разделе.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

# Confirm deletion:
try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
}
```

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `python` для файла quickstart.

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

* [Что такое Распознаватель документов?](../../overview.md)
* Пример кода из этого руководства, а также другие ресурсы можно найти на [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).
