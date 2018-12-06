---
title: Визуализация данных Hive из кластера Interactive Query с помощью Power BI в Azure HDInsight
description: Использование Microsoft Power BI для визуализации данных Interactive Query Hive из Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 68f2314b995eb0f2f67307b44cbfd177f5c5a796
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309276"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Визуализация данных Apache Hive из кластера Interactive Query с Microsoft Power BI с использованием прямого запроса в Azure HDInsight

Эта статья рассказывает о том, как подключить Microsoft Power BI к кластерам Interactive Query в Azure HDInsight и визуализировать данные Apache Hive с использованием прямого запроса. В этом примере выполняется загрузка данных из таблицы Hive с именем hivesampletable в Power BI. Таблица Hive hivesampletable содержит данные об использовании мобильных телефонов. Затем вы отобразите эти данные на карте мира:

![Отчет карты HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Можно использовать [драйвер ODBC Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) для импорта с помощью универсального соединителя ODBC в Power BI Desktop. Но этот драйвер не рекомендуется для рабочих нагрузок бизнес-аналитик, с учетом того что ядро запросов Hive не является интерактивным. Для лучшей производительности используйте [соединитель интерактивных запросов HDInsight ](./apache-hadoop-connect-hive-power-bi-directquery.md) и [соединитель HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect).

## <a name="prerequisites"></a>Предварительные требования
Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер HDInsight.** Это может быть кластер HDInsight с Apache Hive или новый кластер Interactive Query. Сведения о создании кластеров см. в [этом разделе](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Копию этой программы можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Загрузка данных из HDInsight

Таблица Hive hivesampletable поставляется с кластерами HDInsight.

1. Войдите в Power BI Desktop.

2. Перейдите на вкладку **Главная**, на ленте **Внешние данные** щелкните **Получить данные**, а затем выберите **Дополнительно**.

    ![Открытие данных HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
    
3. На панели **Получение данных** введите **hdinsight** в поле поиска. Если **HDInsight Interactive Query (Beta)** (HDInsight Interactive Query (бета-версия)) не отображается, необходимо обновить Power BI Desktop до последней версии.

4. Выберите **HDInsight Interactive Query (бета-версия)**, а затем нажмите кнопку **Подключиться**.

5. Нажмите кнопку **Продолжить**, чтобы закрыть диалоговое окно с предупреждением **Предварительная версия соединителя**.

6. В разделе **HDInsight Interactive Query** выберите или введите следующие сведения:

    - **Сервер**: введите имя кластера Interactive Query, например *myiqcluster.azurehdinsight.net*.

    - **База данных**: для работы с этим руководством введите **по умолчанию**.
    
    - **Режим подключения к данным**: для работы с этим руководством выберите **DirectQuery**.

    ![Интерактивный запрос HDInsight, подключение DirectQuery в Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Последовательно выберите **ОК**.

8. Введите учетные данные пользователя HTTP и нажмите кнопку **ОК**. Имя пользователя по умолчанию — **admin**.

9. В левой области выберите **hivesampletale**, а затем нажмите кнопку **Загрузить**.

    ![Интерактивный запрос HDInsight, таблица hivesampletable в Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Визуализация данных на карте

Продолжите из последней процедуры.

1. В области "Визуализации" выберите **Карта**.  Это значок глобуса.

    ![Настройка отчета HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
    
2. В области "Поля" выберите **страна** и **devicemake**. На карте отобразятся данные.

3. Разверните карту.

## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Power BI.  Дополнительные сведения о визуализации данных см. в следующих статьях:

* [Визуализация данных Apache Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md) 
* [Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](./../hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Apache Hadoop с помощью Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
