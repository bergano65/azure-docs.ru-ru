---
title: Фиксированный ЦП в кластере Apache HBase — Azure HDInsight
description: Устранение неполадок фиксированного ЦП на сервере регионов в кластере Apache HBase в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887314"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Сценарий: фиксированный ЦП на сервере региона в кластере Apache HBase в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Процесс сервера Apache HBase в регионе начинается с близкого к 200% ЦП, что приводит к срабатыванию предупреждений в HBase Master процессе и кластере для неполной работы.

## <a name="cause"></a>Причина

Если вы используете кластер HBase версии 3.4, возможно, возникла потенциальная ошибка, вызванная обновлением JDK до Version 1.7.0 _151. Симптомом, который мы видим, это начало процесса сервера региона, которое занимает до 200% ресурсов ЦП (для проверки выполнения команды `top`; если процесс, занимающийся близко к 200% ЦПУ, получит свой PID и подтвердит, что он является процессом сервера региона, выполнив `ps -aux | grep`).

## <a name="resolution"></a>Разрешение

1. Установите JDK 1,8 на всех узлах кластера, как показано ниже.

    * Запустите действие скрипта `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`. Не забудьте выбрать параметр для запуска на всех узлах.

    * Кроме того, можно войти в каждый отдельный узел и выполнить команду `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`.

1. Перейдите к пользовательскому интерфейсу Ambari — `https://<clusterdnsname>.azurehdinsight.net`.

1. Перейдите к **HBase-> configs — > Advanced-> advanced** `hbase-env configs` и измените переменную `JAVA_HOME` на `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Сохраните изменения конфигурации.

1. [Необязательно, но рекомендуется] [Очистить все таблицы в кластере](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. В пользовательском интерфейсе Ambari перезапустите все службы HBase, требующие перезагрузки.

1. В зависимости от данных в кластере для достижения стабильного состояния кластера может потребоваться от нескольких минут до часа. Способ подтверждения кластера в стабильном состоянии заключается в проверке пользовательского интерфейса HMaster (все серверы регионов должны быть активны) из Ambari (обновление) или из головного узла запустите оболочку HBase, а затем выполните команду status.

Чтобы убедиться, что обновление прошло успешно, убедитесь, что соответствующие процессы HBase запущены с использованием соответствующей версии Java — например, для сервера региона:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
