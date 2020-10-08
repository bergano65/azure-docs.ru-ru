---
title: включить файл
description: включить файл
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/19/2020
ms.openlocfilehash: bbd32f7e40e3039a69a95b4458de86ff35f26867
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91643137"
---
**Целевые объекты вычислений могут использоваться повторно для разных заданий обучения**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.  В конвейерах машинного обучения используйте правильный [шаг конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) для каждого целевого объекта вычислений.

|Целевые объекты &nbsp;обучения|[Автоматическое Машинное обучение](../articles/machine-learning/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/concept-ml-pipelines.md) | [Конструктор Машинного обучения Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| да | &nbsp; | &nbsp; |
|[Вычислительный кластер Машинного обучения Azure](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| да + <br/>настройка&nbsp;гиперпараметров | да | да |
|[Вычислительная операция Машинного обучения Azure](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | да (с помощью пакета SDK) | да |  |
|[Удаленная виртуальная машина](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | да + <br/>Настройка гиперпараметров | да | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| да (только в локальном режиме SDK) | да | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | да | &nbsp; |
