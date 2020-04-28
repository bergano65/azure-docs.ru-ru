---
title: Низкая производительность в Apache Hive запросов LLAP в Azure HDInsight
description: Запросы в Apache Hive LLAP выполняются медленнее, чем ожидалось в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895122"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Сценарий: низкая производительность в Apache Hive запросов LLAP в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Конфигурации кластера по умолчанию недостаточно настроены для рабочей нагрузки. Запросы в Hive LLAP выполняются медленнее, чем ожидалось.

## <a name="cause"></a>Причина:

Это может быть вызвано разными причинами.

## <a name="resolution"></a>Разрешение

LLAP оптимизирован для запросов, использующих операции объединения и агрегирования. Запросы, подобные приведенным ниже, не работают в кластере интерактивных Hive.

```
select * from table where column = "columnvalue"
```

Чтобы улучшить производительность запросов к точкам в Hive LLAP, задайте следующие конфигурации:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Кроме того, можно увеличить использование кэша LLAP, чтобы повысить производительность с помощью следующих изменений конфигурации:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
