---
title: Смотрите интерактивные данные о запросе С помощью Power BI в Azure HDInsight
description: Использование Microsoft Power BI для визуализации данных Interactive Query Hive из Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367998"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Визуализируйте интерактивные данные Apache Hive с помощью Microsoft Power BI с помощью прямого запроса в HDInsight

Эта статья рассказывает о том, как подключить Microsoft Power BI к кластерам Interactive Query в Azure HDInsight и визуализировать данные Apache Hive с использованием прямого запроса. В приведенном примере приводится загрузка данных из таблицы `hivesampletable` Hive в Power BI. Таблица `hivesampletable` Hive содержит некоторые данные об использовании мобильного телефона. Затем вы отобразите эти данные на карте мира:

![Отчет карты HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Можно использовать [драйвер ODBC Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) для импорта с помощью универсального соединителя ODBC в Power BI Desktop. Но этот драйвер не рекомендуется для рабочих нагрузок бизнес-аналитик, с учетом того что ядро запросов Hive не является интерактивным. Для лучшей производительности используйте [соединитель интерактивных запросов HDInsight ](./apache-hadoop-connect-hive-power-bi-directquery.md) и [соединитель HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect).

## <a name="prerequisites"></a>Предварительные требования
Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер HDInsight.** Это может быть кластер HDInsight с Apache Hive или новый кластер Interactive Query. Сведения о создании кластеров см. в [этом разделе](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Копию этой программы можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Загрузка данных из HDInsight

Таблица `hivesampletable` Hive поставляется со всеми кластерами HDInsight.

1. Запустите Power BI Desktop.

2. Из меню бар, перейдите на **дом** > **Получить данные** > **Подробнее ...**.

    ![HDInsight Power BI Получить данные Подробнее](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Из окна **Get Data** введите **hdinsight** в поле поиска.  

4. Из результатов поиска выберите **интерактивный запрос HDInsight,** а затем выберите **Connect.**  Если вы не видите **интерактивный запрос HDInsight,** вам необходимо обновить рабочий стол Power BI desktop до последней версии.

5. Выберите **Продолжить,** чтобы закрыть **Подключение к диалогу сторонних служб.**

6. В окне **интерактивного запроса HDInsight** введите следующую информацию, а затем выберите **OK:**

    |Свойство | Значение |
    |---|---|
    |Сервер |Введите имя кластера, например, *myiqcluster.azurehdinsight.net*.|
    |База данных |Введите **значение по умолчанию** для этой статьи.|
    |Режим подключения к данным |Выберите **Прямую Кевири** для этой статьи.|

    ![Интерактивный запрос HDInsight, подключение DirectQuery в Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Введите учетные данные HTTP, а затем выберите **Connect.** Имя пользователя по умолчанию — **admin**.

8. Из окна **Навигатора** в левом стекле выберите **hivesampletale.**

9. Выберите **Загрузку** из основного окна.

    ![Интерактивный запрос HDInsight, таблица hivesampletable в Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Визуализация данных на карте

Продолжите из последней процедуры.

1. Из панели визуализаций выберите **карту**, значок глобуса. Затем в главном окне отображается общая карта.

    ![Настройка отчета HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. В области "Поля" выберите **страна** и **devicemake**. Через несколько мгновений в главном окне появляется карта мира с точками данных.

3. Разверните карту.

## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Microsoft Power BI.  Дополнительные сведения о визуализации данных см. в следующих статьях:

* [Визуализируйте данные Apache Hive с помощью Microsoft Power BI с помощью ODBC в Azure HDInsight.](../hadoop/apache-hadoop-connect-hive-power-bi.md) 
* [Используйте Apache Цеппелин для запуска запросов Apache Hive в Azure HDInsight.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключите Excel к Apache Hadoop с помощью power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Используйте инструмент Azure HDInsight для визуального кода studio.](../hdinsight-for-vscode.md)
* [Загрузка данных в HDInsight](./../hdinsight-upload-data.md).
