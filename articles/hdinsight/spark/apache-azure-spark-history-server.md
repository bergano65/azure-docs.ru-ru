---
title: Используйте расширенные функции в сервере истории Apache Spark для отладки приложений - Azure HDInsight
description: Используйте расширенные функции в сервере истории Apache Spark, чтобы отладить и диагностировать приложения Spark - Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548940"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Используйте расширенные функции сервера истории Spark Apache для отладки и диагностики приложений Spark

В этой статье показано, как использовать расширенные функции Apache Spark History Server для отладки и диагностики завершенных или запущенных приложений Spark. Расширение включает вкладку **Data,** вкладку **График** и вкладку **«Диагноз».** На вкладке **«Данные»** можно проверить данные ввода и вывода задания Spark. На вкладке **График** можно проверить поток данных и воспроизвести график задания. На вкладке **"Диагностика"** можно обратиться к функциям анализа анализа **данных,** **Time Skew**и **Исполнителя.**

## <a name="get-access-to-the-spark-history-server"></a>Получите доступ к серверу истории Spark

Spark History Server — это веб-мий для завершенных и запущенных приложений Spark. Вы можете открыть его либо с портала Azure, либо с URL-адреса.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Откройте веб-ui Spark History Server с портала Azure

1. Откройте кластер Spark на [портале Azure](https://portal.azure.com/). Дополнительные сведения см. в разделе [Отображение кластеров](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Из **панелей мониторинга кластера**выберите **сервер истории Spark.** При появлении запроса введите учетные данные администратора для кластера Spark.

    ![Запустите сервер истории Spark с портала Azure.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Сервер журнала Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Откройте веб-ui Spark History Server по URL

Откройте сервер истории Spark, просматривая, `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`где **CLUSTERNAME** — это название кластера Spark.

Веб-uI Spark History Server может выглядеть так же, как и этот снимок:

![Страница Spark History Server.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Используйте вкладку Данных на сервере истории Spark

Выберите идентификатор задания, а затем выберите **Данные** в меню инструмента, чтобы увидеть представление данных.

+ Просмотрите **входные данные,** **выходы**и **таблицы операций,** выбрав отдельные вкладки.

    ![Вкладки данных на странице приложения Data for Spark.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Копируйте все строки, выбрав кнопку **Copy.**

    ![Копирование данных на странице приложения Spark.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Сохранить все данные в виде . ФАЙЛ CSV, выбрав кнопку **CSV.**

    ![Сохранение данных как . Файл CSV со страницы приложения Data for Spark.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Поиск данных, введя ключевые слова в поле **поиска.** Результаты поиска будут отображаться немедленно.

    ![Поиск данных на странице приложения Spark.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Выберите заголовок столбца для сортировки таблицы. Выберите знак плюс, чтобы расширить строку, чтобы показать более подробную информацию. Выберите знак минус, чтобы свернуть ряд.

    ![Таблица данных на странице приложения Data for Spark.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Загрузите один файл, выбрав кнопку **Частичной загрузки** справа. Выбранный файл будет загружен локально. Если файл больше не существует, это откроет новую вкладку для отображания сообщений об ошибках.

    ![Строка загрузки данных на странице приложения Data for Spark.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Копируйте полный путь или относительный путь, выбрав либо опцию **Copy Full Path,** либо опцию **Copy Relative Path,** которая расширяется из меню загрузки. Для файлов хранения озер данных Azure выберите **Open в Azure Storage Explorer,** чтобы запустить Explorer хранения Azure и найти папку после вхвнутри.

    ![Копирование вариантов полного пути и копирование относительных вариантов пути на странице приложения «Данные» для spark.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Если на одной странице слишком много строк, выберите номера страниц ы в нижней части таблицы для навигации.

    ![Номера страниц на странице приложения «Данные для искры».](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Для получения дополнительной информации нависните или выберите вопросительный знак рядом с **Data for Spark Application,** чтобы показать набор инструментов.

    ![Дополнительную информацию можно получить на странице приложения Data for Spark.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Чтобы отправить обратную связь по вопросам, выберите **Предоставьте нам обратную связь**.

    ![Предоставление обратной связи со страницы приложения Data for Spark.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Используйте вкладку График на сервере истории spark

+ Выберите идентификатор задания, а затем выберите **График** в меню инструмента, чтобы увидеть график задания. По умолчанию график будет отображать все задания. Фильтруй результаты с помощью меню выпадения **идентификатора работы.**

    ![Меню выпадающего идентификатора «Идентификатор» на странице Spark Application & Job Graph.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **Прогресс** выбирается по умолчанию. Проверьте поток данных, выбрав **Прочитано** или **Написано** в меню **выпадения дисплея.**

    ![Проверьте поток данных на странице Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ Цвет фона каждой задачи соответствует тепловой карте.

   ![Тепловая карта на странице Spark Application & Работа Граф.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Color |Описание |
    |---|---|
    |Зеленый|задание выполнено успешно.|
    |Оранжевый|Задача не выполнена, но это не влияет на конечный результат задания. Эти задачи имеют дубликаты или повторяют экземпляры, которые могут быть успешными позже.|
    |Синий|задача выполняется.|
    |White|задача ожидает выполнения, либо этап пропущен.|
    |Красный|задачу выполнить не удалось.|

     ![Выполнение задачи на странице Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     Пропущенные этапы отображаются белым цветом.
    ![Пропущенная задача на странице Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Неудачная задача на странице Spark Application & Вакансия.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > Воспроизведение доступно для завершенных заданий. Выберите кнопку **воспроизведения,** чтобы воспроизвести задание. Остановите задание в любое время, выбрав кнопку «Стоп». При воспроизведении задания каждая задача будет отображать свой статус по цвету. Воспроизведение не поддерживается для неполных заданий.

+ Прокрутите, чтобы увеличить или выйти на график задания, или выберите **Увеличить, чтобы соответствовать,** чтобы сделать его пригодным для экрана.

    ![Выберите Увеличить, чтобы поместиться на странице Spark Application & График работы.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ При сбою задач нависнетнадие над узлом графика, чтобы увидеть набор инструментов, а затем выберите этап, чтобы открыть его на новой странице.

    ![Просмотр инструментария на странице Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ На странице Spark Application & Job Graph на этапах будут отображаться наконечники инструментов и небольшие значки, если задачи отвечают этим условиям:
  + Перекос данных: размер чтения данных > средний размер данных для чтения всех задач на данном этапе No 2 *и* размер чтения данных > 10 МБ.
  + Время перекоса: время выполнения > среднее время выполнения всех задач на этом этапе No 2 *и* время выполнения > 2 минут.

    ![Искаженный значок задачи на странице Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ В графическом узлах выполняется следующая информация о каждом этапе:
  + ID
  + Имя или описание
  + Общий номер задачи
  + Данные читать: сумма ввода размера и перетасовки читать размер
  + Данные пишут: сумма размера вывода и тасовки размера записи
  + Время выполнения: время между временем начала первой попытки и временем завершения последней попытки
  + Количество строк: сумма входных записей, выходных записей, перетасовки прочитанзаписей и перетасовки записей записей записей записей записей записей записей записей записей записей записей записей записей записей записей записей записей записей
  + Ход выполнения

    > [!NOTE]  
    > По умолчанию узла графика выполнения задания будет отображаться информация из последней попытки каждого этапа (за исключением времени выполнения этапа). Но во время воспроизведения узла графика задания будет отображаться информация о каждой попытке.

    > [!NOTE]  
    > Для чтения данных и записи данных размеры, мы используем 1 Mb и 1000 КБ и 1000 1000 байтов.

+ Отправить обратную связь о проблемах, выбрав **Предоставьте нам обратную связь**.

    ![Опция обратной связи на странице Spark Application & Работа Граф.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Используйте вкладку «Диагностика» на сервере «История spark»

Выберите идентификатор вакансии, а затем выберите **Диагноз** в меню инструмента, чтобы увидеть представление диагноза работы. **Вкладка Диагностика** включает в себя **данные Skew**, **Время Skew**, и **анализ использования исполнителя.**

+ Просмотрите **данные Skew,** **Time Skew**и **анализ использования исполнителя,** выбрав вкладки соответственно.

    ![Вкладка Data Skew в вкладке Диагноз.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Неравномерное распределение данных

Выберите вкладку **Data Skew.** Соответствующие перекосы отображаются на основе указанных параметров.

#### <a name="specify-parameters"></a>Указание параметров

В разделе **"Параметры уточнения"** отображаются параметры, которые используются для обнаружения skew данных. Правило по умолчанию: чтение данных задачи превышает трехкратность средней считываемых данных задачи, а чтение данных задачи — более 10 МБ. Если вы хотите определить свое собственное правило для перекоса задач, вы можете выбрать параметры. **Разделы Skewed Stage** и **Skew Chart** будут соответствующим образом обновляться.

#### <a name="skewed-stage"></a>Этап перекоса

В разделе **Skewed Stage** отображаются этапы, на которых есть перекосы задач, отвечающих указанным критериям. Если на этапе есть более чем одна перекосная задача, секция **Skewed Stage** отображает только самую неравновесную задачу (т.е. самые большие данные для искажения данных).

![Более широкое представление вкладки Data Skew в вкладке «Диагноз».](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Скью Диаграмма

При выборе строки в таблице **Skew Stage** **в диаграмме Skew** отображается больше деталей распределения задач на основе времени чтения и выполнения данных. Перекосы задач отмечены красным цветом, а обычные задачи — синим цветом. Для рассмотрения производительности диаграмма отображает до 100 задач образца. Детали задачи отображаются в нижней правой панели.

![Диаграмма Skew для этапа 10 в ui Spark.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Неравномерное распределение времени

На вкладке **Неравномерное распределение времени** отображаются задачи с неравномерным распределением времени выполнения.

#### <a name="specify-parameters"></a>Указание параметров

В разделе **"Параметры уточнения"** отображаются параметры, которые используются для обнаружения перекоса времени. Правило по умолчанию: время выполнения задачи превышает среднее время выполнения задачи, а время выполнения задачи превышает 30 секунд. Параметры можно изменить в соответствии с вашими потребностями. **В skewed Stage** и **Skew Chart** отображаются соответствующие этапы и информация о задачах, как и во вкладке Data **Skew.**

При выборе **Time Skew**отфильтрованный результат отображается в разделе **Skewed Stage** в соответствии с параметрами, установленными в разделе **Параметры уточнения.** При выборе одного элемента в разделе **Skewed Stage** соответствующая диаграмма составляется в третьем разделе, а детали задачи отображаются в нижней правой панели.

![Вкладка времени перекос в вкладке Диагноз.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Графики анализа использования исполнителя

**График использования исполнителя** отображает фактическое распределение исполнителя и состояние выполнения.  

При выборе **анализа использования исполнителя**составлены четыре различные кривые об использовании исполнителя: **Выделенные**исполнители, **Запущенные исполнители,** простаивающие **исполнители**и **Случаи Максима Исполнителя.** Каждый **добавленный исполнитель** или **удаление исполнителя** увеличит или уменьшит выделенные исполнители. Вы можете проверить **Хронику событий** во вкладке **«Вакансии»** для получения дополнительных сравнений.

![Вкладка анализа использования исполнителя в вкладке «Диагноз».](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Выберите значок цвета, чтобы выбрать или не выбрать соответствующее содержимое во всех проектах.

 ![Выберите диаграмму во вкладке «Анализ использования исполнителя».](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>часто задаваемые вопросы

### <a name="how-do-i-revert-to-the-community-version"></a>Как вернуться к версии сообщества?

Чтобы вернуться к версии сообщества, сделайте следующие шаги.

1. Откройте кластер в Амбари.
1. Перейдите к **Spark2** > **Configs**.
1. Выберите **пользовательские искры2-по умолчанию.**
1. Выберите **Добавить свойство ...**.
1. Добавить **spark.ui.enhancement.enabled-false**, а затем сохранить его.
1. Это свойство задает значение **false**.
1. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.

    ![Выключите функцию в Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Выберите **Spark2** в левой панели. Затем, на вкладке **Резюме,** выберите **Spark2 History Server.**

    ![Резюме на поле Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Чтобы перезапустить сервер Spark History Server, выберите кнопку **Started** справа от **Spark2 History Server,** а затем выберите **перезагрузку** из меню выпадающих.

    ![Перезапустите сервер истории spark в Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Освежите веб-ui Spark History Server. Он вернется к версии сообщества.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Как загрузить событие Spark History Server, чтобы сообщить о непой?

Если вы столкнулись с ошибкой в Spark History Server, сделайте следующие шаги, чтобы сообщить о событии.

1. Загрузите событие, выбрав **Скачать** в веб-uI Spark History Server.

    ![Скачать событие в ui Spark History Server.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Выберите **Предоставить нам обратную связь** со страницы **Spark Application & Job Graph.**

    ![Обеспечить обратную связь на странице Spark Application & Работа Граф](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Предоставьте название и описание ошибки. Затем перетащите файл .zip в поле для отправителя и выберите **Отправить новую проблему.**

    ![Загрузите и отправьте новый выпуск.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Как обновить файл .jar в сценарии hotfix?

Если вы хотите обновить с hotfix, используйте следующий скрипт, который будет обновить. `spark-enhancement.jar*`

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Использование

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Пример

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Используйте файл Bash с портала Azure

1. Запустите [портал Azure,](https://ms.portal.azure.com)а затем выберите кластер.
2. Заполните [действие скрипта](../hdinsight-hadoop-customize-cluster-linux.md) со следующими параметрами.

    |Свойство |Значение |
    |---|---|
    |Тип скрипта|- Custom|
    |name|ОбновлениеЯр|
    |URI bash-скрипта|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Типы узлов|Голова, Рабочий|
    |Параметры|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Портал Azure отправляет действие сценария](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Известные проблемы

+ В настоящее время Spark History Server работает только для Spark 2.3 и 2.4.

+ Данные ввода и вывода, которые используют RDD, не отображаются во вкладке **Data.**

## <a name="next-steps"></a>Дальнейшие действия

+ [Управление ресурсами для кластера Apache Spark в HDInsight](apache-spark-resource-manager.md)
+ [Настройка параметров Apache Spark](apache-spark-settings.md)

## <a name="feedback"></a>Отзывы

Если у вас есть какие-либо отзывы или встретить какие-либо проблемы при использовании этого инструмента, отправить по электронной почте ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
