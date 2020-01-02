---
title: включение файла
description: включение файла
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875462"
---
**Целевые объекты вычислений можно повторно использовать из одного учебного задания к другому**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.  Для конвейеров машинного обучения используйте соответствующий [Шаг конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) для каждого целевого объекта вычислений.

|Цели обучения &nbsp;|[Автоматический ML](../articles/machine-learning/service/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/service/concept-ml-pipelines.md) | [Конструктор Машинное обучение Azure](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Да | &nbsp; | &nbsp; |
|[Кластер Машинное обучение Azure COMPUTE](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Да & <br/>Настройка&nbsp;параметров | Да | Да |
|[Удаленная виртуальная машина](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | Да & <br/>Настройка гиперпараметров | Да | &nbsp; |
|[Azure&nbsp;ные кирпичи](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| Да | Да | &nbsp; |
|[Аналитика озера данных Azure](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | Да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | Да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | Да | &nbsp; |
