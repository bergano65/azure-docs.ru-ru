---
title: Сервер Apache зоопарк не может сформировать кворум в Azure HDInsight
description: Сервер Apache зоопарк не может сформировать кворум в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250242"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Сервер Apache зоопарк не может сформировать кворум в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Сервер Apache zooKeeper неработоспособен, симптомы могут включать: оба ресурса менеджеров / имя `zkFailoverController` узлов находятся в режиме ожидания, простые операции HDFS не работают, остановлены и не могут быть запущены, Yarn / Spark / Livy рабочих мест неудачу из-за ошибок зоозащитника. LLAP Daemons также может не начаться на кластерах Secure Spark или Interactive Hive. Вы можете увидеть сообщение об ошибке, похожее на:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

В журнале сервера зоозащитника на любом хозяине зоозащитника на /var/log/zookeeper/zookeeper-zookeeper-зоозащитник-сервер-\*.out, вы можете также увидеть следующую ошибку:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Причина

Когда объем файлов моментальных снимков большой или файлы моментального снимка повреждены, сервер зооkeeper не сможет сформировать кворум, что приводит к неработоспособным службам, связанным с зоозащитником. Сервер зоохранителя не будет удалять старые файлы моментального снимка из каталога данных, вместо этого, это периодическая задача, которая должна быть выполнена пользователями для поддержания работоспособности зоозащитника. Для получения дополнительной информации, [см.](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)

## <a name="resolution"></a>Решение

Проверьте каталог данных зоопарка `/hadoop/zookeeper/version-2` и `/hadoop/hdinsight-zookeeper/version-2` узнайте, большой ли размер файла снимков. При наличии больших моментальных снимков, при наличии больших моментальных снимков:

1. Резервное копирование `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`снимков в и .

1. Очистка снимков `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`в и .

1. Перезапустите все серверы зоопарка с uI Apache Ambari.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

- Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

- Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
