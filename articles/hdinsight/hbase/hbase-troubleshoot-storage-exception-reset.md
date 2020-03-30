---
title: Исключение для хранения после сбросить соединение в Azure HDInsight
description: Исключение для хранения после сбросить соединение в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887229"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Сценарий: Исключение для хранения после сбросить соединение в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Невозможно создать новую таблицу Apache HBase.

## <a name="cause"></a>Причина

В процессе усечения таблицы возникла проблема подключения к хранилищам. Запись таблицы была удалена в таблице метаданных HBase. Все, кроме одного файла капли, были удалены.

Хотя не было папки `/hbase/data/default/ThatTable` капли называется сидя в хранилище. Водитель WASB нашел существование вышефайлфайла файла и не позволит создать `/hbase/data/default/ThatTable` какой-либо капли называется, поскольку он предполагал, что родительские папки существовали, таким образом, создание таблицы не удастся.

## <a name="resolution"></a>Решение

1. С uI Apache Ambari перезапустите активный HMaster. Это позволит одному из двух резервных HMaster стать активным, а новый активный HMaster перезагрузит информацию о таблице метаданных. Таким образом, вы `already-deleted` не увидите таблицу в uI HMaster.

1. Вы можете найти файл сирот капли из инструментов uI, таких как Cloud Explorer или запущенная команда, как. `hdfs dfs -ls /xxxxxx/yyyyy` Выполнить, `hdfs dfs -rmr /xxxxx/yyyy` чтобы удалить эту каплю. Например, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Теперь вы можете создать новую таблицу с тем же названием в HBase.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
