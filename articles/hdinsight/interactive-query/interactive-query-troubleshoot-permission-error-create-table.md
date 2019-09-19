---
title: Ошибка отказа в разрешении при попытке создания таблицы Apache Hive в Azure HDInsight
description: Ошибка отказа в разрешении при попытке создания таблицы Apache Hive в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8440bbc1cf7019e77c6274cbb11c013fb4e3e69d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091393"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Сценарий. Ошибка отказа в разрешении при попытке создания таблицы Apache Hive в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблемы

При попытке создать таблицу вы увидите следующую ошибку:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Если запустить следующую команду хранилища HDFS, вы увидите аналогичное сообщение об ошибке:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Причина:

Возможность создания таблицы в Apache Hive определяется разрешениями, применяемыми к учетной записи хранения кластера. Если разрешения учетной записи хранения кластера неверны, создавать таблицы будет невозможно. Это означает, что у вас могут быть правильные политики Ranger для создания таблицы и по-прежнему отображаться ошибки "отказано в разрешении".

## <a name="resolution"></a>Разрешение

Это вызвано отсутствием достаточных разрешений в используемом контейнере хранилища. Пользователь, создающий таблицу Hive, должен иметь разрешения на чтение, запись и выполнение для контейнера. Дополнительные сведения см. в статье рекомендации [по авторизации Hive с помощью Apache Ranger в HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
