---
title: Ошибка интерпретатора Apache Hive Zeppelin — Azure HDInsight
description: Интерпретатор JDBC Zeppelin Hive Apache указывает на неправильный URL-адрес в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: dd5a45cb885d800d9922e7c8967f20b63b92be60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494174"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Сценарий: Apache Hive интерпретатор Zeppelin дает ошибку Zookeeper в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

В кластере Apache Hive LLAP интерпретатор Zeppelin выдает следующее сообщение об ошибке при попытке выполнить запрос:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Причина:

Интерпретатор JDBC Zeppelin Hive указывает на неверный URL-адрес.

## <a name="resolution"></a>Разрешение

1. Перейдите к сводке по компоненту Hive и скопируйте в буфер обмена URL-адрес Hive JDBC.

1. Перейдите на страницу `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`.

1. Измените параметры JDBC: измените значение Hive. URL на URL-адрес JDBC для Hive, скопированный на шаге 1.

1. Сохраните, а затем повторите запрос

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. в статье [Как создать запрос на получение поддержки Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к поддержке в рамках управления подпиской и выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через тот или иной [план поддержки Azure](https://azure.microsoft.com/support/plans/).
