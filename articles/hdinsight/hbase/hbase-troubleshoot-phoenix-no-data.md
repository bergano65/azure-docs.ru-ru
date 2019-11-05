---
title: HDP Upgrade & нет данных в представлениях Apache Phoenix в Azure HDInsight
description: При обновлении HDP данные в представлениях Apache Phoenix в Azure HDInsight не выводятся.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 148c7ea4bb3eef3fff7c1a8fd3b865791613704f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498031"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>Сценарий: при обновлении HDP данные в представлениях Apache Phoenix в Azure HDInsight не выводятся.

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Представление Apache Phoenix не содержит даты после обновления с HDP 2,4 до HDP 2,5.

## <a name="cause"></a>Причина:

Таблица индексов для представлений (все индексы для представления хранятся в одной физической таблице Apache HBase) усекается во время обновления

## <a name="resolution"></a>Разрешение

Удаление и повторное создание всех индексов представления после обновления.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к поддержке в рамках управления подпиской и выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через тот или иной [план поддержки Azure](https://azure.microsoft.com/support/plans/).
