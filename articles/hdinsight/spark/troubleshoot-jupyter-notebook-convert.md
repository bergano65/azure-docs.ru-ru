---
title: Не удалось создать записную книжку Jupyter в Azure HDInsight
description: Описывает шаги по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 61e7cd8d37108b8f4eea88c4f6b6b2a8cdbfd605
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186805"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Не удалось создать записную книжку Jupyter в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

При запуске записной книжки Jupyter появляется сообщение об ошибке, содержащее следующее:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Причина

Несовпадение версий.

## <a name="resolution"></a>Решение

1. Используйте [команду SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Измените приведенную ниже команду, заменив ИМЯ_КЛАСТЕРА именем кластера, а затем введите следующую команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Откройте `_version.py`, выполнив следующую команду:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Измените значение **5** на **4** , чтобы измененная строка выявлялась следующим образом:

    ```python
    version_info = (4, 0, 3)
    ```

    Сохраните изменения, введя **CTRL + X**, **Y**, **введите**.

1. В веб-браузере перейдите к `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`, где `CLUSTERNAME` — имя кластера.

1. Перезапустите службу Jupyter.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
