---
title: Создание прогностических конвейеров данных
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029995"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Текущая версия](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) позволяет создавать, тестировать и развертывать решения для прогнозной аналитики. Этот процесс можно обобщить до трех этапов.

1. **Создайте обучаемый эксперимент.** Вы делаете этот шаг с помощью Azure Machine Learning Studio (классический). Azure Machine Learning Studio (классическая) — это среда совместной визуальной разработки, используемая для обучения и тестирования модели прогностической аналитики с использованием обучающих данных.
2. **Преобразуйте его в прогностический эксперимент.** Когда модель будет обучена с помощью существующих данных и готова для оценки новых данных, следует подготовить и оптимизировать эксперимент для оценки.
3. **Развертывание эксперимента в виде веб-службы**. Оценивающий эксперимент можно опубликовать в виде веб-службы Azure. С помощью конечной точки этой веб-службы вы можете отправлять данные в свою модель и получать из нее результаты прогнозов.

### <a name="data-factory-and-machine-learning-together"></a>Фабрика данных и машинное обучение вместе
Фабрика данных Azure позволяет легко создавать конвейеры, которые используют опубликованный веб-сервис [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) для прогностической аналитики. Используя **действие пакета исполнения** в конвейере Azure Data Factory, можно вызвать веб-сервис Azure Machine Learning Studio (классический) для прогнозирования данных в пакете.

Со временем прогностические модели в Azure Machine Learning Studio (классические) скоринговые эксперименты должны быть переподготовлены с помощью новых наборов входных данных. Чтобы повторно обучить модель из конвейера Фабрики данных, выполните следующие действия:

1. Опубликуйте обучающий (не прогнозный) эксперимент в виде веб-службы. Вы делаете этот шаг в Студии машинного обучения Azure (классический), как вы сделали, чтобы разоблачить прогностический эксперимент в качестве веб-службы в предыдущем сценарии.
2. Используйте Azure Machine Learning Studio (классическая) активность выполнения пакетов, чтобы вызвать веб-службу для учебного эксперимента. В принципе, вы можете использовать Azure Machine Learning Studio (классический) Пакет исполнения деятельности для вызывать как обучение веб-службы и скоринга веб-службы.

После того как вы закончите с переподготовкой, обновить скоринг веб-службы (прогнозный эксперимент подвергается как веб-сервис) с недавно обученной модели с помощью **Azure Machine Learning Studio (классический) Обновление ресурсной активности**. Дополнительные сведения см. в статье [Updating Azure Machine Learning models using Update Resource Activity](update-machine-learning-models.md) (Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс").

## <a name="azure-machine-learning-linked-service"></a>Связанная служба Машинного обучения Azure

Создайте связанную службу **Машинного обучения Azure**, чтобы связывать веб-службы Машинного обучения Azure с фабрикой данных Azure. Связанная служба используется в Azure Machine Learning Batch Execution Activity and [Update Resource Activity.](update-machine-learning-models.md)

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

| Свойство          | Описание                              | Обязательно |
| :---------------- | :--------------------------------------- | :------- |
| name              | Имя действия в конвейере.     | Да      |
| description       | Описание действия.  | нет       |
| type              | Для деятельности Data Lake Analytics U-S'L тип омование — **AzureMLBatchExecution.** | Да      |
| linkedServiceName | Связанные службы для связанной службы Машинного обучения Azure. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md). | Да      |
| webServiceInputs  | Пары "ключ —значение", сопоставляющие имена входных данных веб-службы Машинного обучения Azure. Ключ должен соответствовать входным параметрам, определенным в опубликованной веб-службе Машинного обучения Azure. Значение — это пара свойств связанных служб хранилища Azure и FilePath, указывающих на расположения входных больших двоичных объектов. | нет       |
| webServiceOutputs | Пары "ключ —значение", сопоставляющие имена выходных данных веб-службы Машинного обучения Azure. Ключ должен соответствовать выходным параметрам, определенным в опубликованной веб-службе Машинного обучения Azure. Значением является пара свойств связанных служб хранилища Azure и FilePath, указывающих на расположения выходных больших двоичных объектов. | нет       |
| globalParameters  | Пары ключей и значений, которые будут переданы в конечную точку службы машинного обучения Azure (классическая) служба исполнения пакетов. Ключи должны соответствовать названиям параметров веб-сервиса, определенных в опубликованной web-службе Azure Machine Learning Studio (классический). Значения передаются в свойстве GlobalParameters запроса на выполнение пакетов Azure Machine Learning Studio (классический) | нет       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Сценарий 1. Эксперименты с вводом-выводом веб-службы, ссылающейся на данные в хранилище BLOB-объектов

