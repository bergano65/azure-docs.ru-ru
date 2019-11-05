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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489585"
---
**Целевые объекты вычислений можно повторно использовать из одного учебного задания к другому**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.

|Цели обучения &nbsp;| Поддержка GPU |[Автоматический ML](../articles/machine-learning/service/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/service/concept-ml-pipelines.md) | [Конструктор Машинное обучение Azure](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Ну | Да | &nbsp; | &nbsp; |
|[Машинное обучение Azure вычислительный экземпляр](../articles/machine-learning/service/concept-compute-instance.md)| Да | | Да |  |
|[Кластер Машинное обучение Azure COMPUTE](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Да | Да & <br/>Настройка&nbsp;параметров | Да | Да |
|[Удаленная виртуальная машина](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Да | Да & <br/>Настройка гиперпараметров | Да | &nbsp; |
|[Azure&nbsp;ные кирпичи](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Да | Да | &nbsp; |
|[Аналитика озера данных Azure](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Да | &nbsp; |
