---
title: Невозможно создать ноутбук Jupyter в Azure HDInsight
description: Описывает шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 61e7cd8d37108b8f4eea88c4f6b6b2a8cdbfd605
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186805"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Невозможно создать ноутбук Jupyter в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

При запуске записного книжки Jupyter вы получаете сообщение об ошибке, содержащее:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Причина

Несоответствие версии.

## <a name="resolution"></a>Решение

1. Используйте [команду ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Отоверьте приведенную ниже команду, заменив CLUSTERNAME на имя кластера, а затем введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Открыть, `_version.py` выполняя следующую команду:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Изменение **5** к **4,** так что измененная линия отображается следующим образом:

    ```python
    version_info = (4, 0, 3)
    ```

    Сохранить изменения, введя **Ctrl X**, **Y**, **Введите**.

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`перейдите к , где `CLUSTERNAME` имя вашего кластера.

1. Перезапустите службу Jupyter.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
