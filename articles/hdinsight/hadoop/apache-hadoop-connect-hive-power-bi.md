---
title: Визуализация данных Apache Hive с помощью Power BI в Azure HDInsight
description: Сведения о визуализации данных Hive, обрабатываемых Azure HDInsight, с помощью Microsoft Power BI.
keywords: hdinsight,hadoop,hive,интерактивный запрос,interactive hive,LLAP,odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 69353968f6b38f0d16b68c58b9b00c3e6d45850b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446875"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Визуализация данных Apache Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight

Сведения о подключении Microsoft Power BI Desktop для Azure HDInsight с использованием ODBC и визуализации данных с Apache Hive.

>[!IMPORTANT]
> Можно использовать драйвер Hive ODBC для импорта с помощью универсального соединителя ODBC в Power BI Desktop. Но этот драйвер не рекомендуется для рабочих нагрузок бизнес-аналитик, с учетом того что ядро запросов Hive не является интерактивным. Для лучшей производительности используйте [соединитель интерактивных запросов HDInsight ](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) и [соединитель HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect).

В этой статье вы загрузите данные из `hivesampletable` таблицу Hive для Power BI. Эта таблица содержит некоторые данные об использовании мобильного телефона. Затем вы отобразите эти данные на карте мира:

![Отчет карты HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Эти сведения также относятся к новому типу кластера [интерактивных запросов](../interactive-query/apache-interactive-query-get-started.md). Сведения о подключении к HDInsight Interactive Query при помощи прямого запроса см. в статье [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive Interactive Query при помощи Microsoft Power BI с использованием прямого запроса в Azure HDInsight).

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер HDInsight.** Это может быть кластер HDInsight с Hive или новый кластер интерактивных запросов. Сведения о создании кластеров см. в [этом разделе](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Копию этой программы можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Создание источника данных Hive ODBC

Дополнительные сведения см. в разделе [Создание источника данных Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Загрузка данных из HDInsight

Таблица Hive hivesampletable поставляется с кластерами HDInsight.

1. Запустите Power BI Desktop.

2. В верхнем меню, перейдите к **Главная** > **получить данные** > **более...** .

    ![Открытие данных HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Из **получить данные** диалоговое окно, выберите **других** слева, выберите **ODBC** справа налево, а затем выберите **Connect** внизу.

4. Из **из ODBC** диалоговое окно, выберите данные, вы создали в предыдущем разделе в раскрывающемся списке имя источника, а затем выберите **ОК**.

5. Из **Навигатор** диалоговом окне разверните **ODBC > HIVE > по умолчанию**выберите **hivesampletable**, а затем выберите **нагрузки**.

6. Из **драйвер ODBC** диалоговом окне выберите **стандартную или созданную**, а затем выберите **Connect**.

## <a name="visualize-data"></a>Визуализируйте данные

Продолжите из последней процедуры.

1. В области "Визуализации" выберите **Карта**.  Это значок глобуса.

    ![Настройка отчета HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Из **поля** области выберите **страны** и **devicemake**. На карте отобразятся данные.
3. Разверните карту.

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Power BI.  Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Apache Hadoop с помощью Power Query](apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
