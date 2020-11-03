---
title: Служба "Apache HBase" не отвечает на запросы в Azure HDInsight
description: Устранение проблемы с помощью Apache HBase RESTFUL, не отвечающего на запросы в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: c4a0ef82b951fa43eb4c58050d3148fd2d695026
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286975"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Сценарий: остальные компоненты Apache HBase не отвечают на запросы в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Служба RESTFUL Apache HBase не реагирует на запросы в Azure HDInsight.

## <a name="cause"></a>Причина

Возможно, это может быть вызвано тем, что служба RESTFUL Apache HBase имеет утечку сокетов, что особенно распространено, когда служба работает в течение длительного времени (например, месяцев). В клиентском пакете SDK может появиться сообщение об ошибке следующего вида:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Решение

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

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]