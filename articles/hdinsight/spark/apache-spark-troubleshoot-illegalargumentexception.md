---
title: Ошибка Иллегаларгументексцептион для Apache Spark — Azure HDInsight
description: Иллегаларгументексцептион для действия Apache Spark в Azure HDInsight для фабрики данных Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 334e8d129a7b5bb79c9e01d5ba7f347682e5bd79
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545622"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Сценарий: Иллегаларгументексцептион для действия Apache Spark в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы исправления проблем, возникающих при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При попытке выполнить действие Spark в конвейере фабрики данных Azure появляется следующее исключение:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Причина

Задание Spark завершится ошибкой, если JAR-файл приложения не размещается в хранилище по умолчанию (первичном) кластера Spark.

Это известная проблема с инфраструктурой открытого кода Spark, проданной в этой ошибке: [Задание Spark завершается ошибкой, если в FS. дефаултфс и JAR-файл хранятся разные URL](https://issues.apache.org/jira/browse/SPARK-22587).

Эта проблема решена в Spark 2.3.0.

## <a name="resolution"></a>Решение

Убедитесь, что JAR-файл приложения хранится в хранилище по умолчанию (первичном) для кластера HDInsight. В случае фабрики данных Azure убедитесь, что связанная служба ADF указывает на контейнер HDInsight по умолчанию, а не на дополнительный контейнер.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка** . Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).