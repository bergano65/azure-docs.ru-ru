---
title: Использование Apache Ambari в Azure HDInsight
description: Обсуждение того, как Apache Ambari используется в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067400"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Использование Apache Ambari в Azure HDInsight

HDInsight использует Apache Ambari для развертывания кластеров и управления. Агенты Амбари работают на каждом уде (головной узла, рабочий узла, зоозащитника и кромнода, если он существует). Сервер Ambari работает только на headnode (hn0 или hn1). Только один экземпляр сервера Ambari должен работать одновременно. Это контролируется контроллером сбоя HDInsight. Когда один из headnodes не для перезагрузки или обслуживания, другой headnode станет активным и Ambari сервер амбари на втором headnode будет запущен.

Вся конфигурация кластера должна быть выполнена через [uI Ambari,](./hdinsight-hadoop-manage-ambari.md)любое локальное изменение будет перезаписано при перезапущени узлов.

## <a name="failover-controller-services"></a>Службы контроллера Failover

Контроллер сбоя HDInsight также отвечает за обновление IP-адреса хоста headnode, который указывает на текущий активный головной узлы. Все агенты Ambari настроены, чтобы сообщить о своем состоянии и сердцебиении хедноду-хозяину. Контроллер failover представляет собой набор служб, работающих на каждом узеле в кластере, если они не работают, headnode failover может работать неправильно, и вы в конечном итоге с HTTP 502 при попытке получить доступ к серверу Ambari.

Чтобы проверить, какой headnode активен, один из способов состоит в `ping headnodehost` том, чтобы ssh к одному из узлов в кластере, а затем запустить и сравнить IP с двумя headnodes.

Если службы контроллера сбоя не работают, сбой headnode может произойти неправильно, что может привести к отказу от запуска сервера Ambari. Чтобы проверить, работают ли службы контроллера сбоя, выполните:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Журналы

На активном головном уде вы можете проверить журналы сервера Ambari по адресу:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

На любом узле в кластере вы можете проверить журналы агента Ambari по адресу:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Последовательности запуска службы

Это последовательность запуска службы во время загрузки:

1. Hdinsight-агент запускает службы контроллера сбоя.
1. Службы контроллера Failover запускают агента Ambari на каждом узлах и сервере Ambari на активном головном уде.

## <a name="ambari-database"></a>База данных Амбари

HDInsight создает базу данных S'L Azure под капотом, чтобы служить базой данных для сервера Ambari. Уровень обслуживания по умолчанию [— S0.](../sql-database/sql-database-elastic-pool-scale.md)

Для любого кластера с числом узлов рабочего уровня, превышающего 16 при создании кластера, S2 является уровнем службы баз данных.

## <a name="takeaway-points"></a>Очки на вынос

Никогда вручную не запускайте/не останавливайте службы ambari-server или ambari-agent, если только вы не пытаетесь перезапустить службу для работы над проблемой. Чтобы заставить failover, вы можете перезагрузить активный headnode.

Никогда вручную не изменяйте файлы конфигурации на любом кластерном узеле, пусть ambari UI сделает работу за вас.

## <a name="next-steps"></a>Дальнейшие действия

* [Управление кластерами HDInsight с помощью веб-интерфейса Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Управление кластерами HDInsight с помощью Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
