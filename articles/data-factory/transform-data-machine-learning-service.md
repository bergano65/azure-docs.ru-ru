---
title: Выполнение конвейеров служб Машинное обучение Azure в конвейерах фабрики данных Azure | Документация Майкрософт
description: Узнайте, как выполнять конвейеры службы Машинное обучение Azure в конвейерах фабрики данных Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: ddaa020b3567f8f00cfd7fc7f7728f1160d9306b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301351"
---
# <a name="execute-azure-machine-learning-service-pipelines-in-azure-data-factory"></a>Выполнение конвейеров служб Машинное обучение Azure в фабрике данных Azure

Запустите конвейеры службы Машинное обучение Azure в качестве шага в конвейерах фабрики данных Azure. Действие Машинное обучение выполнение конвейера позволяет выполнять сценарии прогнозирования пакетной обработки, такие как определение возможных значений по умолчанию для ссуд, определение тональности и анализ шаблонов поведения клиентов.

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

Свойство | Описание | Допустимые значения | Обязательно для заполнения
-------- | ----------- | -------------- | --------
name | Имя действия в конвейере. | Строка, | Да
type | Тип действия — "Азуремлексекутепипелине" | Строка, | Да
linkedServiceName | Связанная служба со службой Машинное обучение Azure | Ссылка на связанную службу | Да
млпипелинеид | Идентификатор опубликованного конвейера Машинное обучение Azure | Строка (или выражение с типом результата "строка") | Да
експериментнаме | Имя эксперимента журнала выполнения Машинное обучение выполнения конвейера | Строка (или выражение с типом результата "строка") | Нет
млпипелинепараметерс | Пары "ключ — значение", которые будут переданы в опубликованную конечную точку конвейера Машинное обучение Azure. Ключи должны соответствовать именам параметров конвейера, определенных в опубликованном конвейере Машинное обучение | Объект с парами "ключ-значение" (или выражение с объектом resultType) | Нет
млпарентрунид | Родительский идентификатор запуска конвейера службы Машинное обучение Azure | Строка (или выражение с типом результата "строка") | Нет
континуеонстепфаилуре | Следует ли продолжать выполнение других шагов в Машинное обучение выполнении конвейера в случае сбоя шага | boolean | Нет

## <a name="next-steps"></a>Следующие шаги
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
