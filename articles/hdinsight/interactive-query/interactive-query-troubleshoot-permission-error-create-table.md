---
title: Разрешение отказано в ошибке с apache Hive таблице в Azure HDInsight
description: Разрешение отказано в ошибке при попытке создать таблицу Apache Hive в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895147"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Сценарий: Разрешение отказано в ошибке при попытке создать таблицу Apache Hive в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные разрешения проблем при использовании компонентов интерактивного запроса в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При попытке создания таблицы вы увидите следующую ошибку:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Вы увидите аналогичное сообщение об ошибке, если вы запустите следующую команду хранения HDFS:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Причина

Возможность создания таблицы в Apache Hive определяется разрешениями, применяемыми к учетной записи хранения кластера. Если разрешения учетной записи хранения кластеров неверны, вы не сможете создавать таблицы. Это означает, что вы можете иметь правильные политики Ranger для создания таблицы, и по-прежнему видеть "Разрешение отказано" ошибки.

## <a name="resolution"></a>Решение

Это вызвано отсутствием достаточных разрешений на используемый контейнер для хранения. Пользователю, создавшемтаблицу Hive, необходимо читать, писать и выполнять разрешения в отношении контейнера. Для получения дополнительной информации, пожалуйста, смотрите [лучшие практики для авторизации Улья с использованием Apache Ranger в HDP 2.2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
