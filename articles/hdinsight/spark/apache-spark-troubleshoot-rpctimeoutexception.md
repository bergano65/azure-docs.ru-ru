---
title: RpcTimeoutИсключение для бережливости Apache Spark - Azure HDInsight
description: При обработке больших наборов данных с помощью бережливого сервера Apache Spark вы видите 502 ошибки
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894284"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Сценарий: RpcTimeoutИсключение для бережливого сервера Apache Spark в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Приложение Spark выходит `org.apache.spark.rpc.RpcTimeoutException` из строя `Futures timed out`за исключением и сообщением: , как в следующем примере:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`и `overhead limit exceeded` ошибки могут `sparkthriftdriver.log` также отображаться в следующем примере:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Причина

Эти ошибки вызваны нехваткой ресурсов памяти при обработке данных. Если начинается процесс сбора мусора Java, это может привести к повешению приложения Spark. Запросы начнут тайм-аут и остановки обработки. Ошибка `Futures timed out` указывает на кластер в условиях сильного стресса.

## <a name="resolution"></a>Решение

Увеличьте размер кластера, добавив больше узлов работы или увеличив емкость памяти существующих кластерных узлов. Можно также настроить конвейер данных, чтобы уменьшить объем обрабатываемых данных одновременно.

Контроль `spark.network.timeout` тайм-аута для всех сетевых подключений. Увеличение тайм-аута сети может позволить больше времени для некоторых критических операций, чтобы закончить, но это не решит проблему полностью.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
