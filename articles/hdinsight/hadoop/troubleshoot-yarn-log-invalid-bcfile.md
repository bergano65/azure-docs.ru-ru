---
title: Не удается прочитать журнал Apache Yarn в Azure HDInsight.
description: Действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76776200"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Сценарий: не удается прочитать журнал Apache Yarn в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Журналы Apache Yarn, найденные из учетной записи хранения, не могут быть прочитаны человеком. Средство синтаксического анализа файлов не работает и выдает следующее сообщение об ошибке:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Причина

Журнал Apache Yarn обрабатывается в `IndexFile` формате, который не поддерживается средством синтаксического анализа файлов.

## <a name="resolution"></a>Решение

1. В веб-браузере перейдите на страницу `https://CLUSTERNAME.azurehdinsight.net`, где `CLUSTERNAME` — это имя вашего кластера.

1. В пользовательском интерфейсе Ambari перейдите на страницу**конфигурации** >  **YARN** > **Advanced** > **Advanced YARN-site**.

1. Для хранилища WASB: значение по умолчанию `yarn.log-aggregation.file-formats` для `IndexedFormat,TFile`—. Измените значение на `TFile`.

1. Для хранилища ADLS: значение по умолчанию `yarn.nodemanager.log-aggregation.compression-type` для `gz`—. Измените значение на `none`.

1. Сохраните изменения и перезапустите все затронутые службы.

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
