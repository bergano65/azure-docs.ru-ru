---
title: Рпктимеаутексцептион для Apache Spark Thrift — Azure HDInsight
description: При обработке больших наборов данных с помощью Apache Spark Thrift Server отображается ошибка 502
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: c9e71c745d62432af3c0fe035d28009e3e5be761
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241029"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Сценарий: Рпктимеаутексцептион для сервера Apache Spark Thrift в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Приложение Spark завершает работу с исключением `org.apache.spark.rpc.RpcTimeoutException` и сообщением: `Futures timed out`, как показано в следующем примере:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

ошибки `OutOfMemoryError` и `overhead limit exceeded` также могут появиться в `sparkthriftdriver.log`, как показано в следующем примере:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Причина:

Эти ошибки вызваны недостатком ресурсов памяти при обработке данных. Если запускается процесс сборки мусора Java, это может привести к зависанию приложения Spark. Запросы начнут истечет время ожидания и прерывать обработку. `Futures timed out` ошибка указывает на кластер в условиях сильной нагрузки.

## <a name="resolution"></a>Разрешение

Увеличьте размер кластера, добавив дополнительные рабочие узлы или увеличив объем памяти существующих узлов кластера. Можно также настроить конвейер данных, чтобы уменьшить объем обрабатываемых данных.

`spark.network.timeout` управляет временем ожидания для всех сетевых подключений. Увеличение времени ожидания сети может послужить большим временем для завершения некоторых критических операций, но это не позволит полностью устранить проблему.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
