---
title: Apache Ambari Tez View медленно загружается в Azure HDInsight
description: Apache Ambari Tez View может загружаться медленно или вообще не загружаться в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895088"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Сценарий: Apache Ambari Tez View медленно загружается в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные разрешения проблем при использовании компонентов интерактивного запроса в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Apache Ambari Tez View может загружаться медленно или вообще не загружаться. При загрузке Ambari Tez View можно увидеть, как процессы на Headnodes становятся безответными.

## <a name="cause"></a>Причина

Доступ к AIS ATS Yarn иногда может иметь низкую производительность на кластерах, созданных до октября 2017 года, когда кластер имеет большое количество рабочих мест Hive работать на нем.

## <a name="resolution"></a>Решение

Это проблема, которая была исправлена в октябре 2017 года. Воссоздание кластера позволит ему больше не столкнуться с этой проблемой.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
