---
title: Исключение хранилища после сброса подключения в Azure HDInsight
description: Исключение хранилища после сброса подключения в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887229"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Сценарий: исключение хранилища после сброса подключения в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Не удалось создать новую таблицу Apache HBase.

## <a name="cause"></a>Причина

В процессе усечения таблицы возникла ошибка подключения к хранилищу. Запись таблицы была удалена в таблице метаданных HBase. Был удален только один файл большого двоичного объекта.

Хотя в хранилище отсутствует большой двоичный объект папки, именуемый `/hbase/data/default/ThatTable`. Драйвер WASB обнаружил наличие приведенного выше файла большого двоичного объекта и не позволит создать BLOB-объект с именем `/hbase/data/default/ThatTable`, так как предполагается, что родительские папки существовали, поэтому создание таблицы завершится ошибкой.

## <a name="resolution"></a>Разрешение

1. В пользовательском интерфейсе Apache Ambari перезапустите активный HMaster. Это позволит одному из двух резервных HMaster становится активным, а новый активный HMaster будет перезагружать сведения о таблице метаданных. Поэтому таблица `already-deleted` не будет отображаться в пользовательском интерфейсе HMaster.

1. Потерянный файл большого двоичного объекта можно найти в средствах пользовательского интерфейса, таких как Cloud Explorer или выполнение команды, например `hdfs dfs -ls /xxxxxx/yyyyy`. Запустите `hdfs dfs -rmr /xxxxx/yyyy`, чтобы удалить этот большой двоичный объект. Например, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Теперь можно создать новую таблицу с тем же именем в HBase.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
