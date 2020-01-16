---
title: Создание конвейеров прогнозных данных
description: Узнайте, как создать прогнозирующий конвейер с помощью действия выполнения пакета службы "Машинное обучение Azure" в фабрике данных Azure.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 44371c78a4d02588eef21aae5a4bba3d033763d7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029995"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Текущая версия](transform-data-using-machine-learning.md)

[Машинное обучение Azure](https://azure.microsoft.com/documentation/services/machine-learning/) позволяет создавать, тестировать и развертывать решения для прогнозной аналитики. Этот процесс можно обобщить до трех этапов.

1. **Создание обучающего эксперимента**. Этот шаг выполняется с помощью Машинное обучение Azure Studio (классическая модель). Машинное обучение Azure Studio (классическая модель) — это среда для совместной работы визуальной разработки, используемая для обучения и тестирования модели прогнозной аналитики с помощью обучающих данных.
2. **Преобразование обучающего эксперимента в прогнозный**. Когда модель будет обучена с помощью существующих данных и готова для оценки новых данных, следует подготовить и оптимизировать эксперимент для оценки.
3. **Развертывание эксперимента в виде веб-службы**. Оценивающий эксперимент можно опубликовать в виде веб-службы Azure. С помощью конечной точки этой веб-службы вы можете отправлять данные в свою модель и получать из нее результаты прогнозов.

### <a name="data-factory-and-machine-learning-together"></a>Фабрика данных и машинное обучение вместе
Фабрика данных Azure позволяет легко создавать конвейеры, использующие опубликованную [машинное обучение Azure](https://azure.microsoft.com/documentation/services/machine-learning) веб-службу для прогнозной аналитики. С помощью **действия выполнения пакета** в конвейере фабрики данных Azure можно вызвать веб-службу машинное обучение Azure Studio (классическая), чтобы сделать прогнозы для данных в пакетной службе.

Со временем прогнозные модели в экспериментах оценки Машинное обучение Azure Studio (классическая модель) необходимо переучить с помощью новых входных наборов данных. Чтобы повторно обучить модель из конвейера Фабрики данных, выполните следующие действия:

1. Опубликуйте обучающий (не прогнозный) эксперимент в виде веб-службы. Этот шаг выполняется в Машинное обучение Azure Studio (классическая модель), так как в предыдущем сценарии можно предоставить прогнозный эксперимент в качестве веб-службы.
2. Используйте действие выполнения пакета Машинное обучение Azure Studio (классическая модель), чтобы вызвать веб-службу для обучающего эксперимента. По сути, вы можете использовать действие пакетного выполнения Машинное обучение Azure Studio (классическая модель) для вызова веб-службы обучения и оценки.

После завершения повторного обучения обновите веб-службу оценки (прогнозный эксперимент, представленный в виде веб-службы) с использованием новой обученной модели с помощью **действия ресурса обновления машинное обучение Azure Studio (классическая модель)** . Дополнительные сведения см. в статье [Updating Azure Machine Learning models using Update Resource Activity](update-machine-learning-models.md) (Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс").

## <a name="azure-machine-learning-linked-service"></a>Связанная служба Машинного обучения Azure

Создайте связанную службу **Машинного обучения Azure**, чтобы связывать веб-службы Машинного обучения Azure с фабрикой данных Azure. Связанная служба используется действием выполнения пакета службы "Машинное обучения Azure" и [действием обновления ресурса](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

В статье [Вычисление поддерживаемых сред с помощью фабрики данных Azure](compute-linked-services.md) описаны свойства в определении JSON.

Служба "Машинного обучения Azure" поддерживает как классические, так и новые веб-службы для прогнозного эксперимента. В фабрике данных можно выбрать один из них для использования. Чтобы получить сведения, необходимые для создания связанной службы машинного обучения Azure, перейдите на сайт https://services.azureml.net, где перечислены все (новые и классические) веб-службы. Щелкните веб-службу, к которой требуется получить доступ, а затем щелкните страницу **Использование**. Скопируйте **первичный ключ** для свойства **apiKey** и **запросы пакетной службы** для свойства **mlEndpoint**.

![Веб-службы Машинного обучения Azure](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Действие выполнения пакета Машинного обучения Azure

Следующий фрагмент кода JSON определяет действие выполнения пакета в службе "Машинное обучение Azure". Определение действия содержит ссылку на созданную ранее связанную службу Машинного обучения Azure.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Свойство          | Description                              | Обязательно для заполнения |
| :---------------- | :--------------------------------------- | :------- |
| name              | Имя действия в конвейере.     | Да      |
| description       | Описание действия.  | Нет       |
| type              | Для действия U-SQL Data Lake Analytics в качестве типа действия используется **AzureMLBatchExecution**. | Да      |
| linkedServiceName | Связанные службы для связанной службы Машинного обучения Azure. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md). | Да      |
| webServiceInputs  | Пары "ключ —значение", сопоставляющие имена входных данных веб-службы Машинного обучения Azure. Ключ должен соответствовать входным параметрам, определенным в опубликованной веб-службе Машинного обучения Azure. Значение — это пара свойств связанных служб хранилища Azure и FilePath, указывающих на расположения входных больших двоичных объектов. | Нет       |
| webServiceOutputs | Пары "ключ —значение", сопоставляющие имена выходных данных веб-службы Машинного обучения Azure. Ключ должен соответствовать выходным параметрам, определенным в опубликованной веб-службе Машинного обучения Azure. Значением является пара свойств связанных служб хранилища Azure и FilePath, указывающих на расположения выходных больших двоичных объектов. | Нет       |
| globalParameters  | Пары "ключ — значение", передаваемые в конечную точку службы пакетного выполнения Машинное обучение Azure Studio (классическая модель). Ключи должны соответствовать именам параметров веб-службы, определенных в опубликованной веб-службе Машинное обучение Azure Studio (классическая модель). Значения передаются в свойстве GlobalParameters запроса выполнения пакета Машинное обучение Azure Studio (классическая модель). | Нет       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Сценарий 1. Эксперименты с вводом-выводом веб-службы, ссылающейся на данные в хранилище BLOB-объектов

В этом сценарии веб-служба Машинного обучения Azure делает прогнозы с использованием данных из файла в хранилище больших двоичных объектов Azure и сохраняет результаты прогнозирования в хранилище больших двоичных объектов. Следующий код JSON определяет конвейер фабрики данных действием AzureMLBatchExecution. Входные и выходные данные в хранилище BLOB-объектов Azure указываются с помощью пары LinkedName и FilePath. В образце связанной службы входных и выходных данных можно использовать различные связанные службы для каждого из входных и выходных файлов фабрики, чтобы иметь возможность выбрать нужные файлы и отправить их в веб-службу Машинное обучение Azure Studio (классическая модель).

> [!IMPORTANT]
> В эксперименте Машинное обучение Azure Studio (классическая модель), входные и выходные порты веб-службы и глобальные параметры имеют имена по умолчанию ("INPUT1", "INPUT2"), которые можно настраивать. Имена, используемые для параметров webServiceInputs, webServiceOutputs и globalParameters, должны точно совпадать с именами в экспериментах. Вы можете просмотреть пример полезных данных запроса на странице справки по выполнению пакетов для конечной точки Машинное обучение Azure Studio (классической), чтобы проверить ожидаемое сопоставление.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Сценарий 2. Эксперименты с использованием модулей чтения и записи для обращения к данным в различных хранилищах
Другим распространенным сценарием при создании экспериментов Машинное обучение Azure Studio (классическая модель) является использование модулей импорта данных и выходных данных. Модуль импорта используется для загрузки данных в эксперимент, а модуль вывода — для сохранения данных экспериментов. Дополнительные сведения о модулях импорта и вывода данных см. в [этом](https://msdn.microsoft.com/library/azure/dn905997.aspx) и в [этом](https://msdn.microsoft.com/library/azure/dn905984.aspx) разделах библиотеки MSDN.

При использовании модулей импорта и вывода данных рекомендуется указывать параметр веб-службы для каждого их свойства. Эти параметры веб-службы позволяют настроить значения во время выполнения. Например, можно создать эксперимент с модулем импорта, который использует базу данных SQL Azure: XXX.database.windows.net. После развертывания веб-службы вам необходимо включить объекты-получатели веб-службы, чтобы указать другой сервер Azure SQL `YYY.database.windows.net`. Вы можете использовать параметр веб-службы, чтобы разрешить настройку этого значения.

> [!NOTE]
> Входные и выходные данные веб-службы отличаются от параметров веб-службы. В первом сценарии вы узнали, как можно указать входные и выходные данные для веб-службы Машинное обучение Azure Studio (классическая модель). В этом сценарии вы передаете параметры для веб-службы, соответствующие свойствам модулей импорта и вывода данных.
>
>

Рассмотрим сценарий использования параметров веб-службы. Вы развернули веб-службу машинного обучения Azure, которая использует модуль чтения для чтения данных из одного из поддерживаемых источников данных машинного обучения Azure (например, базы данных SQL Azure). После пакетного выполнения результаты записываются с помощью модуля записи (база данных SQL Azure).  В экспериментах не определены входные и выходные данные веб-службы. В этом случае мы советуем настроить соответствующие параметры веб-службы для модулей чтения и записи. Это позволит настроить их при использовании действия AzureMLBatchExecution. Параметры веб-службы указываются в разделе **globalParameters** действия JSON, как показано ниже.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> В параметрах веб-службы учитывается регистр, поэтому убедитесь, что имена, которые указываются в действии JSON, соответствуют именам, предоставляемым веб-службой.
>

После завершения повторного обучения обновите веб-службу оценки (прогнозный эксперимент, представленный в виде веб-службы) с использованием новой обученной модели с помощью **действия ресурса обновления машинное обучение Azure Studio (классическая модель)** . Дополнительные сведения см. в статье [Updating Azure Machine Learning models using Update Resource Activity](update-machine-learning-models.md) (Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс").

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами:

* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Действие Pig](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Действие Spark](transform-data-using-spark.md)
* [Настраиваемое действие .NET](transform-data-using-dotnet-custom-activity.md)
* [Действие хранимой процедуры](transform-data-using-stored-procedure.md)
