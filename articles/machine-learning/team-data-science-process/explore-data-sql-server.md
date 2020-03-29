---
title: Изучение данных на виртуальной машине сервера SQL Server — командный процесс обработки и анализа данных
description: Сведения о том, как просматривать данные, хранящиеся на виртуальной машине SQL Server в Azure, с помощью SQL или языка программирования, например Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720101"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Просмотр данных в виртуальной машине SQL Server на платформе Azure

В этой статье описывается, как просматривать данные, хранящиеся в виртуальной машине SQL Server на платформе Azure. Для изучения данных используйте S'L или Python.

Эта задача является одним из этапов [процесса обработки и анализа данных группы](overview.md).

> [!NOTE]
> В образцах инструкций SQL, содержащихся в данном документе, предполагается, что данные находятся на сервере SQL Server. Если это не так, обратитесь к карте обработки облачных данных, чтобы узнать, как переместить данные в SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Просмотр данных SQL с помощью сценариев SQL
Вот несколько примеров сценариев SQL, которые можно использовать для изучения хранилищ данных в SQL Server.

1. Получение количества наблюдений за день
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Получение уровней в столбце категорий 
   
    `select  distinct <column_name> from <databasename>`
3. Получение числа уровней в сочетании двух столбцов категорий 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Получение распределения для числовых столбцов
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Для практического примера можно использовать [набор данных о такси Нью-Йорка](https://www.andresmh.com/nyctaxitrips/) и статью IPNB под названием [Структурирование данных Нью-Йорка с помощью IPython Notebook и SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb), содержащую полное пошаговое руководство.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Просмотр данных SQL с помощью Python
Использование Python для изучения данных и генерации функций, когда данные находится в сервере S'L, аналогично обработке данных в Azure blob с помощью Python, как это описано в [данных Process Azure Blob в вашей среде науки о данных.](data-blob.md) Загрузите данные из базы данных в панды DataFrame, а затем могут быть обработаны дальше. В этом разделе задокументирован процесс подключения к базе данных и загрузки данных в кадр данных.

Для подключения к базе данных SQL Server из языка Python с использованием pyodbc можно применить следующий формат строки подключения (замените servername, dbname, username и password соответствующими значениями имени сервера, имени БД, имени пользователя и пароля):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Библиотека Pandas](https://pandas.pydata.org/) в Python предоставляет богатый набор структур данных и инструментов анализа данных для манипуляций с данными для программирования Python. Следующий код считывает результаты, возвращенные из базы данных SQL Server, в кадр данных Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Теперь можно работать с кадром данных Pandas, как описано в статье [Обработка больших двоичных данных Azure с применением методов расширенного анализа](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Пример применения командного процесса обработки и анализа данных на практике
Полноценный пошаговый пример применения процесса Cortana Analytics с использованием общедоступного набора данных см. в статье [Процесс обработки и анализа данных группы на практике: использование SQL Server](sql-walkthrough.md).

