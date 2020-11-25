---
title: Не удалось добавить узлы в кластер Azure HDInsight
description: Устранение неполадок, почему не удается добавить узлы в кластер Apache Hadoop в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014675"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Сценарий: не удается добавить узлы в кластер Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Не удалось добавить узлы в кластер Azure HDInsight.

## <a name="cause"></a>Причина:

Причины могут отличаться.

## <a name="resolution"></a>Разрешение

Используя функцию [Размер кластера](../hdinsight-scaling-best-practices.md) , вычислите количество дополнительных ядер, необходимых для кластера. Количество зависит от общего количества ядер в новых рабочих узлах. Затем выполните одно или несколько из следующих действий.

* Проверьте наличие доступных ядер в расположении кластера.

* Узнайте, сколько ядер доступно в других расположениях. Оцените целесообразность повторного создания кластера в другом расположении с достаточным количеством доступных ядер.

* Если вы хотите увеличить количество ядер в определенном расположении, отправьте запрос в службу поддержки на увеличение квоты для HDInsight.

## <a name="next-steps"></a>Следующие шаги

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]