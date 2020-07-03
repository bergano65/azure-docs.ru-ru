---
title: Apache Spark приложение потоковой передачи остановлено через 24 дня в Azure HDInsight
description: Приложение потоковой передачи Apache Spark останавливается после выполнения в течение 24 дней, и в файлах журнала ошибок нет.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894439"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Сценарий: Apache Spark приложение потоковой передачи останавливается после выполнения в течение 24 дней в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Приложение потоковой передачи Apache Spark останавливается после выполнения в течение 24 дней, и в файлах журнала ошибок нет.

## <a name="cause"></a>Причина:

`livy.server.session.timeout` Значение определяет, сколько времени Apache Livy должен ожидать завершения сеанса. Когда длина сеанса достигнет `session.timeout` значения, сеанс Livy и приложение будут автоматически уничтожены.

## <a name="resolution"></a>Разрешение

Для длительных заданий увеличьте значение `livy.server.session.timeout` с помощью пользовательского интерфейса Ambari. Вы можете получить доступ к конфигурации Livy из пользовательского интерфейса Ambari, используя `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`URL-адрес.

Замените `<yourclustername>` именем кластера HDInsight, как показано на портале.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
