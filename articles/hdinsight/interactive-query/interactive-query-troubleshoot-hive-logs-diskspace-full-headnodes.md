---
title: 'Устранение неполадок: Apache Hive журналов заполнение дискового пространства — Azure HDInsight'
description: В этой статье приведены инструкции по устранению неполадок, которые необходимо выполнить, когда Apache Hive журналы заполняют дисковое пространство головных узлов в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 64bf5714f5eb99df9929a47fef414a827ec680af
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145639"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Сценарий: журналы Apache Hive заполняют дисковое пространство головных узлов в Azure HDInsight.

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем, связанных с нехваткой дискового пространства на головных узлах в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

В кластере Apache Hive или LLAP нежелательные журналы занимают все дисковое пространство на головных узлах. Это состояние может вызвать следующие проблемы.

- SSH-доступ завершается сбоем, так как на головном узле не осталось места.
- Ambari выдает *ошибку HTTP: служба 503 недоступна* .
- HiveServer2 Interactive не перезапускается.

`ambari-agent`При возникновении проблемы в журналах будут содержаться следующие записи:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Причина

В расширенных конфигурациях Hive log4j текущее расписание удаления по умолчанию — это удаление файлов старше 30 дней на основе даты последнего изменения.

## <a name="resolution"></a>Решение

1. Перейдите на страницу Сводка по компоненту Hive на портале Ambari и выберите вкладку **configs (конфигурации** ).

2. Перейдите к `Advanced hive-log4j` разделу **Дополнительные параметры** .

3. Задайте `appender.RFA.strategy.action.condition.age` для параметра возраст по своему усмотрению. В этом примере для параметра Age будет задано значение 14 дней: `appender.RFA.strategy.action.condition.age = 14D`

4. Если вы не видите связанные параметры, добавьте следующие параметры:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Задайте `hive.root.logger` для значение `INFO,RFA` , как показано в следующем примере. Значение по умолчанию — `DEBUG` , что делает журналы большими.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Сохраните настройки и перезапустите необходимые компоненты.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка** . Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
