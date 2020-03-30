---
title: Низкая производительность в запросах Apache Hive LLAP в Azure HDInsight
description: Запросы в Apache Hive LLAP исполняются медленнее, чем ожидалось в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895122"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Сценарий: Низкая производительность в запросах Apache Hive LLAP в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные разрешения проблем при использовании компонентов интерактивного запроса в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Конфигурации кластера по умолчанию недостаточно настроены для рабочей нагрузки. Запросы в Hive LLAP исполняются медленнее, чем ожидалось.

## <a name="cause"></a>Причина

Это может произойти по разным причинам.

## <a name="resolution"></a>Решение

LLAP оптимизирован для запросов, связанных с соединениями и агрегатами. Запросы, подобные следующим, не очень хорошо работают в кластере Interactive Hive:

```
select * from table where column = "columnvalue"
```

Для повышения производительности точечного запроса в Hive LLAP установите следующие конфигурации:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Вы также можете увеличить использование кэша LLAP для повышения производительности при следующем изменении конфигурации:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
