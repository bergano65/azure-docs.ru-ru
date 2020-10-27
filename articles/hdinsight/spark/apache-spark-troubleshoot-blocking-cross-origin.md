---
title: Jupyter 404 ошибка-"Блокировка кросс-источника API" — Azure HDInsight
description: Ошибка Jupyter Server 404 "не найдена" из-за блокировки межисточникового API в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 472dde22e5df32e7d2a276b0515a0f1edafe52b3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545673"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Сценарий: Jupyter Server 404 "не найдено" из-за "блокировки межисточникового API" в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы исправления проблем, возникающих при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При доступе к службе Jupyter в HDInsight отображается окно ошибки с сообщением "не найдено". Если проверить журналы Jupyter, вы увидите нечто вроде:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Вы также можете увидеть IP-адрес в поле "источник" в журнале Jupyter.

## <a name="cause"></a>Причина

Эта ошибка может быть вызвана несколькими причинами:

- Если вы настроили правила группы безопасности сети (NSG), чтобы ограничивать доступ к кластеру. При ограничении доступа с помощью правил NSG вы сможете получить прямой доступ к Apache Ambari и другим службам, используя IP-адрес, а не имя кластера. Однако при доступе к Jupyter может появиться ошибка 404 "не найдено".

- Если вы задаете шлюзу HDInsight настраиваемое DNS-имя, отличное от стандартного `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Решение

1. Измените файлы jupyter.py в следующих двух местах:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Найдите строку, в которой написано: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` и измените ее на: `NotebookApp.allow_origin='\"*\"'` .

1. Перезапустите службу Jupyter из Ambari.

1. При вводе `ps aux | grep jupyter` в командной строке должна отобразиться возможность подключения к любому URL-адресу.

Это менее безопасно, чем уже установленный параметр. Но предполагается, что доступ к кластеру ограничен, и что у него из внешней сети разрешено подключаться к кластеру, так как мы NSG на месте.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка** . Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).