---
title: Jupyter 404 ошибка-"Блокировка кросс-источника API" — Azure HDInsight
description: Ошибка Jupyter Server 404 "не найдена" из-за блокировки межисточникового API в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 7f8eb000533673d243258d569834869bbb2a1778
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241132"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Сценарий: Jupyter Server 404 "не найдено" из-за "блокировки межисточникового API" в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При доступе к службе Jupyter в HDInsight отображается окно ошибки с сообщением "не найдено". Если проверить журналы Jupyter, вы увидите нечто вроде:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: hn0-pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Вы также можете увидеть IP-адрес в поле "источник" в журнале Jupyter.

## <a name="cause"></a>Причина:

Эта ошибка может быть вызвана несколькими причинами:

- Если вы настроили правила группы безопасности сети (NSG), чтобы ограничивать доступ к кластеру. При ограничении доступа с помощью правил NSG вы сможете получить прямой доступ к Apache Ambari и другим службам, используя IP-адрес, а не имя кластера. Однако при доступе к Jupyter может появиться ошибка 404 "не найдено".

- Если вы задаете шлюзу HDInsight настраиваемое DNS-имя, отличное от стандартного `xxx.azurehdinsight.net`.

## <a name="resolution"></a>Разрешение

1. Измените файлы jupyter.py в следующих двух местах:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Найдите строку, в которой написано: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` и измените ее на: `NotebookApp.allow_origin='\"*\"'`.

1. Перезапустите службу Jupyter из Ambari.

1. При вводе `ps aux | grep jupyter` в командной строке должно появиться сообщение о том, что он позволяет подключаться к нему по любому URL-адресу.

Это менее безопасно, чем уже установленный параметр. Но предполагается, что доступ к кластеру ограничен, и что у него из внешней сети разрешено подключаться к кластеру, так как мы NSG на месте.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
