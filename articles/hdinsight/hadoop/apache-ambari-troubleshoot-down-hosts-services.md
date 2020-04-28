---
title: Пользовательский интерфейс Apache Ambari показывает узлы и службы в Azure HDInsight
description: Устранение неполадок пользовательского интерфейса Apache Ambari при отображении узлов и служб в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895638"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Сценарий: Пользовательский интерфейс Apache Ambari показывает узлы и службы в Azure HDInsight.

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Пользовательский интерфейс Apache Ambari доступен, но в пользовательском интерфейсе отображаются почти все службы, но все узлы, отображающие пульс, теряются.

## <a name="cause"></a>Причина:

В большинстве случаев это проблема с Ambari Server, который не работает на активном головного узла. Проверьте, какая головного узла является активной головного узла, и убедитесь, что ambari-сервер работает справа. Не запускайте ambari-Server вручную. Служба отказоустойчивого контроллера должна отвечать за запуск ambari-Server в правой головного узла. Перезагрузите активный головного узла, чтобы принудительно выполнить отработку отказа.

Эта проблема также может быть вызвана неполадками в сети. На каждом узле кластера см. раздел Проверка возможности проверки `headnodehost`связи. Существует редкий случай, когда ни один из узлов кластера не `headnodehost`может подключиться к:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Разрешение

Обычно перезагрузка Active головного узла позволит устранить эту ошибку. Если вы не можете обратиться в службу поддержки HDInsight.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
