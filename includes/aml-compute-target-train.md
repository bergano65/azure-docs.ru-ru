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
ms.openlocfilehash: 1b62d4b2ac1bb69e2270c3202d29eb595df7aac4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806026"
---
**Вычислить целевые объекты можно повторно использовать из одного задания обучения к следующему**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.

|Обучение &nbsp;целевых объектов| Поддержка GPU |[Автоматические машинного Обучения](../articles/machine-learning/service/concept-automated-ml.md) | [Конвейеры машинного Обучения](../articles/machine-learning/service/concept-ml-pipelines.md) | [Графический интерфейс](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Возможно | Да | &nbsp; | &nbsp; |
|[Вычислительная среда Машинного обучения Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Да | Да & <br/>гиперпараметров&nbsp;помощник по настройке | Да | Да |
|[Удаленная виртуальная машина](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Да | Да & <br/>Настройка гиперпараметров | Да | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Да | Да | &nbsp; |
|[Аналитика озера данных Azure](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Да | &nbsp; |
