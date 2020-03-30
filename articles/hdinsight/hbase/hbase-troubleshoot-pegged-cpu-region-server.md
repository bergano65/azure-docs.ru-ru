---
title: Привязанный процессор в кластере Apache HBase - Azure HDInsight
description: Устранение неполадок привязало процессор к серверу региона в кластере Apache HBase в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887314"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Сценарий: Pegged процессор на сервере региона в кластере Apache HBase в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Процесс сервера Apache HBase начинает занимать около 200% процессора, в результате чего оповещения о работе на процессе и кластере HBase Master не функционируют на полную мощность.

## <a name="cause"></a>Причина

Если вы работаете с кластером HBase v3.4, возможно, вы пострадали от потенциальной ошибки, вызванной обновлением jdk до версии 1.7.0'151. Симптомом мы видим, является процесс сервера региона начинает занимать около 200% процессора (для проверки этого запуска `top` команды; если есть процесс, занимающий около `ps -aux | grep` 200% процессора получить его pid и подтвердить, что это процесс сервера региона путем запуска).

## <a name="resolution"></a>Решение

1. Установите jdk 1.8 на ВСЕХ узлах кластера, как унасижу:

    * Выполнить действие `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`скрипта . Обязательно выберите опцию для запуска на всех узлах.

    * Кроме того, вы можете войти в каждый `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`отдельный узла и запустить команду.

1. Перейти к Ambari `https://<clusterdnsname>.azurehdinsight.net`UI - .

1. Перейдите на **HBase->Configs->Advanced->Advanced** `hbase-env configs` и измените переменную `JAVA_HOME` на `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Сохранить изменение конфигурации.

1. (Необязательно, но рекомендуется) [Промыть все таблицы на кластере](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. С ambari UI снова перезапустите все службы HBase, которые нуждаются в перезапуске.

1. В зависимости от данных о кластере для достижения стабильного состояния кластеру может потребоваться от нескольких минут до часа. Способ подтверждения того, как кластер достигает стабильного состояния, либо проверяя uI HMaster (все серверы регионов должны быть активными) от Ambari (обновить), либо из оболочки HBase headnode, а затем запустить команду состояния.

Чтобы убедиться, что обновление было успешным, убедитесь, что соответствующие процессы HBase начинают использовать соответствующую версию Java - например, для проверки сервера региона как:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
