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
ms.openlocfilehash: 54328c7ae4fe2112a718bd82b9f7330c9e95ec69
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146745"
---
**Целевые объекты вычислений могут использоваться повторно для разных заданий обучения**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.  В конвейерах машинного обучения используйте правильный [шаг конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) для каждого целевого объекта вычислений.

|Целевые объекты &nbsp;обучения|[Автоматическое Машинное обучение](../articles/machine-learning/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/concept-ml-pipelines.md) | [Конструктор Машинного обучения Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| да | &nbsp; | &nbsp; |
|[Вычислительный кластер Машинного обучения Azure](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| да + <br/>настройка&nbsp;гиперпараметров | да | да |
|[Вычислительная операция Машинного обучения Azure](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | да + <br/>Настройка гиперпараметров | да |  |
|[Удаленная виртуальная машина](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | да + <br/>Настройка гиперпараметров | да | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| да (только в локальном режиме SDK) | да | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | да | &nbsp; |
