---
title: Управление дисковым пространством в Azure HDInsight
description: Устранение неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473016"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Управление дисковым пространством в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="hive-log-configurations"></a>Конфигурации журнала Hive

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net`перейдите к , где `CLUSTERNAME` имя вашего кластера.

1. Перейдите к **Hive** > **Configs** > **Расширенный** > **Расширенный улья-log4j**. Просмотрите следующие настройки:

    * `hive.root.logger=DEBUG,RFA`. Это значение по умолчанию, изменить `INFO` [уровень журнала](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) для печати меньше записей журналов.

    * `log4jhive.log.maxfilesize=1024MB`. Это значение по умолчанию, изменять по желанию.

    * `log4jhive.log.maxbackupindex=10`. Это значение по умолчанию, изменять по желанию. Если параметр был опущен, генерируемые файлы журнала будут бесконечными.

## <a name="yarn-log-configurations"></a>Конфигурации журналов пряжи

Просмотрите следующие конфигурации:

* Apache Ambari

    1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net`перейдите к , где `CLUSTERNAME` имя вашего кластера.

    1. Перейдите к **Hive** > **Configs** > **Advanced** > Resource**Manager.** Убедитесь, что **агрегация включения журнала** проверяется. При отключении узлы имен будут хранить журналы локально, а не агрегировать их в удаленном хранилище при завершении или завершении приложения.

* Убедитесь, что размер кластера соответствует рабочей нагрузке. Рабочая нагрузка могла измениться недавно или кластер мог быть изменен. [Масштабируйте](../hdinsight-scaling-best-practices.md) кластер, чтобы соответствовать более высокой рабочей нагрузке.

* `/mnt/resource`могут быть заполнены файлами осиротевших (как в случае перезагрузки менеджера ресурсов). При необходимости вручную очистите `/mnt/resource/hadoop/yarn/log` и `/mnt/resource/hadoop/yarn/local`.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
