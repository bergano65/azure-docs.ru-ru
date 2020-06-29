---
title: Руководство по Создание определения задания Apache Spark в Synapse Studio
description: Руководство. Использование Azure Synapse Analytics для создания определений заданий Spark и их отправки в пул Apache Spark для Azure Synapse Analytics.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: 3311a9a92cc5e63a6fa20e4dd0d2af00fdacc95c
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194490"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Руководство по Создание определения задания Apache Spark в Synapse Studio

В этом учебнике показано, как использовать Azure Synapse Studio для создания определений заданий Apache Spark и их отправки в пул Apache Spark.

В рамках этого руководства рассматриваются следующие задачи:

* Создание определения задания Apache Spark для PySpark (Python)
* Создание определения задания Apache Spark для Spark (Scala)
* Создание определения задания Apache Spark для .NET Spark (C#)
* Отправка определения задания Apache Spark в виде пакетного задания
* Добавление определения задания Apache Spark в конвейер

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к изучению этого руководства, убедитесь, что выполнены следующие требования.

* Рабочая область Azure Synapse Analytics. См. руководство по [созданию рабочей области Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Пул Apache Spark.
* Учетная запись хранения ADLS 2-го поколения. Вам потребуется роль владельца данных BLOB-объектов службы хранилища для файловой системы ADLS 2-го поколения, с которой вы хотите работать. Если у вас нет этой роли, добавьте разрешение вручную.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Создание определения задания Apache Spark для PySpark (Python)

В этом разделе рассказывается о том, как создать определение задания Apache Spark для PySpark (Python).

1. Откройте [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Вы можете перейти на страницу [примеров файлов для создания определений заданий Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) и скачать **wordcount.jar** и **shakespear.txt**, а затем отправить эти файлы в службу хранилища Azure. Щелкните вкладку **Данные**, выберите **Учетные записи хранения** и отправьте соответствующие файлы в файловую систему ADLS 2-го поколения. Пропустите этот шаг, если файлы уже находятся в службе хранилища Azure. 

     ![Отправка файла Python](./media/apache-spark-job-definitions/upload-python-file.png)

3. Щелкните центр **Разработка**, в области слева выберите **Spark job definitions** (Определения заданий Spark), нажмите узел действий "..." рядом с **определением задания Spark**, а затем в контекстном меню выберите **New Spark job definition** (Создать определение задания Spark).

     ![Создание нового определения для Python](./media/apache-spark-job-definitions/create-new-definition.png)

4. В главном окне определения задания Apache Spark из раскрывающегося списка языков выберите **PySpark (Python)** .

5. Введите сведения об определении задания Apache Spark. Пример сведений можно скопировать.

     |  Свойство   | Описание   |  
     | ----- | ----- |  
     |Имя определения задания| Введите имя определения задания Apache Spark. Это имя можно изменить в любое время до публикации. Образец. `job definition sample`|
     |Основной файл определения| Основной файл, используемый для задания. Выберите PY-файл в хранилище. Для отправки файла в учетную запись хранения можно выбрать **Отправить файл**. Образец. `abfss://azureportaldeploy@storageaccountname.dfs.core.windows.net/synapse/workspaces/workspace name/batchjobs/python/fileexists.py`|
     |Аргументы командной строки| Дополнительные аргументы для задания. Образец. `shakespeare.txt`|
     |Файлы ссылок| Дополнительные файлы, используемые для ссылки в основном файле определения. Для отправки файла в учетную запись хранения можно выбрать **Отправить файл**. Образец. `abfss://azureportaldeploy@storageaccountname.dfs.core.windows.net/synapse/workspaces/workspace name/batchjobs/python/shakespeare.txt`|
     |Пул Spark| Задание будет отправлено в выбранный пул Apache Spark.|
     |Версия Spark| Версия Apache Spark, которая используется в пуле Apache Spark.|
     |Исполнители| Количество исполнителей, которые будут предоставлены для задания в определенном пуле Apache Spark.|
     |Размер исполнителя| Количество ядер и памяти, которые будут использоваться для исполнителей, предоставленных для задания в определенном пуле Apache Spark.|  
     |Размер драйвера| Количество ядер и объем памяти, которые будут использоваться для драйвера, предоставленного для задания в указанном пуле Apache Spark.|

     ![Выбор значения для определения задания Spark для Python](./media/apache-spark-job-definitions/create-py-definition.png)

6. Выберите **Опубликовать**, чтобы сохранить определение задания Apache Spark.

     ![Публикация определения PY](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Создание определения задания Apache Spark для Apache Spark (Scala)

В этом разделе рассказывается о том, как создать определение задания Apache Spark для Apache Spark (Scala).

 1. Откройте [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Вы можете перейти на страницу [примеров файлов для создания определений заданий Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) и скачать **wordcount.jar** и **shakespear.txt**, а затем отправить эти файлы в службу хранилища Azure. Щелкните вкладку **Данные**, выберите **Учетные записи хранения** и отправьте соответствующие файлы в файловую систему ADLS 2-го поколения. Пропустите этот шаг, если файлы уже находятся в службе хранилища Azure. 
 
     ![Подготовка структуры Scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Щелкните центр **Разработка**, в области слева выберите **Spark job definitions** (Определения заданий Spark), нажмите узел действий "..." рядом с **определением задания Spark**, а затем в контекстном меню выберите **New Spark job definition** (Создать определение задания Spark).
     ![создание нового определения для Scala](./media/apache-spark-job-definitions/create-new-definition.png)

 4. В главном окне определения задания Apache Spark из раскрывающегося списка языков выберите **Spark (Scala)** .

 5. Введите сведения об определении задания Apache Spark. Пример сведений можно скопировать.

     |  Свойство   | Описание   |  
     | ----- | ----- |  
     |Имя определения задания| Введите имя определения задания Apache Spark. Это имя можно изменить в любое время до публикации. Образец. `job definition sample`|
     |Основной файл определения| Основной файл, используемый для задания. Выберите JAR-файл в хранилище. Для отправки файла в учетную запись хранения можно выбрать **Отправить файл**. Образец. `abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/wordcount.jar`|
     |Имя главного класса| Полный идентификатор или основной класс, который находится в основном файле определения. Образец. `WordCount`|
     |Аргументы командной строки| Дополнительные аргументы для задания. Образец. `abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/shakespeare.txt abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/result`|
     |Файлы ссылок| Дополнительные файлы, используемые для ссылки в основном файле определения. Для отправки файла в учетную запись хранения можно выбрать **Отправить файл**.|
     |Пул Spark| Задание будет отправлено в выбранный пул Apache Spark.|
     |Версия Spark| Версия Apache Spark, которая используется в пуле Apache Spark.|
     |Исполнители| Количество исполнителей, которые будут предоставлены для задания в определенном пуле Apache Spark.|  
     |Размер исполнителя| Количество ядер и памяти, которые будут использоваться для исполнителей, предоставленных для задания в определенном пуле Apache Spark.|
     |Размер драйвера| Количество ядер и объем памяти, которые будут использоваться для драйвера, предоставленного для задания в указанном пуле Apache Spark.|

     ![Выбор значения для определения задания Spark для Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. Выберите **Опубликовать**, чтобы сохранить определение задания Apache Spark.

     ![Публикация определения Scala](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkc"></a>Создание определения задания Apache Spark для .NET Spark (C#)

В этом разделе рассказывается о том, как создать определение задания Apache Spark для .NET Spark (C#).
 1. Откройте [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Вы можете перейти на страницу [примеров файлов для создания определений заданий Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) и скачать **wordcount.zip** и **shakespear.txt**, а затем отправить эти файлы в службу хранилища Azure. Щелкните вкладку **Данные**, выберите **Учетные записи хранения** и отправьте соответствующие файлы в файловую систему ADLS 2-го поколения. Пропустите этот шаг, если файлы уже находятся в службе хранилища Azure. 

     ![Подготовка структуры .NET](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Щелкните центр **Разработка**, в области слева выберите **Spark job definitions** (Определения заданий Spark), нажмите узел действий "..." рядом с **определением задания Spark**, а затем в контекстном меню выберите **New Spark job definition** (Создать определение задания Spark).

     ![Создание нового определения для .NET](./media/apache-spark-job-definitions/create-new-definition.png)

 4. В главном окне определения задания Apache Spark из раскрывающегося списка языков выберите **.NET Spark (C#/F#)** .

 5. Введите сведения об определении задания Apache Spark. Пример сведений можно скопировать.
     |  Свойство   | Описание   |  
     | ----- | ----- |  
     |Имя определения задания| Введите имя определения задания Apache Spark. Это имя можно изменить в любое время до публикации. Образец. `job definition sample`|
     |Основной файл определения| Основной файл, используемый для задания. Выберите из хранилища ZIP-файл, содержащий приложение .NET для Apache Spark (то есть основной исполняемый файл, библиотеки DLL с пользовательскими функциями и другие необходимые файлы). Для отправки файла в учетную запись хранения можно выбрать **Отправить файл**. Образец. `abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/wordcount.zip`|
     |Основной исполняемый файл| Основной исполняемый файл — это основной ZIP-файл определения. Образец. `WordCount`|
     |Аргументы командной строки| Дополнительные аргументы для задания. Образец. `abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/shakespeare.txt abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/result`|
     |Файлы ссылок| Дополнительные файлы, необходимые рабочим узлам для запуска приложения .NET для Apache Spark, не включенные в основной ZIP-файл определения (то есть зависимые JAR-файлы, дополнительные библиотеки DLL с пользовательскими функциями и другие файлы конфигурации). Для отправки файла в учетную запись хранения можно выбрать **Отправить файл**.|
     |Пул Spark| Задание будет отправлено в выбранный пул Apache Spark.|
     |Версия Spark| Версия Apache Spark, которая используется в пуле Apache Spark.|
     |Исполнители| Количество исполнителей, которые будут предоставлены для задания в определенном пуле Apache Spark.|  
     |Размер исполнителя| Количество ядер и памяти, которые будут использоваться для исполнителей, предоставленных для задания в определенном пуле Apache Spark.|
     |Размер драйвера| Количество ядер и объем памяти, которые будут использоваться для драйвера, предоставленного для задания в указанном пуле Apache Spark.|

     ![Выбор значения для определения задания Spark для .NET](./media/apache-spark-job-definitions/create-net-definition.png)

 6. Выберите **Опубликовать**, чтобы сохранить определение задания Apache Spark.

      ![Публикация определения .NET](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Отправка определения задания Apache Spark в виде пакетного задания

Созданное определение задания Apache Spark можно отправить в пул Apache Spark. Убедитесь, что вы являетесь владельцем данных BLOB-объектов службы хранилища для файловой системы ADLS 2-го поколения, с которой вы хотите работать. Если у вас нет этой роли, добавьте разрешение вручную.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Сценарий 1. Отправка определения заданий Apache Spark
 1. Откройте окно определения задания Apache Spark, щелкнув его.

      ![Открытие определения задания Spark для отправки ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Щелкните значок **отправить**, чтобы отправить проект в выбранный пул Apache Spark. Вы можете щелкнуть вкладку **Spark monitoring URL** (URL-адрес мониторинга Spark), чтобы просмотреть LogQuery приложения Apache Spark.

    ![Нажмите кнопку "Отправить", чтобы отправить определение задания Spark.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Сценарий 2. Просмотр хода выполнения задания Apache Spark

 1. Щелкните вкладку **Монитор** и выберите **Приложения Spark**. В списке будет отправленное приложение Apache Spark.

     ![Просмотр приложения Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Щелкните приложение Apache Spark. Отобразится окно **LogQuery**. Ход выполнения задания можно просмотреть в окне **LogQuery**.
     
     ![Просмотр LogQuery приложения Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Сценарий 3. Проверка выходного файла

 1. Щелкните вкладку **Данные** и выберите **Учетные записи службы хранилища**. После успешного выполнения можно переходить к хранилищу ADLS 2-го поколения, чтобы проверить выходные файлы.

     ![Просмотр выходного файла](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Добавление определения задания Apache Spark в конвейер

В этом сценарии определение задания Apache Spark добавляется в конвейер.

 1. Откройте имеющееся определение задания Apache Spark.

 2. Щелкните значок в правом верхнем углу определения задания Apache Spark, выберите **Existing Pipeline** (Существующий конвейер) или **New pipeline** (Новый конвейер). Дополнительные сведения см. на странице Pipeline (Конвейер).

     ![Добавление в конвейер](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Добавление в конвейер](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике показано, как использовать Azure Synapse Studio для создания определений заданий Apache Spark и их отправки в пул Apache Spark. Далее вы можете использовать Azure Synapse Studio для создания наборов данных Power BI и управления такими данными.

