---
title: Не удается прочитать журнал Apache Yarn в Azure HDInsight
description: Устранение неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776200"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Сценарий: Не удается прочитать журнал Apache Yarn в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Записи Apache Yarn, найденные в учетной записи хранилища, не читаемы для человека. Файл-парсер не работает и производит следующее сообщение об ошибке:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Причина

Брек Apache Yarn агрегируется в `IndexFile` формат, который не поддерживается файловым парсером.

## <a name="resolution"></a>Решение

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net`перейдите к , где `CLUSTERNAME` имя вашего кластера.

1. От ambari UI, перейдите к **YARN** > **Configs** > **Расширенный** > **Расширенный пряжи-сайт**.

1. Для хранилища WASB: Значение `yarn.log-aggregation.file-formats` `IndexedFormat,TFile`по умолчанию для . Измените значение `TFile`на .

1. Для хранения ADLS: Значение `yarn.nodemanager.log-aggregation.compression-type` `gz`по умолчанию для . Измените значение `none`на .

1. Сохраните изменение и перезапустите все затронутые службы.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
