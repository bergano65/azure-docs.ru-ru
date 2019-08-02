---
title: Apache Spark приложение потоковой передачи прекращает обработку данных после выполнения в течение 24 дней без известных ошибок в журналах в Azure HDInsight
description: Приложение потоковой передачи Apache Spark останавливается после выполнения в течение 24 дней, и в файлах журнала ошибок нет.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 002c45c514b5d8207a1aa70ec8e1c749677a239a
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620887"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Сценарий: Apache Spark приложение потоковой передачи останавливается после выполнения в течение 24 дней в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблемы

Приложение потоковой передачи Apache Spark останавливается после выполнения в течение 24 дней, и в файлах журнала ошибок нет.

## <a name="cause"></a>Причина:

`livy.server.session.timeout` Значение определяет, сколько времени Apache Livy должен ожидать завершения сеанса. Когда длина сеанса достигнет `session.timeout` значения, сеанс Livy и приложение будут автоматически уничтожены.

## <a name="resolution"></a>Разрешение

Для длительных заданий увеличьте значение `livy.server.session.timeout` с помощью пользовательского интерфейса Ambari. Вы можете получить доступ к конфигурации Livy из пользовательского интерфейса Ambari, используя `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`URL-адрес.

Замените `<yourclustername>` именем кластера HDInsight, как показано на портале.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
