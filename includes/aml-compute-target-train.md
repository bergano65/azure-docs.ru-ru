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
ms.openlocfilehash: 6ddff84de6b8ffd5bc8f7c7dcaa7cb4df3d71f81
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703192"
---
**Целевые объекты вычислений могут использоваться повторно для разных заданий обучения**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.  В конвейерах машинного обучения используйте правильный [шаг конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) для каждого целевого объекта вычислений.

|Целевые объекты &nbsp;обучения|[Автоматическое Машинное обучение](../articles/machine-learning/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/concept-ml-pipelines.md) | [Конструктор Машинного обучения Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/how-to-set-up-training-targets.md#local)| да | &nbsp; | &nbsp; |
|[Вычислительный кластер Машинного обучения Azure](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| да + <br/>настройка&nbsp;гиперпараметров | да | да |
|[Вычислительная операция Машинного обучения Azure](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | да + <br/>Настройка гиперпараметров | да |  |
|[Удаленная виртуальная машина](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | да + <br/>Настройка гиперпараметров | да | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| да (только в локальном режиме SDK) | да | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | да | &nbsp; |
