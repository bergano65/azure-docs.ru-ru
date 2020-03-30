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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77155974"
---
**Вычислительные цели могут быть повторно использованы от одной учебной работы к другой.** Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.  Для конвейеров машинного обучения используйте соответствующий [шаг конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) для каждой вычислительной цели.

|Цели &nbsp;обучения|[Автоматическое Машинное обучение](../articles/machine-learning/concept-automated-ml.md) | [Трубопроводы ML](../articles/machine-learning/concept-ml-pipelines.md) | [Конструктор Машинного обучения Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/how-to-set-up-training-targets.md#local)| да | &nbsp; | &nbsp; |
|[Вычислительный кластер Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Да & <br/>гиперпараметр&nbsp;наяпритование | да | да |
|[Удаленный VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Да & <br/>Настройка гиперпараметров | да | &nbsp; |
|[Лазурные&nbsp;databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| да (только в локальном режиме SDK) | да | &nbsp; |
|[Аналитика озер данных Azure](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | да | &nbsp; |
