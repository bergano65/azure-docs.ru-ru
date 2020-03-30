---
title: Ошибка Jupyter 404 - "Блокировка API cross Origin" - Azure HDInsight
description: Jupyter server 404 "Не найдено" ошибка из-за "Блокировка Cross Origin API" в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894415"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Сценарий: Jupyter server 404 "Не найдено" ошибка из-за "Блокировка Cross Origin API" в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При доступе к службе Jupyter на HDInsight вы видите поле ошибки с надписью "Не найдено". Если вы проверите журналы Jupyter, вы увидите что-то вроде этого:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Вы также можете увидеть IP-адрес в поле "Origin" в журнале Jupyter.

## <a name="cause"></a>Причина

Эта ошибка может быть вызвана парой вещей:

- Если вы настроили правила Группы сетевой безопасности (NSG) для ограничения доступа к кластеру. Ограничение доступа с правилами NSG по-прежнему позволит вам получить прямой доступ к Apache Ambari и другим службам, используя IP-адрес, а не название кластера. Однако при доступе к Jupyter можно было увидеть ошибку 404 "Не найдена".

- Если вы дали шлюзу HDInsight индивидуальное имя `xxx.azurehdinsight.net`DNS, кроме стандартного.

## <a name="resolution"></a>Решение

1. Измените jupyter.py файлы в этих двух местах:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Найти строку, `NotebookApp.allow_origin='\"https://{2}.{3}\"'` которая говорит: `NotebookApp.allow_origin='\"*\"'`И изменить его на: .

1. Перезапустите jupyter службы из Амбари.

1. Ввод `ps aux | grep jupyter` в запросе команды должен показать, что он позволяет подключить к нему любой URL-адрес.

Это менее безопасно, чем настройки, которые мы уже имели на месте. Но предполагается, что доступ к кластеру ограничен, и что один из внешних разрешено подключиться к кластеру, как у нас есть NSG на месте.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
