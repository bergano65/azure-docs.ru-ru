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
ms.date: 12/04/2019
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77155974"
---
**Целевые объекты вычислений можно повторно использовать из одного учебного задания к другому**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.  Для конвейеров машинного обучения используйте соответствующий [Шаг конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) для каждого целевого объекта вычислений.

|Цели обучения &nbsp;|[Автоматический ML](../articles/machine-learning/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/concept-ml-pipelines.md) | [Конструктор Машинного обучения Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/how-to-set-up-training-targets.md#local)| да | &nbsp; | &nbsp; |
|[Кластер Машинное обучение Azure COMPUTE](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Да & <br/>Настройка&nbsp;параметров | да | да |
|[Удаленная виртуальная машина](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Да & <br/>Настройка гиперпараметров | да | &nbsp; |
|[Azure&nbsp;ные кирпичи](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Да (только в локальном режиме SDK) | да | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | да | &nbsp; |
