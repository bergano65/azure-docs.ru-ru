---
title: Невозможно добавить узлы в кластер Azure HDInsight
description: Устранение неполадок, почему не удалось добавить узлы в кластер Apache Hadoop в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894095"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Сценарий: Невозможно добавить узлы в кластер Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Невозможно добавить узлы в кластер Azure HDInsight.

## <a name="cause"></a>Причина

Причины могут варьироваться.

## <a name="resolution"></a>Решение

Используя функцию [размера кластера,](../hdinsight-scaling-best-practices.md) вычислите количество дополнительных ядер, необходимых для кластера. Количество зависит от общего количества ядер в новых рабочих узлах. Затем попробуйте один или несколько из следующих шагов:

* Проверьте, есть ли в расположении кластера какие-либо ядра.

* Узнайте, сколько ядер доступно в других расположениях. Оцените целесообразность повторного создания кластера в другом расположении с достаточным количеством доступных ядер.

* Если вы хотите увеличить количество ядер в определенном расположении, отправьте запрос в службу поддержки на увеличение квоты для HDInsight.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
