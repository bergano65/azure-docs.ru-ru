---
title: Сообщение об ошибке, не отображаемое в Apache Hive представлении — Azure HDInsight
description: Запрос завершается ошибкой в Apache Hive представлении без подробной информации об кластере Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 38dd064fe8365e6661ce7ea7e1077a4e1e32dbf5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494254"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Сценарий: сообщение об ошибке запроса не отображается в Apache Hive представлении в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Сообщение об ошибке запроса представления Apache Hive View будет выглядеть примерно так, без дополнительных сведений:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Причина:

Иногда сообщение об ошибке ошибки запроса может быть слишком большим для отображения на главной странице представления Hive.

## <a name="resolution"></a>Разрешение

Просмотрите вкладку уведомления в правом верхнем углу Hive_view, чтобы увидеть полный текст StackTrace и сообщение об ошибке.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. в статье [Как создать запрос на получение поддержки Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к поддержке в рамках управления подпиской и выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через тот или иной [план поддержки Azure](https://azure.microsoft.com/support/plans/).
