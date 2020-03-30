---
title: Использование Grafana в Azure HDInsight
description: Узнайте, как получить доступ к панели мониторинга Grafana с кластерами Apache Hadoop в Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082869"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Доступ к Grafana в Azure HDInsight

[Grafana](https://grafana.com/) — это популярное средство с открытым кодом для построения графов и панелей мониторинга. Grafana — многофункциональное средство, которое не только позволяет пользователям создавать настраиваемые панели мониторинга с общим доступом, но также предоставляет такие возможности, как шаблонны и скрипты панелей мониторинга, интеграция LDAP, использование нескольких источников данных и многое другое.

В настоящее время в Azure HDInsight Grafana поддерживается типами кластеров Spark, HBase, Kafka и Interactive Query. Он не поддерживается для кластеров с включенным пакетом корпоративной безопасности.

Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом.

## <a name="create-an-apache-hadoop-cluster"></a>Создание кластера Apache Hadoop

[См. Создать кластеры Apache Hadoop с помощью портала Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). Для **типа кластера**, выберите **Spark**, **Кафка**, **HBase**, или **Интерактивный запрос.**

## <a name="access-the-grafana-dashboard"></a>Доступ к панели мониторинга Grafana

1. Из веб-браузера `https://CLUSTERNAME.azurehdinsight.net/grafana/` перейдите туда, где clusterNAME — это название кластера.

1. Введите учетные данные пользователя кластера Hadoop.

1. Появится панель мониторинга Grafana, которая выглядит следующим образом:

    ![HDInsight Графана веб-приборной панели](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Dashboard HDInsight Grafana")

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите созданный кластер, сделав следующее:

1. Войдите на [портал Azure](https://portal.azure.com/).

1. В поле **Поиск** в верхней части страницы введите **HDInsight**.

1. Выберите **Кластеры HDInsight** в разделе **Службы**.

1. В списке кластеров HDInsight, которые отображались, выберите **...**

1. Выберите **Удалить**. Нажмите кнопку **Да**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об анализе данных с помощью HDInsight см. в следующих статьях:

* [Используйте Apache Hive с HDInsight](../hadoop/hdinsight-use-hive.md).

* [Используйте MapReduce с HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Начало использования визуальных инструментов Студии Hadoop для HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
