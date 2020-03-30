---
title: Выполнение конвейеров машинного обучения Azure
description: Узнайте, как запустить конвейеры Машинного обучения Azure в конвейерах Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: b54504cf8ca7b32bf14bd4b7e0c561ffd56d4098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155169"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Выполнение конвейеров машинного обучения Azure на фабрике данных Azure

Запустите конвейеры машинного обучения Azure в качестве шага в конвейерах Фабрики данных Azure. Активность Machine Learning Execute Pipeline позволяет сценарии прогнозирования пакетов, такие как определение возможных обязательств по кредиту, определение настроений и анализ моделей поведения клиентов.

Ниже видео имеет шесть минут введения и демонстрации этой функции.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Свойства типа

Свойство | Описание | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
name | Имя действия в конвейере. | Строка | Да
type | Тип деятельности : 'AzureMLExecutePipeline' | Строка | Да
linkedServiceName | Связанные службы к машинному обучению Azure | Ссылка на связанную службу | Да
mlPipelineId | Идентификатор опубликованного конвейера машинного обучения Azure | Строка (или выражение с типом результата "строка") | Да
экспериментИмя | Выполнить название эксперимента по истории запуска конвейера Machine Learning | Строка (или выражение с типом результата "строка") | нет
mlPipeline Параметры | Пары ключей и значений, которые должны быть переданы в опубликованную конечную точку конвейера Azure Machine Learning. Ключи должны соответствовать названиям параметров конвейера, определенных в опубликованном конвейере машинного обучения | Объект с парами ключевых значений (или Выражение с объектом resultType) | нет
mlParentRunId | Идентификатор родительского машинного обучения Azure | Строка (или выражение с типом результата "строка") | нет
continueOnStepfailure | Продолжать ли выполнение других шагов в конвейере машинного обучения, если шаг не удается | Логическое | нет

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами:

* [Выполнение действия потока данных](control-flow-execute-data-flow-activity.md)
* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Свинья деятельность](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Активность искры](transform-data-using-spark.md)
* [пользовательская деятельность .NET](transform-data-using-dotnet-custom-activity.md)
* [Сохраненная процедура деятельности](transform-data-using-stored-procedure.md)
