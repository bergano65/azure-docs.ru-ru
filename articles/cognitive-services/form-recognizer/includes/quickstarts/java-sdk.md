---
title: Краткое руководство. Клиентская библиотека Распознавателя документов для Java
description: Узнайте, как использовать клиентскую библиотеку Распознавателя документов для Java для создания приложения для обработки форм, которое извлекает из пользовательских документов пары "ключ — значение" и табличные данные.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 4632c5cb12b6d3c2a1b8d4baebf37e9237704591
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318942"
---
> [!IMPORTANT]
> * Пакет SDK Распознавателя документов в настоящее время предназначен для версии 2.0 службы "Распознаватель документов".
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. См. справочную документацию ниже. 

[Справочная документация](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Пакет (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Примеры](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Набор данных для обучения в хранилище BLOB-объектов Azure. Советы и варианты для объединения данных для обучения см. в статье о [создании обучающего набора данных для пользовательской модели](../../build-training-data-set.md). При работе с этим кратким руководством можно использовать файлы в папке **Train** из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2090451).
* Текущая версия [пакета средств разработки Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Средство сборки Gradle](https://gradle.org/install/) или другой диспетчер зависимостей.
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Создание ресурса Распознавателя документов"  target="_blank">созданию ресурса Распознавателя документов <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Распознавателя документов потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-gradle-project"></a>Создание проекта Gradle

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `gradle init` из рабочей папки. Эта команда создает необходимые файлы сборки для Gradle, включая *build.gradle.kts*, который используется во время выполнения для создания и настройки приложения.

```console
gradle init --type basic
```

Когда появится запрос на выбор **предметно-ориентированного языка**, выберите **Kotlin**.

Выполните следующие команды из рабочего каталога:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В этом кратком руководстве используется диспетчер зависимостей Gradle. Клиентскую библиотеку и информацию для других диспетчеров зависимостей можно найти в [центральном репозитории Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

В файле проекта *build.gradle.kts* включите клиентскую библиотеку в качестве оператора `implementation` наряду с требуемыми подключаемыми модулями и параметрами.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

Перейдите к новой папке **src/main/java** и создайте файл с именем *Management.java*. Откройте его в предпочитаемом редакторе или интегрированной среде разработки и добавьте следующие операторы `import`:

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.training.*;
import com.azure.ai.formrecognizer.models.*;
import com.azure.ai.formrecognizer.training.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

Добавьте класс и метод `main`, а также переменные для конечной точки и ключа Azure вашего ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку. Методы будут определены позже.


```java
public class FormRecognizer {
    public static void main(String[] args)
    {
        String key = "<replace-with-your-form-recognizer-key>";
        String endpoint = "<replace-with-your-form-recognizer-endpoint>";
    }
}
```

## <a name="object-model"></a>Объектная модель 

С помощью Распознавателя документов можно создавать клиенты двух разных типов. Первый, `FormRecognizerClient`, используется для запроса из службы распознанных полей и содержимого форм. Второй, `FormTrainingClient`, используется для создания настраиваемых моделей и управления ими. Эти модели можно использовать для улучшения распознавания. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` предоставляет операции для перечисленных ниже целей.

- Распознавание полей форм и содержимого с помощью настраиваемых моделей, обученных для распознавания пользовательских форм.  Эти значения возвращаются в коллекцию объектов `RecognizedForm`. См. пример в разделе об [анализе пользовательских форм](#analyze-forms-with-a-custom-model).
- Распознавание содержимого формы, в том числе таблиц, строк и слов, без необходимости обучения модели.  Содержимое форм возвращается в коллекцию объектов `FormPage`. См. пример в разделе о [распознавании содержимого формы](#recognize-form-content).
- Распознавание общих полей в квитанциях для США с помощью предварительно обученной модели для обработки квитанций в службе "Распознаватель документов".  Эти поля и метаданные возвращаются в коллекцию объектов `RecognizedForm`. См. пример в разделе о [распознавании квитанций](#recognize-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` предоставляет операции для перечисленных ниже целей.

- Обучение настраиваемых моделей для распознавания всех полей и значений в пользовательских формах.  Возвращаемый объект `CustomFormModel` задает типы форм, которые будет распознавать модель, и поля, которые она будет извлекать для каждого из этих типов.
- Обучение пользовательских моделей для распознавания конкретных полей и значений, указываемых путем добавления меток к пользовательским формам.  Возвращаемый объект `CustomFormModel` задает поля, которые будут извлечены моделью, а также предполагаемую точность для каждого поля.
- Управление моделями, созданными в учетной записи.
- Копирование настраиваемой модели из одного ресурса Распознавателя документов в другой.

> [!NOTE]
> Модели можно также обучить с помощью графического пользовательского интерфейса, например [средства маркировки Распознавателя документов](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Распознавателя документов для Java:

* [аутентификация клиента](#authenticate-the-client);
* [распознавание содержимого формы](#recognize-form-content);
* [распознавание квитанций](#recognize-receipts);
* [обучение пользовательской модели](#train-a-custom-model);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [управление пользовательскими моделями](#manage-your-custom-models).

## <a name="authenticate-the-client"></a>Аутентификация клиента

Добавьте следующий код в метод `Main`: В этом фрагменте выполняется проверка подлинности двух клиентских объектов с использованием переменных подписки, указанных выше. Здесь применяется объект **AzureKeyCredential**, чтобы при необходимости ключ API можно было обновить, не создавая новых клиентских объектов.

> [!IMPORTANT]
> Получите ключ и конечную точку на портале Azure. Если ресурс Распознавателя документов, созданный в соответствии с указаниями в разделе **Предварительные требования**, успешно развернут, нажмите кнопку **Перейти к ресурсу** в разделе **Дальнейшие действия**. Ключ и конечная точка располагаются на странице **ключа и конечной точки** ресурса в разделе **управления ресурсами**. 
>
> Не забудьте удалить ключ из кода, когда закончите, и никогда не публикуйте его в открытом доступе. Для рабочей среды рекомендуется использовать безопасный способ хранения и доступа к учетным данным. Например, [хранилище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

```java
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential(key))
    .endpoint(endpoint)
    .buildClient();
    
    FormTrainingClient trainingClient = new FormTrainingClientBuilder()
    .credential(new AzureKeyCredential(key))
    .endpoint(endpoint)
    .buildClient();
```

### <a name="call-client-specific-methods"></a>Вызов клиентских методов

В следующем блоке кода с помощью клиентских объектов вызываются методы для каждой из ключевых задач в пакете SDK Распознавателя документов. Эти методы будут определены позже.

Также потребуется добавить URL-адреса данных для обучения и тестирования.

* Чтобы получить подписанный URL-адрес данных для обучения пользовательской модели, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Получить подписанный URL-адрес**. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Получить URL-адрес формы для тестирования (отдельного документа в хранилище больших двоичных объектов) можно с помощью описанных выше действий. Также можно взять URL-адрес документа, расположенного в другом месте.
* С помощью описанного выше метода также получите URL-адрес изображения квитанции.

> [!NOTE]
> Во фрагментах кода в этом руководстве используются удаленные формы, доступ к которым осуществляется по URL-адресам. Если вместо этого вы планируете работать с локальными документами, воспользуйтесь соответствующими методами, которые описаны в [справочной документации](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview).

```java
    String trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    String formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    String receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    String modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl);
```


## <a name="recognize-form-content"></a>Распознавание содержимого формы

С помощью Распознавателя документов можно распознавать таблицы, строки и слова в документах без предварительного обучения модели.

Для распознавания содержимого файла c определенным универсальным кодом ресурса URI используйте метод **beginRecognizeContentFromUrl**.

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

Возвращаемое значение представляет собой коллекцию объектов **FormPage**: по одному для каждой страницы обработанного документа. Приведенный ниже код обрабатывает эти объекты в цикле и выводит извлеченные из документа пары ключей и значений, а также табличные данные.

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
            formPage.getHeight(),
            formPage.getUnit());
        formPage.getTables().forEach(formTable -> {
            System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                formTable.getColumnCount());
            formTable.getCells().forEach(formTableCell -> {
                System.out.printf("Cell has text %s.%n", formTableCell.getText());
            });
            System.out.println();
        });
    });
}
```

### <a name="output"></a>Выходные данные

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="recognize-receipts"></a>Распознавание квитанций

В этом разделе объясняется, как с помощью предварительно обученной модели распознавать используемые в США квитанции и извлекать из них содержимое стандартных полей.

Для распознавания квитанции с определенным универсальным кодом ресурса (URI) используйте метод **beginRecognizeReceiptsFromUrl**. Возвращаемое значение представляет собой коллекцию объектов **RecognizedReceipt**: по одному для каждой страницы обработанного документа.

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, String receiptUri)
{
    SyncPoller<FormRecognizerOperationResult, List<RecognizedForm>> syncPoller =
    recognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    List<RecognizedForm> receiptPageResults = syncPoller.getFinalResult();
```

Следующий блок кода обрабатывает квитанции в цикле и выводит их данные в консоль.

```java
    for (int i = 0; i < receiptPageResults.size(); i++) {
        RecognizedForm recognizedForm = receiptPageResults.get(i);
        Map<String, FormField> recognizedFields = recognizedForm.getFields();
        System.out.printf("----------- Recognized Receipt page %d -----------%n", i);
        FormField merchantNameField = recognizedFields.get("MerchantName");
        if (merchantNameField != null) {
            if (FieldValueType.STRING == merchantNameField.getValue().getValueType()) {
                String merchantName = merchantNameField.getValue().asString();
                System.out.printf("Merchant Name: %s, confidence: %.2f%n",
                    merchantName, merchantNameField.getConfidence());
            }
        }
        FormField merchantAddressField = recognizedFields.get("MerchantAddress");
        if (merchantAddressField != null) {
            if (FieldValueType.STRING == merchantAddressField.getValue().getValueType()) {
                String merchantAddress = merchantAddressField.getValue().asString();
                System.out.printf("Merchant Address: %s, confidence: %.2f%n",
                    merchantAddress, merchantAddressField.getConfidence());
            }
        }
        FormField transactionDateField = recognizedFields.get("TransactionDate");
        if (transactionDateField != null) {
            if (FieldValueType.DATE == transactionDateField.getValue().getValueType()) {
                LocalDate transactionDate = transactionDateField.getValue().asDate();
                System.out.printf("Transaction Date: %s, confidence: %.2f%n",
                    transactionDate, transactionDateField.getConfidence());
            }
        }
```
Следующий блок кода обрабатывает в цикле отдельные объекты, обнаруженные в квитанции, и выводит их данные в консоль.

```java
        FormField receiptItemsField = recognizedFields.get("Items");
        if (receiptItemsField != null) {
            System.out.printf("Receipt Items: %n");
            if (FieldValueType.LIST == receiptItemsField.getValue().getValueType()) {
                List<FormField> receiptItems = receiptItemsField.getValue().asList();
                receiptItems.stream()
                    .filter(receiptItem -> FieldValueType.MAP == receiptItem.getValue().getValueType())
                    .map(formField -> formField.getValue().asMap())
                    .forEach(formFieldMap -> formFieldMap.forEach((key, formField) -> {
                        if ("Name".equals(key)) {
                            if (FieldValueType.STRING == formField.getValue().getValueType()) {
                                String name = formField.getValue().asString();
                                System.out.printf("Name: %s, confidence: %.2fs%n",
                                    name, formField.getConfidence());
                            }
                        }
                        if ("Quantity".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float quantity = formField.getValue().asFloat();
                                System.out.printf("Quantity: %f, confidence: %.2f%n",
                                    quantity, formField.getConfidence());
                            }
                        }
                        if ("Price".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float price = formField.getValue().asFloat();
                                System.out.printf("Price: %f, confidence: %.2f%n",
                                    price, formField.getConfidence());
                            }
                        }
                        if ("TotalPrice".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float totalPrice = formField.getValue().asFloat();
                                System.out.printf("Total Price: %f, confidence: %.2f%n",
                                    totalPrice, formField.getConfidence());
                            }
                        }
                }));
            }
        }
    }
}
```

### <a name="output"></a>Выходные данные 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="train-a-custom-model"></a>Обучение пользовательской модели

В этом разделе демонстрируется, как обучить модель на основе собственных данных. Обученная модель выводит структурированные данные, которые содержат связи типа "ключ/значение" из исходного документа формы. После обучения модели вы можете проверить ее, повторно обучить и, в конечном итоге, применить с целью надежного извлечения данных из нескольких форм в соответствии со своими потребностями.

> [!NOTE]
> Также можно обучить модель с графическим пользовательским интерфейсом, такой как [пример средства разметки Распознавателя документов](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Обучение моделей без меток

Вы можете обучить свою модель распознавать все поля и значения в ваших формах без разметки вручную документов, которые будете использовать для обучения.

Приведенный ниже метод обучает модель на основе заданного набора документов и выводит ее статус в консоль. 

```java
private static String TrainModel(
    FormTrainingClient trainingClient, String trainingDataUrl)
{
    SyncPoller<FormRecognizerOperationResult, CustomFormModel> trainingPoller =
        trainingClient.beginTraining(trainingDataUrl, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
    System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());
```
Возвращаемый объект **CustomFormModel** содержит сведения о типах форм, с которыми работает модель, и полях, которые она способна извлечь из формы каждого типа. Приведенный ниже блок кода выводит эту информацию в консоль.

```java 
    System.out.println("Recognized Fields:");
    // looping through the subModels, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubmodels().forEach(customFormSubmodel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        System.out.printf("The subModel has form type %s%n", customFormSubmodel.getFormType());
        customFormSubmodel.getFields().forEach((field, customFormModelField) ->
            System.out.printf("The model found field '%s' with label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

Наконец, этот метод возвращает уникальный идентификатор модели.

```java
    return customFormModel.getModelId();
}
```

### <a name="output"></a>Выходные данные

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Обучение моделей с метками

Модели также можно обучать на документах, которые размечены вручную. В некоторых сценариях обучение по такой схеме приводит к лучшим результатам. Для обучения с метками в контейнере больших двоичных объектов вместе с документами, используемыми для обучения, должны находиться особые файлы с информацией о метках ( *\<filename\>.pdf.labels.json*). Эти файлы можно создать в пользовательском интерфейсе [средства разметки Распознавателя документов](../../quickstarts/label-tool.md). Создав файлы, вызовите метод **beginTraining** и задайте для параметра *useTrainingLabels* значение `true`.

```java
private static String TrainModelWithLabels(
    FormTrainingClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<FormRecognizerOperationResult, CustomFormModel> trainingPoller = trainingClient.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
    System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());
```

Возвращаемый объект **CustomFormModel** содержит поля, которые модель способна извлечь из документа, с указанием ориентировочной точности для каждого из них. Приведенный ниже блок кода выводит эту информацию в консоль.

```java
    // looping through the subModels, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubmodels().forEach(customFormSubmodel -> {
        System.out.printf("The subModel with form type %s has accuracy: %.2f%n",
            customFormSubmodel.getFormType(), customFormSubmodel.getAccuracy());
        customFormSubmodel.getFields().forEach((label, customFormModelField) ->
            System.out.printf("The model found field '%s' to have name: %s with an accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

### <a name="output"></a>Выходные данные

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>анализ документов с помощью пользовательской модели;

В этом разделе показано, как извлекать данные вида "ключ/значение" и другое содержимое из пользовательских форм с помощью обученных на них моделей.

> [!IMPORTANT]
> Для реализации этого сценария у вас уже должна быть обученная модель, идентификатор которой нужно будет передать указанному ниже методу. См. раздел [Обучение модели](#train-a-model-without-labels).

Используйте метод **beginRecognizeCustomFormsFromUrl**. Возвращаемое значение представляет собой коллекцию объектов **RecognizedForm**: по одному для каждой страницы обработанного документа.

```java
// Analyze PDF form data
private static void AnalyzePdfForm(
    FormRecognizerClient formClient, String modelId, String pdfFormUrl)
{    
    SyncPoller<FormRecognizerOperationResult, List<RecognizedForm>> recognizeFormPoller =
    formClient.beginRecognizeCustomFormsFromUrl(modelId, pdfFormUrl);

    List<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

Следующий код выводит в консоль результаты анализа. Он отображает каждое из распознанных полей с соответствующим значением и коэффициентом достоверности.

```java
    for (int i = 0; i < recognizedForms.size(); i++) {
        final RecognizedForm form = recognizedForms.get(i);
        System.out.printf("----------- Recognized custom form info for page %d -----------%n", i);
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) ->
            // label data is populated if you are using a model trained with unlabeled data,
            // since the service needs to make predictions for labels if not explicitly given to it.
            System.out.printf("Field '%s' has label '%s' with a confidence "
                + "score of %.2f.%n", label, formField.getLabelData().getText(), formField.getConfidence()));
    }
}
```

### <a name="output"></a>Выходные данные

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>Управление пользовательскими моделями

В этом разделе показано, как управлять пользовательскими моделями, которые хранятся в вашей учетной записи. Приведенный ниже код выполняет все задачи по управлению моделями в одном методе (в качестве примера). Сначала скопируйте сигнатуру метода ниже:

```java
private static void ManageModels(
    FormTrainingClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Проверка числа моделей в учетной записи ресурсов FormRecognizer

Приведенный ниже блок кода проверяет, сколько моделей сохранено в вашей учетной записи Распознавателя документов, и сравнивает это значение с ограничением для данной учетной записи.

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = trainingClient.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCustomModelCount(), accountProperties.getCustomModelLimit());
```

#### <a name="output"></a>Выходные данные 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Вывод списка моделей, которые хранятся в учетной записи ресурсов

Приведенный ниже блок кода выводит в консоль список текущих моделей в вашей учетной записи с подробными сведениями о них.

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = trainingClient.listCustomModels();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = trainingClient.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Training started on: %s%n", customModel.getTrainingStartedOn());
        System.out.printf("Training completed on: %s%n", customModel.getTrainingCompletedOn());
        customModel.getSubmodels().forEach(customFormSubmodel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubmodel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubmodel.getAccuracy());
            if (customFormSubmodel.getFields() != null) {
                customFormSubmodel.getFields().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }
        });
    });
```

#### <a name="output"></a>Выходные данные 

Этот ответ усечен для удобства чтения.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Удаление модели из учетной записи ресурсов

Модель можно удалить из учетной записи ресурсов по ее идентификатору.

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s, operation completed with status: %s%n", modelId.get(),
    trainingClient.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>Выполнение приложения

Вернитесь к главному каталогу проекта. Затем создайте приложение с помощью следующей команды:

```console
gradle build
```

Запустите приложение, выполнив цель `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Устранение неполадок

Клиенты Распознавателя документов вызывают исключения `ErrorResponseException`. Например, если вы попытаетесь указать URL-адрес недействительного исходного файла, будет вызвано исключение `ErrorResponseException` с описанием причины ошибки. В следующем фрагменте кода ошибка корректно обрабатывается путем перехвата исключения с выводом дополнительной информации.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Включение ведения журнала клиента

Пакеты SDK Azure для Java поддерживают возможность ведения подробных журналов, которые помогают находить ошибки в приложениях и ускоряют их устранение. Такие журналы содержат сведения о работе приложения до момента сбоя, что позволяет определить его первопричину. Инструкции, которые помогут включить ведение журналов, см. на [вики-странице, посвященной журналам](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK).

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве описано использование клиентской библиотеки Распознавателя документов на языке Java для обучения модели и анализа документов различными способами. Теперь следует изучить советы по созданию лучшего набора данных для обучения и созданию более точных моделей.

> [!div class="nextstepaction"]
> [Создание набора данных для обучения](../../build-training-data-set.md)

* [Что такое Распознаватель документов?](../../overview.md)
* Пример кода из этого руководства, а также другие ресурсы можно найти на [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).
