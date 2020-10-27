---
title: Присоединение в Apache Hive приводит к ошибке OutOfMemory — Azure HDInsight
description: Работа с ошибками OutOfMemory "превышен предел накладных расходов GC"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: c227758e31b3b17768b8140475872245b2f34e52
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532957"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Сценарий: присоединение в Apache Hive приводит к ошибке OutOfMemory в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Поведение по умолчанию для Apache Hive соединений заключается в загрузке всего содержимого таблицы в память, чтобы соединение можно было выполнить без выполнения шага Map/reduce. Если таблица Hive слишком велика для размещения в памяти, запрос может завершиться ошибкой.

## <a name="cause"></a>Причина

При выполнении операций объединения в Hive с достаточным размером возникает следующая ошибка:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Решение

Запретите загрузку таблиц Hive в память в операциях объединения (вместо этого выполните операцию Map или reduce), задав следующее значение конфигурации Hive:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Дальнейшие действия

Если задание этого значения не привело к устранению проблемы, посетите один из следующих...

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка** . Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).