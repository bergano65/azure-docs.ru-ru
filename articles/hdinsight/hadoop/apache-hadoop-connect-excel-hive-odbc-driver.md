---
title: Подключение Excel к Apache Hadoop с помощью драйвера Hive ODBC в Azure HDInsight
description: Узнайте, как установить и использовать драйвер Microsoft Hive ODBC для Excel, чтобы запрашивать данные в кластерах HDInsight из Microsoft Excel.
keywords: hadoop excel, hive excel, hive odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: hrasheed
ms.openlocfilehash: 3a47b18051036e925e54b9507bf2cb4e40aad844
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202532"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Подключение Excel к Apache Hadoop с помощью драйвера Microsoft Hive ODBC в Azure HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Решение Майкрософт для работы с данными большого размера интегрирует компоненты бизнес-аналитики Майкрософт с кластерами Apache Hadoop, которые были развернуты в Azure HDInsight. Примером такой интеграции является возможность подключения Excel к хранилищу данных Hive на кластере Hadoop в HDInsight с помощью драйвера Microsoft Hive ODBC.

Можно также подключить данные, связанные с кластером HDInsight, и другими источниками данных, включая другие кластеры Hadoop (не HDInsight) из Excel с использованием надстройки Microsoft Power Query для Excel. Сведения об установке и использовании Power Query см. в статье [Подключение Excel к Hadoop с помощью Power Query][hdinsight-power-query].


## <a name="prerequisites"></a>Технические условия

Перед началом работы с этой статьей необходимо иметь следующее:

* Кластер HDInsight Hadoop. Дополнительные сведения о создании кластера см. в статье [Приступая к работе с Hadoop в HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Рабочая станция с Office 2010 Professional Plus или более поздней версии или Excel 2010 или более поздней версии.

## <a name="install-microsoft-hive-odbc-driver"></a>Установка драйвера Microsoft Hive ODBC
Скачайте и установите версию [Microsoft Hive ODBC Driver][hive-odbc-driver-download], которая соответствует версии приложения, где будет использоваться драйвер ODBC.  В рамках этого руководства используется драйвер для Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Создание источника данных Apache Hive ODBC
Ниже показано, как создать источник данных Hive ODBC.

1. В Windows откройте "Пуск > Средства администрирования Windows > Источники данных ODBC" (32-разрядная или 64-разрядная версия).  В результате откроется окно **Администратор источников данных ODBC**.
   
    ![Администратор источников данных ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Настройка DSN с помощью администратора источников данных ODBC")

2. На вкладке **DSN пользователя** выберите **Добавить**, чтобы открыть окно **Создание нового источника данных**.

3. Выберите **Microsoft Hive ODBC Driver**, а затем — **Готово**, чтобы открыть окно **Microsoft Hive ODBC Driver DSN Setup** (Настройка DSN Microsoft Hive ODBC Driver).

4. Введите или выберите следующие значения:
   
   | Свойство | ОПИСАНИЕ |
   | --- | --- |
   |  Имя источника данных |Присвойте имя источнику данных |
   |  Узлы |Введите &lt;имя_кластера_HDInsight&gt;.azurehdinsight.net. Например, myHDICluster.azurehdinsight.net |
   |  Порт |Используйте <strong>443</strong>. (Этот порт был изменен с 563 на 443.) |
   |  База данных |Используйте <strong>значение по умолчанию</strong>. |
   |  Механизм |Выберите <strong>Служба Azure HDInsight</strong>. |
   |  Имя пользователя |Введите имя пользователя HTTP кластера HDInsight. Имя пользователя по умолчанию — <strong>admin</strong>. |
   |  Пароль |Введите пароль пользователя кластера HDInsight. |

   
5. Необязательно: Выберите **Дополнительные параметры...**  
   
   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   |  Использовать исходный запрос |При выборе этого параметра драйвер ODBC НЕ пытается преобразовать TSQL в HiveQL. Следует использовать только при 100% уверенности в отправке действительных инструкций HiveQL. При подключении к серверу SQL Server или базе данных Azure SQL необходимо снять этот флажок. |
   |  Строки, загружаемые для каждого блока |При получении большого объема записей включение этого параметра может обеспечить оптимальную производительность. |
   |  Длина столбца строки по умолчанию, длина столбца двоичного кода, масштаб столбца десятичных значений |Длина и точность типа данных может повлиять на способ выведения данных. Это приведет к возврату недопустимой информации из-за потери точности и (или) усечения. |

    ![Дополнительные параметры](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Дополнительные параметры конфигурации DSN")

5. Щелкните **Тест** для проверки источника данных. При правильной настройке источника данных в результатах теста отображается **Успешно!**.  

6. Нажмите кнопку **ОК**, чтобы закрыть окно тестов.  

7. Нажмите кнопку **ОК**, чтобы закрыть окно **Microsoft Hive ODBC Driver DSN Setup** (Настройка DSN Microsoft Hive ODBC Driver).  

8. Нажмите кнопку **ОК**, чтобы закрыть окно **Администратор источников данных ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Импорт данных в Excel из службы HDInsight
Ниже описан способ импорта данных из таблицы Hive в рабочую книгу Excel с помощью источника данных ODBC, созданного в предыдущем разделе.

1. Откройте новую или существующую рабочую книгу в Excel.

2. На вкладке **Данные** перейдите к разделу **Получить данные** > **Из других источников** > **Из ODBC**, чтобы открыть окно **Из ODBC**.
   
    ![Открытие мастера подключения к данным](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Открытие мастера подключения к данным")

3. Из раскрывающегося списка выберите имя источника данных, который вы создали в предыдущем разделе, и щелкните **ОК**.

4. Введите имя пользователя Hadoop (имя по умолчанию — admin) и пароль, а затем выберите **Подключить**, чтобы открыть окно **Навигатор**.

5. В окне **Навигатор** перейдите к **HIVE** > **по умолчанию** > **hivesampletable**, а затем нажмите кнопку **Загрузить**. Для импорта данных в Excel потребуется несколько секунд.

    ![Навигатор ODBC Hive в HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Открытие мастера подключения данных")

## <a name="next-steps"></a>Дальнейшие действия
В рамках этой статьи вы узнали, как получить данные из службы HDInsight в Excel с помощью драйвера Microsoft Hive ODBC. Аналогичным образом можно получать данные из службы HDInsight в базу данных SQL. Можно также передавать данные в службу HDInsight. Дополнительные сведения см. на следующих ресурсах:

* [Визуализация данных Apache Hive с помощью Microsoft Power BI в Azure HDInsight](apache-hadoop-connect-hive-power-bi.md)
* [Visualize Interactive Query Hive data with Microsoft Power BI using DirectQuery in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive из кластера Interactive Query с помощью Microsoft Power BI и DirectQuery в Azure HDInsight).
* [Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](./../hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Apache Hadoop с помощью Power Query](apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698


