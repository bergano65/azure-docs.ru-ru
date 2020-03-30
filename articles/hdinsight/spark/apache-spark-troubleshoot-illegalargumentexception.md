---
title: Ошибка illegalArgumentException для Apache Spark - Azure HDInsight
description: НезаконныйАргументИсключение для деятельности Apache Spark в Azure HDInsight для фабрики данных Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894384"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Сценарий: НезаконныйАргументИсключение для деятельности Apache Spark в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При попытке выполнения действия Spark в конвейере Azure Data Factory необходимо следующее исключение:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Причина

Задание Spark выйдет из строя, если файл банки приложения не находится в по умолчанию/первичном хранилище кластера Spark.

Это известная проблема с платформой с открытым исходным кодом Spark, отслеживаемым в этой ошибке: [работа Spark выходит из строя, если fs.defaultFS и банку приложений отличаются URL.](https://issues.apache.org/jira/browse/SPARK-22587)

Эта проблема была решена в Spark 2.3.0.

## <a name="resolution"></a>Решение

Убедитесь, что банку приложения хранится в хранилище по умолчанию/первичной для кластера HDInsight. В случае с Фабрикой данных Azure убедитесь, что связанная служба ADF указывается на контейнер по умолчанию HDInsight, а не на вторичный контейнер.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
