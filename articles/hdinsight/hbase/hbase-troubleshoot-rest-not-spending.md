---
title: Служба "Apache HBase" не отвечает на запросы в Azure HDInsight
description: Устранение проблемы с HDInsight HBase, не отвечающим на запросы
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 7219f66457e47bba34e750ec74810b8d2edee36e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817112"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Сценарий: Служба "Apache HBase" не отвечает на запросы в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

Служба RESTFUL Apache HBase не реагирует на запросы в Azure HDInsight.

## <a name="cause"></a>Причина:

Возможно, это может быть вызвано тем, что служба RESTFUL Apache HBase имеет утечку сокетов, что особенно распространено, когда служба работает в течение длительного времени (например, месяцев). В клиентском пакете SDK может появиться сообщение об ошибке следующего вида:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Разрешение

Перезапустите службу HBase RESTFUL, выполнив следующую команду после SSH на узле. Вы также можете использовать действия сценария, чтобы перезапустить эту службу на всех рабочих узлах:

```bash
sudo service hdinsight-hbrest restart
```

Эта команда останавливает сервер региона HBase на том же узле. Вы можете вручную запустить сервер региона HBase через Ambari или разрешить функции автоматического перезапуска Ambari автоматически восстановить сервер региона HBase.

Если проблема сохраняется, можно установить следующий сценарий устранения рисков в качестве задания CRON, которое выполняется каждые 5 минут на каждом узле рабочей роли. Этот сценарий устранения проблем проверяет связь со службой RESTFUL и перезапускает ее в случае, если служба RESTFUL не отвечает.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
