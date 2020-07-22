---
title: Служба "Apache HBase" не отвечает на запросы в Azure HDInsight
description: Устранение проблемы с помощью Apache HBase RESTFUL, не отвечающего на запросы в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887178"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Сценарий: остальные компоненты Apache HBase не отвечают на запросы в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

Служба RESTFUL Apache HBase не реагирует на запросы в Azure HDInsight.

## <a name="cause"></a>Причина:

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

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка**. Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
