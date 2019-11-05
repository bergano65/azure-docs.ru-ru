---
title: Выполнение конвейеров Машинное обучение Azure в конвейерах фабрики данных Azure | Документация Майкрософт
description: Узнайте, как выполнять конвейеры Машинное обучение Azure в конвейерах фабрики данных Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: c05a0fde7019d9162a051a04696c2251301d70dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490600"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Выполнение конвейеров Машинное обучение Azure в фабрике данных Azure

Запустите конвейеры Машинное обучение Azure в качестве шага в конвейерах фабрики данных Azure. Действие Машинное обучение выполнение конвейера позволяет выполнять сценарии прогнозирования пакетной обработки, такие как определение возможных значений по умолчанию для ссуд, определение тональности и анализ шаблонов поведения клиентов.

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

Свойство | Description (Описание) | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
name | Имя действия в конвейере. | string | Да
type | Тип действия — "Азуремлексекутепипелине" | string | Да
linkedServiceName (имя связанной службы) | Связанная служба для Машинное обучение Azure | Ссылка на связанную службу | Да
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
