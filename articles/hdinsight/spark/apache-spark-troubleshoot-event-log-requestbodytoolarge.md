---
title: Ошибка Рекуестбодитуларже из приложения Apache Spark — Azure HDInsight
description: Нативеазурефилесистем... Рекуестбодитуларже отображается в журнале для приложения потоковой передачи Apache Spark в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894396"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"Нативеазурефилесистем... Рекуестбодитуларже "отображается в журнале приложений Apache Spark потоковой передачи в HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы исправления проблем, возникающих при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Ошибка: `NativeAzureFileSystem ... RequestBodyTooLarge` отображается в журнале драйвера для приложения потоковой передачи Apache Spark.

## <a name="cause"></a>Причина

Возможно, файл журнала событий Spark имеет ограничение длины файла для WASB.

В Spark 2,3 Каждое приложение Spark создает один файл журнала событий Spark. Файл журнала событий Spark для приложения потоковой передачи Spark продолжит расти во время работы приложения. Сегодня файл в WASB имеет ограничение в 50000 блоков, а размер блока по умолчанию — 4 МБ. Поэтому в конфигурации по умолчанию максимальный размер файла составляет 195 ГБ. Однако служба хранилища Azure увеличила максимальный размер блока до 100 МБ, что фактически вывела ограничение одного файла на 4,75 ТБ. Дополнительные сведения см. в статье [Scalability and performance targets for Blob storage](../../storage/blobs/scalability-targets.md) (Целевые показатели масштабируемости и производительности для хранилища BLOB-объектов).

## <a name="resolution"></a>Решение

Для этой ошибки доступны три решения:

* Увеличьте размер блока до 100 МБ. В пользовательском интерфейсе Ambari измените свойство конфигурации HDFS `fs.azure.write.request.size` (или создайте его в `Custom core-site` разделе). Задайте для свойства большее значение, например: 33554432. Сохраните обновленную конфигурацию и перезапустите затронутые компоненты.

* Периодически прекращайте и повторно отправляйте задание Spark-Streaming.

* Используйте HDFS для хранения журналов событий Spark. Использование HDFS для хранения может привести к утрате данных событий Spark при масштабировании кластера или обновлении Azure.

    1. Внесите изменения в `spark.eventlog.dir` `spark.history.fs.logDirectory` Пользовательский интерфейс Ambari и через него:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Создание каталогов в HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Перезапустите все затронутые службы через пользовательский интерфейс Ambari.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка**. Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
