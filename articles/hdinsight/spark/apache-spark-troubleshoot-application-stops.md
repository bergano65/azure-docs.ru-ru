---
title: Приложение Apache Spark Streaming останавливается через 24 дня в Azure HDInsight
description: Приложение Apache Spark Streaming останавливается после выполнения в течение 24 дней, и в файлах журнала нет ошибок.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894439"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Сценарий: Приложение Apache Spark Streaming останавливается после выполнения в течение 24 дней в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Приложение Apache Spark Streaming останавливается после выполнения в течение 24 дней, и в файлах журнала нет ошибок.

## <a name="cause"></a>Причина

Значение `livy.server.session.timeout` определяет, как долго Apache Livy должен ждать завершения сеанса. Как только длина `session.timeout` сеанса достигает значения, сеанс Livy и приложение автоматически убьют.

## <a name="resolution"></a>Решение

Для длительных `livy.server.session.timeout` рабочих мест увеличьте стоимость использования uI Ambari. Вы можете получить доступ к конфигурации Livy из `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`uI Ambari с помощью URL- данных.

Замените `<yourclustername>` имя кластера HDInsight, как показано на портале.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
