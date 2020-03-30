---
title: Apache Ambari UI показывает вниз хостов и услуг в Azure HDInsight
description: Устранение проблем с проблемой UI Apache Ambari, когда отображает сядеки и службы в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895638"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Сценарий: Apache Ambari UI показывает вниз хостов и услуг в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

UI Apache Ambari доступен, но uI показывает, что почти все службы не работает, все хосты, показывающие потеря сердца.

## <a name="cause"></a>Причина

В большинстве сценариев это проблема с сервером Ambari, не работающим на активном головном ноде. Проверьте, какой headnode является активным headnode и убедитесь, что ваш ambari-сервер работает на правой. Не запускайте вручную ambari-server, пусть служба контроллера failover будет отвечать за запуск ambari-сервера на правом головном сервере. Перезагрузите активный головной нод, чтобы заставить неудачу.

Проблемы сетей также могут вызвать эту проблему. Из каждого кластерного узла, `headnodehost`посмотреть, если вы можете пинг . Существует редкая ситуация, когда ни `headnodehost`один кластерный узла не может подключиться к:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Решение

Обычно перезагрузка активного headnode смягчит эту проблему. Если не обращайтесь в службу поддержки HDInsight.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
