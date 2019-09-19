---
title: Присоединение в Apache Hive приводит к ошибке OutOfMemory в Azure HDInsight
description: Работа с ошибками OutOfMemory "превышен предел накладных расходов GC"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: e1f41b6e1e5f51cb7e6e0af1e99184cdfbd373e1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091411"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Сценарий. Присоединение в Apache Hive приводит к ошибке OutOfMemory в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблемы

Поведение по умолчанию для Apache Hive соединений заключается в загрузке всего содержимого таблицы в память, чтобы соединение можно было выполнить без выполнения шага Map/reduce. Если таблица Hive слишком велика для размещения в памяти, запрос может завершиться ошибкой.

## <a name="cause"></a>Причина:

При выполнении операций объединения в Hive с достаточным размером возникает следующая ошибка:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Разрешение

Запретите загрузку таблиц Hive в память в операциях объединения (вместо этого выполните операцию Map или reduce), задав следующее значение конфигурации Hive:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Следующие шаги

Если задание этого значения не привело к устранению проблемы, посетите один из следующих...

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
