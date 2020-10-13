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
ms.date: 09/17/2020
ms.openlocfilehash: cdf2c3d1840f64b267f1aac1f8877d5f0bf76082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841966"
---
**Целевые объекты вычислений могут использоваться повторно для разных заданий обучения**. Например, после подключения удаленной виртуальной машины к вашей рабочей области ее можно многократно использовать для нескольких заданий. В конвейерах машинного обучения используйте правильный [шаг конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true) для каждого целевого объекта вычислений.

Для большинства заданий можно использовать любой из приведенных ниже ресурсов для обучающего целевого объекта вычислений. Не все ресурсы можно использовать для автоматизированного машинного обучения, конвейеров машинного обучения или конструктора.

|Целевые объекты &nbsp;обучения|[Автоматизированное машинное обучение](../articles/machine-learning/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/concept-ml-pipelines.md) | [Конструктор Машинного обучения Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Да | &nbsp; | &nbsp; |
|[Вычислительный кластер Машинного обучения Azure](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Да | Да | Да |
|[Вычислительная операция Машинного обучения Azure](../articles/machine-learning/how-to-create-manage-compute-instance.md) | да (с помощью пакета SDK)  | Да |  |
|[Удаленная виртуальная машина](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Да  | Да | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| да (только в локальном режиме SDK) | Да | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Да | &nbsp; |
