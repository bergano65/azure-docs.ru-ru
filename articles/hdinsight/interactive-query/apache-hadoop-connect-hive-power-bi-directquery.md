---
title: Визуализация данных Hive из кластера Interactive Query с помощью Power BI в Azure HDInsight
description: Использование Microsoft Power BI для визуализации данных Interactive Query Hive из Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: fb4e16c8be5344c5b9947758b6a09845b470196d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800994"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Визуализация данных Apache Hive из кластера Interactive Query с Microsoft Power BI с использованием прямого запроса в Azure HDInsight

Эта статья рассказывает о том, как подключить Microsoft Power BI к кластерам Interactive Query в Azure HDInsight и визуализировать данные Apache Hive с использованием прямого запроса. В примере данные загружаются из `hivesampletable` таблицу Hive для Power BI. `hivesampletable` Таблица Hive содержит некоторые данные об использовании мобильного телефона. Затем вы отобразите эти данные на карте мира:

![Отчет карты HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Можно использовать [драйвер ODBC Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) для импорта с помощью универсального соединителя ODBC в Power BI Desktop. Но этот драйвер не рекомендуется для рабочих нагрузок бизнес-аналитик, с учетом того что ядро запросов Hive не является интерактивным. Для лучшей производительности используйте [соединитель интерактивных запросов HDInsight ](./apache-hadoop-connect-hive-power-bi-directquery.md) и [соединитель HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect).

## <a name="prerequisites"></a>Технические условия
Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер HDInsight.** Это может быть кластер HDInsight с Apache Hive или новый кластер Interactive Query. Сведения о создании кластеров см. в [этом разделе](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Копию этой программы можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Загрузка данных из HDInsight

`hivesampletable` Таблицы Hive в состав всех кластеров HDInsight.

1. Запустите Power BI Desktop.

2. В строке меню перейдите к **Главная** > **получить данные** > **более...** .

    ![Открытие данных HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Из **получить данные** окне введите **hdinsight** в поле поиска.  

4. В результатах поиска выберите **HDInsight Interactive Query**, а затем выберите **Connect**.  Если вы не видите **HDInsight Interactive Query**, необходимо обновить Power BI Desktop до последней версии.

5. Выберите **Продолжить** закрыть **подключение к сторонней службе** диалоговое окно.

6. В **HDInsight Interactive Query** окно, введите следующие сведения, а затем выберите **ОК**:

    |Свойство | Value |
    |---|---|
    |сервер; |Введите имя кластера, например *myiqcluster.azurehdinsight.net*.|
    |База данных |Введите **по умолчанию** для этой статьи.|
    |Режим подключения к данным |Выберите **DirectQuery** для этой статьи.|

    ![Интерактивный запрос HDInsight, подключение DirectQuery в Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Введите учетные данные HTTP, а затем выберите **Connect**. Имя пользователя по умолчанию — **admin**.

8. Из **Навигатор** окно в левой области выберите **hivesampletale**.

9. Выберите **нагрузки** в главном окне.

    ![Интерактивный запрос HDInsight, таблица hivesampletable в Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Визуализация данных на карте

Продолжите из последней процедуры.

1. Из панели визуализации, выберите **карты**, значок глобуса. Универсальное сопоставление откроется в главном окне.

    ![Настройка отчета HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. В области "Поля" выберите **страна** и **devicemake**. В главном окне появится сопоставление мир с точками данных, через несколько секунд.

3. Разверните карту.

## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Microsoft Power BI.  Дополнительные сведения о визуализации данных см. в следующих статьях:

* [Визуализация данных Apache Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md) 
* [Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Apache Hadoop с помощью Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