В этом сценарии веб-служба Машинного обучения Azure делает прогнозы с использованием данных из файла в хранилище больших двоичных объектов Azure и сохраняет результаты прогнозирования в хранилище больших двоичных объектов. Следующий код JSON определяет конвейер фабрики данных действием AzureMLBatchExecution. Входные и выходные данные в хранилище BLOB-объектов Azure указываются с помощью пары LinkedName и FilePath. В примере Linked Service входов и выходов различны, вы можете использовать различные связанные службы для каждого из ваших входов / выходов для Data Factory, чтобы иметь возможность подобрать нужные файлы и отправить в Azure Machine Learning Studio (классический) Web Service.

> [!IMPORTANT]
> В эксперименте Azure Machine Learning Studio (классический) ввода веб-службы и выходные порты, а также глобальные параметры имеют имена по умолчанию ("ввод1", "ввод2"), которые можно настроить. Имена, используемые для параметров webServiceInputs, webServiceOutputs и globalParameters, должны точно совпадать с именами в экспериментах. Вы можете просмотреть полезную нагрузку запроса образца на странице справки о выполнении пакета для вашей (классической) конечной точки Azure Machine Learning Studio (классическая) для проверки ожидаемого отображения.
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
Другим распространенным сценарием при создании экспериментов Azure Machine Learning Studio (классических) является использование модулей импортных данных и выходных данных. Модуль импорта используется для загрузки данных в эксперимент, а модуль вывода — для сохранения данных экспериментов. Дополнительные сведения о модулях импорта и вывода данных см. в [этом](https://msdn.microsoft.com/library/azure/dn905997.aspx) и в [этом](https://msdn.microsoft.com/library/azure/dn905984.aspx) разделах библиотеки MSDN.

При использовании модулей импорта и вывода данных рекомендуется указывать параметр веб-службы для каждого их свойства. Эти параметры веб-службы позволяют настроить значения во время выполнения. Например, можно создать эксперимент с модулем импорта, который использует базу данных SQL Azure: XXX.database.windows.net. После развертывания веб-службы вам необходимо включить объекты-получатели веб-службы, чтобы указать другой сервер Azure SQL `YYY.database.windows.net`. Вы можете использовать параметр веб-службы, чтобы разрешить настройку этого значения.

> [!NOTE]
> Входные и выходные данные веб-службы отличаются от параметров веб-службы. В первом сценарии вы видели, как можно указать вход ные и выходные данные для веб-сервиса Azure Machine Learning Studio (классический). В этом сценарии вы передаете параметры для веб-службы, соответствующие свойствам модулей импорта и вывода данных.
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

После того как вы закончите с переподготовкой, обновить скоринг веб-службы (прогнозный эксперимент подвергается как веб-сервис) с недавно обученной модели с помощью **Azure Machine Learning Studio (классический) Обновление ресурсной активности**. Дополнительные сведения см. в статье [Updating Azure Machine Learning models using Update Resource Activity](update-machine-learning-models.md) (Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс").

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами:

* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Свинья деятельность](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Активность искры](transform-data-using-spark.md)
* [пользовательская деятельность .NET](transform-data-using-dotnet-custom-activity.md)
* [Сохраненная процедура деятельности](transform-data-using-stored-procedure.md)
