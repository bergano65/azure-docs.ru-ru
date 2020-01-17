---
title: Выполнение конвейеров Машинное обучение Azure
description: Узнайте, как выполнять конвейеры Машинное обучение Azure в конвейерах фабрики данных Azure.
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
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155169"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Выполнение конвейеров Машинное обучение Azure в фабрике данных Azure

Запустите конвейеры Машинное обучение Azure в качестве шага в конвейерах фабрики данных Azure. Действие Машинное обучение выполнение конвейера позволяет выполнять сценарии прогнозирования пакетной обработки, такие как определение возможных значений по умолчанию для ссуд, определение тональности и анализ шаблонов поведения клиентов.

В приведенном ниже видеоролике представлено представление и демонстрация этой функции в течение шести минут.

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

Свойство | Description | Допустимые значения | Обязательно для заполнения
-------- | ----------- | -------------- | --------
name | Имя действия в конвейере. | String | Да
type | Тип действия — "Азуремлексекутепипелине" | String | Да
linkedServiceName | Связанная служба для Машинное обучение Azure | Ссылка на связанную службу | Да
млпипелинеид | Идентификатор опубликованного конвейера Машинное обучение Azure | Строка (или выражение с типом результата "строка") | Да
експериментнаме | Имя эксперимента журнала выполнения Машинное обучение выполнения конвейера | Строка (или выражение с типом результата "строка") | Нет
млпипелинепараметерс | Пары "ключ — значение", которые будут переданы в опубликованную конечную точку конвейера Машинное обучение Azure. Ключи должны соответствовать именам параметров конвейера, определенных в опубликованном конвейере Машинное обучение | Объект с парами "ключ-значение" (или выражение с объектом resultType) | Нет
млпарентрунид | Идентификатор выполнения родительского Машинное обучение Azure конвейера | Строка (или выражение с типом результата "строка") | Нет
континуеонстепфаилуре | Следует ли продолжать выполнение других шагов в Машинное обучение выполнении конвейера в случае сбоя шага | Логическое | Нет

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами:

* [Выполнение действия потока данных](control-flow-execute-data-flow-activity.md)
* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Действие Pig](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Действие Spark](transform-data-using-spark.md)
* [Настраиваемое действие .NET](transform-data-using-dotnet-custom-activity.md)
* [Действие хранимой процедуры](transform-data-using-stored-procedure.md)
