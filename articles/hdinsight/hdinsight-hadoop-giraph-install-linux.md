---
title: Установка и использование Giraph в Azure HDInsight
description: Узнайте, как установить Giraph в кластерах HDInsight с помощью действий сценария. Giraph можно использовать для обработки графов в Apache Hadoop в облаке Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/26/2019
ms.openlocfilehash: 1f6fd88ec492f26f6819dff099ec8fe53364ba0b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552259"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Установка Apache Giraph в кластерах HDInsight Hadoop и использование Giraph для обработки диаграмм больших объемов

Узнайте, как установить Apache Giraph в кластере HDInsight. Действие сценария в HDInsight позволяет настроить кластер с помощью сценария bash. Сценарии можно использовать для настройки кластеров во время и после создания кластера.

## <a name="what-is-giraph"></a>Что такое Giraph

[Apache Giraph](https://giraph.apache.org/) позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight. Графы моделируют взаимоотношения между объектами. Например, подключения между маршрутизаторами в большой сети, подобной Интернету, или взаимоотношения между людьми в социальных сетях. Обработка графов дает возможность делать выводы о взаимоотношениях объектов в графе, таких как:

* определение потенциальных друзей на основе текущих взаимоотношений;

* определение кратчайшего маршрута между двумя компьютерами в сети;

* вычисление ранга страницы для веб-страниц.

> [!WARNING]  
> Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Служба технической поддержки Майкрософт помогает выявить и решить проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты, такие как Giraph, получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. Эту проблему может решить служба технической поддержки Майкрософт. В противном случае необходимо обратиться в сообщество разработчиков открытого кода, обладающих большим опытом в этой сфере. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) или [https://stackoverflow.com](https://stackoverflow.com). Кроме того, в проектах Apache есть сайты проектов на [https://apache.org](https://apache.org), например [Hadoop](https://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Что делает сценарий

Сценарий выполняет следующие действия:

* Устанавливает Giraph в `/usr/hdp/current/giraph`.

* Копирует файл `giraph-examples.jar` в хранилище по умолчанию (WASB) для кластера: `/example/jars/giraph-examples.jar`.

## <a name="install-giraph-using-script-actions"></a>Установка Giraph с помощью действий сценария

Пример скрипта для установки Giraph в кластере HDInsight доступен по адресу `https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`

Этот раздел содержит инструкции по использованию примера сценария при создании кластера с помощью портала Azure.

> [!NOTE]  
> Действие сценария может применяться следующими способами:
> * Azure PowerShell
> * Интерфейс командной строки Azure
> * Пакет SDK для HDInsight .NET
> * Шаблоны Azure Resource Manager
> 
> Действия сценария также можно применять к уже работающим кластерам. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

1. Начните создание кластера, выполнив действия, описанные в статье [Создание кластеров HDInsight под управлением Linux](hdinsight-hadoop-create-linux-clusters-portal.md), но не создание. Необходимо использовать **классическую процедуру создания** и **Пользовательский интерфейс (размер, параметры, приложения)** .

1. В этом примере убедитесь, что в разделе **Размер кластера** **число рабочих узлов** не меньше 2.

1. В разделе **действия скрипта** укажите следующие сведения.

    |Свойство |Значение |
    |---|---|
    |Тип скрипта|- Custom|
    |Имя|установка Giraph;|
    |URI bash-скрипта|`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`|
    |Типы узлов|Head|
    |Параметры|Не указывайте|

    Дополнительные сведения см. [в разделе Использование действия сценария во время создания кластера](./hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation).

1. Продолжите создание кластера, как описано в разделе [Создание кластеров HDInsight под управлением Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="how-do-i-use-giraph-in-hdinsight"></a>Разделы справки использовать Giraph в HDInsight?

После создания кластера выполните следующие действия, чтобы запустить пример SimpleShortestPathsComputation (входит в Giraph). В примере использована простая реализация [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) для поиска кратчайшего пути между объектами в графе.

1. Используйте [команду SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Измените приведенную ниже команду, заменив ИМЯ_КЛАСТЕРА именем кластера, а затем введите следующую команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Создайте файл **tiny_graph.txt**, используя следующую команду.

    ```bash
    nano tiny_graph.txt
    ```

    В качестве содержимого файла добавьте следующий текст:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Эти данные описывают взаимоотношения между объектами в направленном графе с использованием формата `[source_id, source_value,[[dest_id], [edge_value],...]]`. Каждая строка представляет взаимоотношение между объектом `source_id` и одним или несколькими объектами `dest_id`. Значение `edge_value` можно представить себе как силу или расстояние подключения между `source_id` и `dest\_id`.

    В визуальной форме и с использованием значения (или веса) в качестве расстояния между объектами данные могут выглядеть, как показано на следующей схеме.

    ![tiny_graph.txt начерчен в виде кругов с линиями различной длины между](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Чтобы сохранить файл, нажмите **CTRL+X**, затем **Y** и **ВВОД**.

4. Чтобы сохранить данные в основном хранилище в кластере HDInsight, используйте следующую команду:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Запустите пример SimpleShortestPathsComputation, используя следующую команду:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    > [!IMPORTANT]
    > Значение, передаваемое в `-w`, должно быть меньше или равно фактическому числу рабочих узлов.

    В следующей таблице описаны параметры, используемые в этой команде.

   | Параметр | Что будет сделано |
   | --- | --- |
   | JAR |JAR-файл, содержащий примеры. |
   | org. Apache. giraph. Гирафруннер |Класс, используемый для запуска примеров. |
   | org. Apache. giraph. examples. пример simpleshortestpathscomputation |Используемый пример. В нем вычисляется кратчайший путь между ID 1 и всеми другими идентификаторами в графе. |
   | -CA mapred. job. Tracker |Головной узел кластера. |
   | -ВИФ |Формат входных данных. |
   | — виртуальный IP-адрес |Файл входных данных. |
   | -ВОФ |Формат выходных данных. В данном примере — ИД и значение в виде обычного текста. |
   | -Op |Расположение выходных данных. |
   | -w 2 |Количество используемых рабочих ролей. В этом примере — 2. |

    Дополнительные сведения об этих и других параметрах, которые используются в примерах Giraph, см. в [кратком руководстве по Giraph](https://giraph.apache.org/quick_start.html).

6. После завершения задания результаты сохраняются в каталоге **/ексампле/аутпут/шортестпасс** . Имена выходных файлов начинаются с **Part-m** и заканчиваются числом, указывающим на первый, второй и т. д. файла. Чтобы просмотреть выходные данные, используйте следующую команду:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Выходные данные выглядят следующим образом:

    ```output
    0    1.0
    4    5.0
    2    2.0
    1    0.0
    3    1.0
    ```

    Пример SimpleShortestPathComputation жестко запрограммирован для запуска с объекта ID 1 и поиска кратчайшего пути к другим объектам. Выходные данные имеют следующий формат: `destination_id` и `distance`. `distance` представляет собой значение переходов на пути между объектом ID 1 и целевым объектом ID.

    При визуализации данных можно проверить результаты, проходя кратчайшие пути между ID 1 и всеми другими объектами. Кратчайший путь между ID 1 и ID 4 — это 5. Это значение равно общему расстоянию между ИДЕНТИФИКАТОРами 1 и 3, а затем ИДЕНТИФИКАТОРом 3 и 4.

    ![Представление объектов в виде кругов с кратчайшими путями между ними](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Дальнейшие действия

[Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md).
