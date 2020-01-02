---
title: Руководство по Анализ данных Apache Spark Azure HDInsight с помощью Power BI
description: 'Учебник: визуализация хранимых данных Apache Spark в кластерах HDInsight с помощью Microsoft Power BI'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: 3fd1405d8421d71f52d9cd215dd055ce1595abd0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327275"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Руководство по Анализ данных Apache Spark с использованием Power BI в HDInsight

Из этого учебника вы узнаете, как использовать [Microsoft Power BI](https://powerbi.microsoft.com/) для визуализации данных в кластере Apache Spark в [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Визуализация данных Spark с помощью Power BI

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Выполните инструкции из статьи [Руководство. Загрузка данных и выполнение запросов в кластере Apache Spark в Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Необязательно: [Пробная подписка Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Проверка данных

[Записная книжка Jupyter Notebook](https://jupyter.org/), созданная при работе с [предыдущим руководством](apache-spark-load-data-run-query.md), содержит код для создания таблицы `hvac`. Эта таблица основана на CSV-файле, доступном во всех кластерах HDInsight Spark в `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Ниже описана процедура проверки данных.

1. В записной книжке Jupyter вставьте приведенный ниже пример кода и нажмите клавиши **SHIFT + ВВОД**. Этот код предназначен для проверки наличия таблиц.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Он возвращает примерно такие выходные данные:

    ![Отображение таблиц в Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Если закрыть записную книжку перед выполнением шага выше, таблица `hvactemptable` будет очищена и не будет включена в выходные данные.  С помощью инструментов бизнес-аналитики можно получить доступ только к таблицам Hive, размещенным в хранилище метаданных (для таких таблиц в столбце **isTemporary** задано значение **false**). В этом руководстве вы подключитесь к созданной ранее таблице **hvac**.

2. Вставьте указанный ниже фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**. Этот код проверяет данные в таблице.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Он возвращает примерно такие выходные данные:

    ![Отображение строк из таблицы hvac в Spark](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. Для этого в меню **File** (Файл) элемента Notebook выберите **Close and Halt** (Закрыть и остановить). Завершите работу записной книжки для освобождения ресурсов.

## <a name="visualize-the-data"></a>Визуализация данных

В этом разделе вы с помощью Power BI создадите визуализации, отчеты и панели мониторинга на основе данных из кластера Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Создание отчета в Power BI Desktop

Начиная работать со Spark, в первую очередь необходимо подключиться к кластеру в Power BI Desktop, загрузить данные из кластера, а затем создать базовую визуализацию на основе этих данных.

> [!NOTE]  
> Соединитель, описанный в этой статье, находится в стадии предварительной версии. Любые отзывы о нем вы можете предоставить на [сайте сообщества Power BI](https://community.powerbi.com/) или на форуме [Power BI ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Откройте Power BI Desktop. Закройте экран-заставку запуска, если он открыт.

2. Перейдите с вкладки **Домашняя страница** к элементу **Получение данных** > **Дополнительно...**

    ![Получение данных в Power BI Desktop из HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Получение данных в Power BI из Apache Spark BI")

3. Введите в поле поиска `Spark` и выберите **Azure HDInsight Spark**, а затем щелкните **Подключиться**.

    ![Получение данных в Power BI из Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Получение данных в Power BI из Apache Spark BI")

4. Введите URL-адрес кластера (в формате `mysparkcluster.azurehdinsight.net`) в текстовое поле **Сервер**.

5. В разделе **Режим подключения к данным** выберите **DirectQuery**. Нажмите кнопку **ОК**.

    Для Spark можно использовать любой режим подключения данных. При использовании DirectQuery изменения отображаются в отчетах, при этом весь набор данных не обновляется. В случае импорта данных необходимо обновить набор данных, чтобы увидеть изменения. Дополнительные сведения о том, как и когда следует использовать DirectQuery, см. в статье [Использование DirectQuery в Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Введите данные учетной записи для входа в HDInsight и выберите **Подключиться**. Имя учетной записи по умолчанию — *admin*.

7. Выберите таблицу `hvac`, дождитесь отображения данных предварительного просмотра и щелкните **Загрузка**.

    ![Имя пользователя и пароль для кластера Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Имя пользователя и пароль для кластера Spark")

    Теперь в Power BI Desktop есть все сведения, необходимые для подключения к кластеру Spark и загрузки данных из таблицы `hvac`. Таблица и ее столбцы отображаются в области **Fields** (Поля).

8. Отобразите расхождения между целевой температурой и фактической температурой для каждого здания:

    1. В области **визуализаций** выберите **Диаграмма с областями**.

    2. Перетащите поле **BuildingID** в раздел **Ось**, а поля **ActualTemp** и **TargetTemp** в раздел **значений**.

        ![Добавление столбцов значений](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Добавление столбцов значений")

        Схема выглядит следующим образом.

        ![Сумма диаграммы с областями](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Сумма диаграммы с областями")

        По умолчанию представление показывает сумму для **фактической температуры** и **целевой температуры**. Выберите направленную вниз стрелку рядом с полями **ActualTemp** и **TragetTemp** на панели визуализации, и вы увидите выбранную **сумму**.

    3. Щелкните направленную вниз стрелку рядом с полями **ActualTemp** и **TragetTemp** на панели визуализации, выберите **Среднее**, чтобы получить среднее значение фактической и целевой температуры для каждого здания.

        ![Среднее значение](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Среднее значение")

        Визуализация данных должна выглядеть примерно так, как показано на снимке экрана ниже. Наведите указатель мыши на визуализацию, чтобы отобразить всплывающие подсказки с соответствующими данными.

        ![Диаграмма с областями](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Диаграмма с областями")

9. Выберите **Файл** > **Сохранить**, введите имя `BuildingTemperature` для файла, а затем выберите **Сохранить**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Публикация отчета в службе Power BI (необязательно)

Служба Power BI поддерживает совместное использование отчетов и панелей мониторинга для всей организации. В этом разделе вы опубликуете набор данных и отчет. Затем вы закрепите отчет на панели мониторинга. Как правило, панели мониторинга используются для просмотра определенной выборки данных в отчете. В вашем отчете есть только одна визуализация, но и с ней можно выполнить приведенные ниже шаги.

1. Откройте Power BI Desktop.

1. На вкладке **Home** (Главная) щелкните **Publish** (Опубликовать).

    ![Publish from Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Публикация из Power BI Desktop") (Публикация из Power BI Desktop)

1. Выберите рабочую область, в которой вы хотите опубликовать набор данных и отчет, а затем нажмите кнопку **Выбор**. На рисунке ниже выбран параметр по умолчанию **Моя рабочая область**.

    ![Выбор рабочей области для публикации набора данных и отчета](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Выбор рабочей области для публикации набора данных и отчета") 

1. Когда публикация успешно завершится, нажмите кнопку **Open 'BuildingTemperature.pbix' in Power BI** (Открыть BuildingTemperature.pbix в Power BI).

    ![Успешная публикация и ввод учетных данных](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Успешная публикация и ввод учетных данных") 

1. В службе Power BI щелкните ссылку, чтобы **ввести учетные данные**.

    ![Ввод учетных данных в службе Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Ввод учетных данных в службе Power BI")

1. Щелкните ссылку, чтобы **изменить учетные данные**.

    ![Изменение учетных данных в службе Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Изменение учетных данных в службе Power BI")

1. Введите данные учетной записи для входа в HDInsight и щелкните **Sign in** (Вход). Имя учетной записи по умолчанию — *admin*.

    ![Вход в кластер Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Вход в кластер Spark")

1. В левой области выберите **Рабочие области** > **Моя рабочая область** > **Отчеты**, а затем щелкните **BuildingTemperature**.

    ![Отчет в списке отчетов в области слева](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Отчет в списке отчетов в области слева")

    Вы также увидите **BuildingTemperature** в списке раздела **Наборы данных** в левой области.

    Визуальный элемент, созданный в Power BI Desktop, теперь доступен в службе Power BI. 

1. Наведите курсор на визуализацию и щелкните значок булавки в правом верхнем углу.

    ![Создание отчетов в службе Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Создание отчетов в службе Power BI")

1. Выберите "Новая информационная панель", введите имя `Building temperature`, а затем нажмите кнопку **Pin** (Закрепить).

    ![Закрепление на новой панели мониторинга](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Закрепление на новой панели мониторинга")

1. В отчете нажмите кнопку **Перейти к информационной панели**.

Ваш визуальный элемент закреплен на информационной панели. Вы можете добавить в отчет другие визуальные элементы, а затем закрепить их на этой же информационной панели. Дополнительные сведения об отчетах и информационных панелях см. в статьях [Отчеты в Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) и [Общие сведения о панелях мониторинга для разработчиков Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы с этим руководством кластер можно удалить. В случае с HDInsight ваши данные хранятся в службе хранилища Azure, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Так как затраты на кластер во много раз превышают затраты на хранилище, экономически целесообразно удалять неиспользуемые кластеры.

Инструкции по удалению кластера см. в статье [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI).

## <a name="next-steps"></a>Дополнительная информация

Из этого учебника вы узнали, как использовать [Microsoft Power BI](https://powerbi.microsoft.com/) для визуализации данных в кластере Apache Spark в [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Перейдите к следующей статье, чтобы создать приложение машинного обучения.

> [!div class="nextstepaction"]
> [Создание приложений машинного обучения Apache Spark в Azure HDInsight](./apache-spark-ipython-notebook-machine-learning.md)