---
title: Использование Grafana в Azure HDInsight
description: Узнайте, как получить доступ к панели мониторинга Grafana с кластерами Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 6027978b50ca72de5d18ff474b36814e22a94e85
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552225"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Доступ к Grafana в Azure HDInsight

[Grafana](https://grafana.com/) — это популярное средство с открытым кодом для построения графов и панелей мониторинга. Grafana — многофункциональное средство, которое не только позволяет пользователям создавать настраиваемые панели мониторинга с общим доступом, но также предоставляет такие возможности, как шаблонны и скрипты панелей мониторинга, интеграция LDAP, использование нескольких источников данных и многое другое.

В настоящее время в Azure HDInsight Grafana поддерживается с типами кластеров HBase, Kafka и Interactive Requery. Он не поддерживается для кластеров с включенным пакетом безопасности Enterprise.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-an-apache-hadoop-cluster"></a>Создание кластера Apache Hadoop

См. раздел [Создание кластеров Apache Hadoop с помощью портал Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). В качестве **типа кластера**выберите **Kafka**, **HBase**или **интерактивный запрос**.

## <a name="access-the-grafana-dashboard"></a>Доступ к панели мониторинга Grafana

1. В веб-браузере перейдите по адресу `https://CLUSTERNAME.azurehdinsight.net/grafana/`, где ИМЯ_КЛАСТЕРА — имя кластера.

1. Введите учетные данные пользователя кластера Hadoop.

1. Появится панель мониторинга Grafana, которая выглядит следующим образом:

    ![Веб-панель мониторинга HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Панель мониторинга Grafana HDInsight")

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите созданный кластер, сделав следующее:

1. Войдите на [портал Azure](https://portal.azure.com/).

1. В поле **Поиск** в верхней части страницы введите **HDInsight**.

1. Выберите **Кластеры HDInsight** в разделе **Службы**.

1. В появившемся списке кластеров HDInsight щелкните **...** рядом с созданным кластером.

1. Выберите команду **Удалить**. Выберите **Да**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об анализе данных с помощью HDInsight см. в следующих статьях:

* [Используйте Apache Hive с HDInsight](../hadoop/hdinsight-use-hive.md).

* [Использование MapReduce с HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* Приступая [к работе с Visual Studio Hadoop Tools для HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
