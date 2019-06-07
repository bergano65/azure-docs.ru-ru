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
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752948"
---
|Обучение &nbsp;целевых объектов| Поддержка GPU |[Автоматические машинного Обучения](../articles/machine-learning/service/concept-automated-ml.md) | [Конвейеры машинного Обучения](../articles/machine-learning/service/concept-ml-pipelines.md) | [Графический интерфейс](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Локальный компьютер](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Возможно | Да | &nbsp; | &nbsp; |
|[Вычислительная среда Машинного обучения Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Да | Да & <br/>гиперпараметров&nbsp;помощник по настройке | Да | Да |
|[Удаленная виртуальная машина](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Да | Да & <br/>Настройка гиперпараметров | Да | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Да | Да | &nbsp; |
|[Аналитика озера данных Azure](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Да | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Да | &nbsp; |
|[Пакетная служба Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Да | &nbsp; |

**Все целевые объекты вычислений могут использоваться повторно для нескольких заданий обучения**. Например, после присоединения удаленной виртуальной машины к рабочей области ее можно повторно использовать для нескольких заданий.