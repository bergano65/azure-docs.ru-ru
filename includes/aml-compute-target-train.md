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
ms.openlocfilehash: 9a04bca11c6fca480ea1c9fd3d4a51956c1830de
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933122"
---
**Целевые объекты вычислений могут использоваться повторно для разных заданий обучения**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.  В конвейерах машинного обучения используйте правильный [шаг конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) для каждого целевого объекта вычислений.

|Целевые объекты &nbsp;обучения|[Автоматическое Машинное обучение](../articles/machine-learning/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/concept-ml-pipelines.md) | [Конструктор Машинного обучения Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/how-to-set-up-training-targets.md#local)| да | &nbsp; | &nbsp; |
|[Вычислительный кластер Машинного обучения Azure](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| да + <br/>настройка&nbsp;гиперпараметров | да | да |
|[Вычислительная операция Машинного обучения Azure](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | да + <br/>Настройка гиперпараметров | да |  |
|[Удаленная виртуальная машина](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | да + <br/>Настройка гиперпараметров | да | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-set-up-training-targets.md#databricks)| да (только в локальном режиме SDK) | да | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-set-up-training-targets.md#adla) | &nbsp; | да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | да | &nbsp; |
