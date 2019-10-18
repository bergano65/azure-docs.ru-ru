---
title: Визуализация данных Apache Hive с помощью Power BI в Azure HDInsight
description: Сведения о визуализации данных Hive, обрабатываемых Azure HDInsight, с помощью Microsoft Power BI.
keywords: hdinsight,hadoop,hive,интерактивный запрос,interactive hive,LLAP,odbc
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9b99e24d00441f0caab4b55a46e5ccc8be9cdd64
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529535"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Визуализация данных Apache Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight

Узнайте, как подключить Microsoft Power BI Desktop к Azure HDInsight с помощью ODBC и визуализировать Apache Hive данные.

> [!IMPORTANT]
> Можно использовать драйвер Hive ODBC для импорта с помощью универсального соединителя ODBC в Power BI Desktop. Но этот драйвер не рекомендуется для рабочих нагрузок бизнес-аналитик, с учетом того что ядро запросов Hive не является интерактивным. Для лучшей производительности используйте [соединитель интерактивных запросов HDInsight ](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) и [соединитель HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect).

В этой статье данные загружаются из таблицы `hivesampletable` Hive в Power BI. Эта таблица содержит некоторые данные об использовании мобильного телефона. Затем вы отобразите эти данные на карте мира:

![Отчет карты HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Эти сведения также относятся к новому типу кластера [интерактивных запросов](../interactive-query/apache-interactive-query-get-started.md). Сведения о подключении к HDInsight Interactive Query при помощи прямого запроса см. в статье [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive Interactive Query при помощи Microsoft Power BI с использованием прямого запроса в Azure HDInsight).

## <a name="prerequisites"></a>Технические условия

Для выполнения действий, указанных в этой статье, вам потребуется следующее:

* **Кластер HDInsight.** Это может быть кластер HDInsight с Hive или новый кластер интерактивных запросов. Сведения о создании кластеров см. в [этом разделе](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Копию этой программы можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Создание источника данных Hive ODBC

Дополнительные сведения см. в разделе [Создание источника данных Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Загрузка данных из HDInsight

Таблица Hive **hivesampletable** поставляется со всеми кластерами HDInsight.

1. Запустите Power BI Desktop.

1. В верхнем меню перейдите к **домашней**  > **получить данные**  > **другие..** ..

    ![Power BI открытие данных в HDInsight Excel](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. В диалоговом окне **Получение данных** выберите **другое** слева, выберите **ODBC** справа, а затем щелкните **подключить** в нижней части экрана.

1. В диалоговом окне **из ODBC** выберите имя источника данных, созданного в последнем разделе из раскрывающегося списка. Нажмите кнопку **ОК**.

1. При первом использовании откроется диалоговое окно **драйвера ODBC** . В меню слева выберите **значение по умолчанию или пользовательский** . Затем выберите **Подключиться** , чтобы открыть **Навигатор**.

1. В диалоговом окне **Навигатор** разверните узел **ODBC > Hive > умолчанию**, выберите **hivesampletable**и нажмите кнопку **загрузить**.

## <a name="visualize-data"></a>Визуализация данных

Продолжите из последней процедуры.

1. В области визуализации выберите **Map**, а это значок земного шара.

    ![Настройка отчета HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. В области **поля** выберите **Country** и **devicemake**. На карте отобразятся данные.

1. Разверните карту.

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Power BI.  Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Apache Hadoop с помощью Power Query](apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
