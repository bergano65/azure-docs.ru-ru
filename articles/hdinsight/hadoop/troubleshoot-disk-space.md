---
title: Управление дисковым пространством в Azure HDInsight
description: Действия по устранению неполадок и возможные способы устранения проблем с дисковым пространством при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 0628033ecf6f2e51cf18f61c6e5b36042557dc7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530167"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Управление дисковым пространством в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="hive-log-configurations"></a>Конфигурации журналов Hive

1. В веб-браузере перейдите на страницу `https://CLUSTERNAME.azurehdinsight.net`, где `CLUSTERNAME` — это имя вашего кластера.

1. Последовательно выберите **Hive**  >  **настройки**Hive дополнительно  >  **Advanced**  >  **Hive-log4j**. Проверьте следующие параметры:

    * `hive.root.logger=DEBUG,RFA`. Это значение по умолчанию. Измените [уровень ведения журнала](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) на, `INFO` чтобы напечатать меньше записей журналов.

    * `log4jhive.log.maxfilesize=1024MB`. Это значение по умолчанию, при необходимости измените.

    * `log4jhive.log.maxbackupindex=10`. Это значение по умолчанию, при необходимости измените. Если параметр не указан, создаваемые файлы журнала будут бесконечными.

## <a name="yarn-log-configurations"></a>Конфигурации журналов Yarn

Ознакомьтесь со следующими конфигурациями:

* Apache Ambari

    1. В веб-браузере перейдите на страницу `https://CLUSTERNAME.azurehdinsight.net`, где `CLUSTERNAME` — это имя вашего кластера.

    1. Перейдите к **Hive**  >  **Configs**  >  **расширенным**  >  **Диспетчер ресурсов**config Hive. Убедитесь, что установлен флажок **включить объединение журналов** . Если отключено, узлы имен будут хранить журналы локально, а не объединять их в удаленном хранилище при завершении или прекращении работы приложения.

* Убедитесь, что размер кластера соответствует рабочей нагрузке. Возможно, Рабочая нагрузка изменилась недавно или размер кластера был изменен. Увеличьте [масштаб](../hdinsight-scaling-best-practices.md) кластера в соответствии с более высокой рабочей нагрузкой.

* `/mnt/resource` могут быть заполнены потерянными файлами (как в случае перезапуска Resource Manager). При необходимости вручную очистите `/mnt/resource/hadoop/yarn/log` и `/mnt/resource/hadoop/yarn/local` .

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подпишитесь на [@AzureSupport](https://twitter.com/azuresupport) — официальный канал Microsoft Azure для работы с клиентами. Вступайте в сообщество Azure для получения нужных ресурсов: ответов, поддержки и советов экспертов.

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка**. Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
