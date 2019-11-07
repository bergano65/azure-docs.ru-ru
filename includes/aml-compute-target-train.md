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
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580597"
---
**Целевые объекты вычислений можно повторно использовать из одного учебного задания к другому**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.

|Цели обучения &nbsp;| Поддержка GPU |[Автоматический ML](../articles/machine-learning/service/concept-automated-ml.md) | [Конвейеры машинного обучения](../articles/machine-learning/service/concept-ml-pipelines.md) | [Конструктор Машинное обучение Azure](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Ну | Да | &nbsp; | &nbsp; |
|[Кластер Машинное обучение Azure COMPUTE](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Да | Да & <br/>Настройка&nbsp;параметров | Да | Да |
|[Удаленная виртуальная машина](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Да | Да & <br/>Настройка гиперпараметров | Да | &nbsp; |
|[Azure&nbsp;ные кирпичи](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Да | Да | &nbsp; |
|[Аналитика озера данных Azure](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Да | &nbsp; |
