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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76776200"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Сценарий: не удается прочитать журнал Apache Yarn в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

Журналы Apache Yarn, найденные из учетной записи хранения, не могут быть прочитаны человеком. Средство синтаксического анализа файлов не работает и выдает следующее сообщение об ошибке:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Причина:

Журнал Apache Yarn обрабатывается в `IndexFile` формате, который не поддерживается средством синтаксического анализа файлов.

## <a name="resolution"></a>Решение

1. В веб-браузере перейдите на страницу `https://CLUSTERNAME.azurehdinsight.net`, где `CLUSTERNAME` — это имя вашего кластера.

1. В пользовательском интерфейсе Ambari перейдите на страницу конфигурации **YARN**  >  **Configs**  >  **Advanced**  >  **Advanced YARN-site**.

1. Для хранилища WASB: значение по умолчанию для `yarn.log-aggregation.file-formats` — `IndexedFormat,TFile` . Измените значение на `TFile` .

1. Для хранилища ADLS: значение по умолчанию для `yarn.nodemanager.log-aggregation.compression-type` — `gz` . Измените значение на `none` .

1. Сохраните изменения и перезапустите все затронутые службы.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подпишитесь на [@AzureSupport](https://twitter.com/azuresupport) — официальный канал Microsoft Azure для работы с клиентами. Вступайте в сообщество Azure для получения нужных ресурсов: ответов, поддержки и советов экспертов.

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка**. Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
