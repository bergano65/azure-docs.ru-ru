---
title: Подключение Excel к Apache Hadoop с помощью Power Query — Azure HDInsight
description: Узнайте, как пользоваться преимуществами компонентов бизнес-аналитики и применять Power Query для Excel для получения доступа к данным, хранящимся в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435808"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Подключение Excel к Apache Hadoop с помощью Power Query

Одной из ключевых особенностей решения Майкрософт для работы с большими данными является интеграция компонентов бизнес-аналитики Майкрософт с кластерами Apache Hadoop в службе Azure HDInsight. Важнейшим примером является возможность подключения Excel к учетной записи хранения Azure, в которой хранятся данные, связанные с кластером Hadoop, с помощью надстройки Microsoft Power Query для Excel. В этой статье приводится пошаговое руководство по настройке и использованию Power Query для запроса данных, связанных с кластером Hadoop, который управляется с помощью HDInsight.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).
* Рабочая станция, которая работает под управлением Windows 10, 7, Windows Server 2008 R2, или более поздняя операционная система.
* Office 2016, Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office профессиональный плюс 2010.

## <a name="install-microsoft-power-query"></a>Установка энергетического запроса Майкрософт

Power Query может импортировать данные, которые были выведены или созданы заданием Hadoop, выполняющимся в кластере HDInsight.

В Excel 2016 надстройка Power Query находится на вкладке "Данные" ленты в группе "Скачать & преобразовать". В предыдущих версиях Excel необходимо скачать надстройку Microsoft Power Query для Excel из [Центра загрузки Майкрософт](https://go.microsoft.com/fwlink/?LinkID=286689) и установить ее.

## <a name="import-hdinsight-data-into-excel"></a>Импорт данных HDInsight в Excel

Надстройка Power Query для Excel удобна для импорта данных из кластера HDInsight в Excel, где можно использовать средства бизнес-аналитики, такие как PowerPivot и Power Map, для изучения, анализа и представления данных.

1. Запуск Excel.

1. Создайте новую пустую книгу.

1. Выполните указанные ниже действия для вашей версии Excel.

   * Excel 2016

     * Выберите > **данные** > **получить данные** > **из Azure** > **из Azure HDInsight (HDFS)**.

       ![Hdi. Power''еry.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013 или 2010

     * Выберите **запрос** > на питание**из Azure** > **от Microsoft Azure HDInsight.**

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Примечание:** Если вы не видите меню **Power Query,** перейдите в**надстройки****опций** >  **файлов** > и выберите дополнения **COM** из окна **управления** выпадением в нижней части страницы. Нажмите кнопку **Перейти...** и убедитесь, что установлен флажок «Power Query для Excel».

       **Примечание:** Энергетический запрос также позволяет импортировать данные из HDFS, выбирая **из других источников.**

1. Из диалога **Azure HDInsight (HDFS)** в **названии учетной записи или текстовом** поле URL введите имя учетной записи хранения Azure Blob, связанной с кластером. Затем выберите **OK**. Это может быть учетная запись хранения по умолчанию или связанная учетная запись хранения.  Формат — `https://StorageAccountName.blob.core.windows.net/`.

1. Для **ключа учетной записи**введите ключ для учетной записи хранилища Blob, а затем выберите **Connect.** (Вводить данные учетной записи требуется только при первом доступе к этому магазину.)

1. В панели **Navigator** слева от редактора запроса дважды щелкните имя контейнера для хранения Blob, связанное с кластером. По умолчанию имя контейнера совпадает с именем кластера.

1. Найдите **HiveSampleData.txt** в столбце **Name** (путь папки **.. /hive/warehouse/hivesampletable/**), а затем выберите **Binary** слева от HiveSampleData.txt. HiveSampleData.txt поставляется вместе с кластером. При необходимости можно использовать собственный файл.

    ![Данные импорта запроса на мощность HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Если необходимо, можно переименовать имена столбцов. Когда вы будете готовы, выберите **Close & Load.**  Данные загружены в книгу.

    ![Импортная таблица запроса на мощность HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Дальнейшие действия

В этой статье было показано, как использовать Power Query для извлечения данных из HDInsight в Excel. Аналогичным образом можно извлекать данные из HDInsight в базу данных SQL Azure. Также можно загрузить данные в HDInsight. Дополнительные сведения см. в следующих статьях:

* [Визуализируйте данные Apache Hive с помощью Microsoft Power BI в Azure HDInsight.](apache-hadoop-connect-hive-power-bi.md)
* [Visualize Interactive Query Hive data with Microsoft Power BI using DirectQuery in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive из кластера Interactive Query с помощью Microsoft Power BI и DirectQuery в Azure HDInsight).
* [Используйте Apache Цеппелин для запуска запросов Apache Hive в Azure HDInsight.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Используйте инструмент Azure HDInsight для визуального кода studio.](../hdinsight-for-vscode.md)
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
