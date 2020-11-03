---
title: Медленное представление Apache Ambari Tez в Azure HDInsight
description: Представление Apache Ambari Tez может загружаться медленно или вообще не загружаться в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 0e294566da4c6f514704abc2ac014b8345020b5a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288839"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Сценарий: медленное представление Apache Ambari Tez в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Представление Apache Ambari Tez может загружаться медленно или вообще не загружаться. При загрузке Ambari Tez View можно увидеть, что процессы в головных узлах не отвечают.

## <a name="cause"></a>Причина

Иногда при работе с кластерами, созданными до октября 2017 в кластере с большим количеством заданий Hive, доступ к API-интерфейсам Yarn ATS может снизить производительность.

## <a name="resolution"></a>Решение

Это проблема, которая исправлена в Oct 2017. Повторное создание кластера сделает невозможной повторный запуск этой проблемы.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]