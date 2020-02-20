---
title: Управление дисковым пространством в Azure HDInsight
description: Действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77473016"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Управление дисковым пространством в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="hive-log-configurations"></a>Конфигурации журналов Hive

1. В веб-браузере перейдите к `https://CLUSTERNAME.azurehdinsight.net`, где `CLUSTERNAME` — имя кластера.

1. Перейдите в раздел > **конфигурации** **Hive** > **Расширенный** > **Расширенный Hive-log4j**. Проверьте следующие параметры:

    * `hive.root.logger=DEBUG,RFA`. Это значение по умолчанию. Измените [уровень ведения журнала](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) на `INFO`, чтобы напечатать меньше записей журналов.

    * `log4jhive.log.maxfilesize=1024MB`. Это значение по умолчанию, при необходимости измените.

    * `log4jhive.log.maxbackupindex=10`. Это значение по умолчанию, при необходимости измените. Если параметр не указан, создаваемые файлы журнала будут бесконечными.

## <a name="yarn-log-configurations"></a>Конфигурации журналов Yarn

Ознакомьтесь со следующими конфигурациями:

* Apache Ambari

    1. В веб-браузере перейдите к `https://CLUSTERNAME.azurehdinsight.net`, где `CLUSTERNAME` — имя кластера.

    1. Перейдите к разделу > **конфигурации** **Hive** > **Дополнительные** > **Диспетчер ресурсов**. Убедитесь, что установлен флажок **включить объединение журналов** . Если отключено, узлы имен будут хранить журналы локально, а не объединять их в удаленном хранилище при завершении или прекращении работы приложения.

* Убедитесь, что размер кластера соответствует рабочей нагрузке. Возможно, Рабочая нагрузка изменилась недавно или размер кластера был изменен. Увеличьте [масштаб](../hdinsight-scaling-best-practices.md) кластера в соответствии с более высокой рабочей нагрузкой.

* `/mnt/resource` могут быть заполнены потерянными файлами (как в случае перезапуска Resource Manager). При необходимости вручную очистите `/mnt/resource/hadoop/yarn/log` и `/mnt/resource/hadoop/yarn/local`.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
