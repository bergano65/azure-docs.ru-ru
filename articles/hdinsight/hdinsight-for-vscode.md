---
title: Azure HDInsight для визуального кода студии
description: Узнайте, как использовать Spark & Hive Tools (Azure HDInsight) для Visual Studio Code для создания и отправки запросов и скриптов.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435686"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Используйте Spark & Hive Tools для визуального кода студии

Узнайте, как использовать Spark & Hive Tools для Visual Studio Code для создания и отправки рабочих мест apache Hive, интерактивных запросов Hive и скриптов PySpark для Apache Spark. Сначала мы остановимся, как установить Spark & Hive Tools в Visual Studio Code, а затем мы пройдемся по тому, как представить рабочие места Spark & Hive Tools.  

Spark & Hive Tools можно установить на платформах, поддерживаемых Visual Studio Code, включая Windows, Linux и macOS. Обратите внимание на следующие предпосылки для различных платформ.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения действий, описанных в этой статье, необходимо следующее:

- Кластер Azure HDInsight. Сведения о создании кластера см. в статье о [начале работы с HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Или используйте кластер Spark and Hive, который поддерживает конечную точку Apache Livy.
- [Визуальный код студии](https://code.visualstudio.com/).
- [Моно](https://www.mono-project.com/docs/getting-started/install/). Mono требуется только для Linux и macOS.
- [Интерактивная среда PySpark для Visual Studio Code.](set-up-pyspark-interactive-environment.md)
- Местный каталог. В этой статье используется **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Установка Spark & Hive Tools

После выполнения предпосылок можно установить Spark & Hive Tools для визуального кода Studio, выполнив следующие действия:

1. Откройте Visual Studio Code.

2. Из панели меню перейдите к **View** > **Extensions.**

3. В поле поиска введите **Spark & Hive**.

4. Выберите **Spark & Hive Tools** из результатов поиска, а затем выберите **Установить:**

   ![Искра & Hive для установки Visual Studio Code Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Выберите Перезагрузить при **необходимости.**

## <a name="open-a-work-folder"></a>Открыть рабочую папку

Чтобы открыть рабочую папку и создать файл в Visual Studio Code, выполните следующие действия:

1. Из меню бар, перейти к **файлу** > **Открыть Фолдер ...**  >  **C: HD-HDexample**, а затем выберите кнопку **Select Folder.** Папка отображается в представлении **проводника** слева.

2. В представлении **Explorer** выберите папку **HDexample,** а затем выберите значок **нового файла** рядом с папкой работы:

   ![визуальный студийный код нового значка файла](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Назовите новый `.hql` файл, используя либо (запросы Hive) или `.py` расширение файла Spark script. В этом примере используется файл **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Настройка среды Azure

Для пользователя общенационального облака выполните следующие действия, чтобы сначала настроить среду Azure, а затем использовать команду **Azure: Sign In,** чтобы войти в Azure:

1. Перейдите к**настройкам****предпочтений** >  **файлов.** > 
2. Поиск по следующей строке: **Azure: Облако**.
3. Выберите национальное облако из списка:

   ![Задание конфигурации записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Подключение к учетной записи Azure

Прежде чем отправлять скрипты в кластеры из Visual Studio Code, необходимо либо подключиться к учетной записи Azure, либо связать кластер (используя имя пользователя и учетные данные Apache Ambari или учетную запись, связанную с доменом). Выполните следующие действия, чтобы подключиться к Azure:

1. Из панели меню перейдите к **просмотру** > **палитры команд...**, и введите **Azure: Войти в**:

    ![Искра & Hive Инструменты для визуальной студии код входа](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Следуйте инструкциям по входе в Azure. После подключения имя учетной записи Azure отображается в строке состояния в нижней части окна кода Visual Studio.  

## <a name="link-a-cluster"></a>Связывание кластера

### <a name="link-azure-hdinsight"></a>Ссылка: Azure HDInsight

Вы можете связать нормальный кластер с помощью [apache Ambari-управляемых](https://ambari.apache.org/)имя пользователя, или вы можете связать кластер безопасности Enterprise Security Hadoop с помощью имени пользователя домена (например: `user1@contoso.com`).

1. Из меню бар, перейдите **к Просмотр** > **команды палитра ...**, и введите **Spark / Улей: Ссылка кластера**.

   ![Команда кластера связи Командной палитре](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Выберите связанный тип кластера **Azure HDInsight**.

3. Введите URL-адрес кластера HDInsight.

4. Введите имя пользователя Ambari; по умолчанию является **админ**.

5. Введите пароль Ambari.

6. Выберите тип кластера.

7. Установите имя дисплея кластера (необязательно).

8. Просмотрите представление **OUTPUT** для проверки.

   > [!NOTE]  
   > Связанное имя пользователя и пароль используются, если кластер вошел в подписку Azure и связал кластер.  

### <a name="link-generic-livy-endpoint"></a>Ссылка: Общие Livy конечная точка

1. Из меню бар, перейдите **к Просмотр** > **команды палитра ...**, и введите **Spark / Улей: Ссылка кластера**.

2. Выберите связанный тип кластера **Generic Livy Endpoint** (Общая конечная точка Livy).

3. Введите общую конечную точку Livy. Например:\:http//10.172.41.42:18080.

4. Выберите тип авторизации **Базовый** или **Нет**.  Если вы выберете **Basic:**  
    &emsp;а. Введите имя пользователя Ambari; по умолчанию является **админ**.  
    &emsp;Б. Введите пароль Ambari.

5. Просмотрите представление **OUTPUT** для проверки.

## <a name="list-clusters"></a>список кластеров

1. Из меню бар, перейдите **к Просмотр** > **команды палитра ...**, и введите **Spark / Улей: Список кластера**.

2. Выберите пожелавшую подписку.

3. Просмотрите представление **OUTPUT**. Это представление показывает связанные кластеры (или кластеры) и все кластеры под подпиской Azure:

    ![Настройка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Установка кластера по умолчанию

1. Повторно откройте папку **HDexample,** которая обсуждалась [ранее,](#open-a-work-folder)если она закрыта.  

2. Выберите файл **HelloWorld.hql,** который был создан [ранее.](#open-a-work-folder) Он открывается в редакторе скрипта.

3. Нажмите правой кнопкой мыши редактор скрипта, а затем выберите **Spark / Hive: Установить кластер по умолчанию**.  

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще этого не сделали.

5. Выберите кластер в качестве используемого по умолчанию для текущего файла скрипта. Инструменты автоматически обновляют **. Файл конфигурации VSCode-settings.json:**

   ![Настройка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Отправка интерактивных запросов Hive и пакетных скриптов Hive

С помощью Spark & Hive Tools for Visual Studio Code вы можете отправлять интерактивные запросы Hive и пакетные скрипты Hive в кластеры.

1. Повторно откройте папку **HDexample,** которая обсуждалась [ранее,](#open-a-work-folder)если она закрыта.  

2. Выберите файл **HelloWorld.hql,** который был создан [ранее.](#open-a-work-folder) Он открывается в редакторе скрипта.

3. Копируйте и вставьте следующий код в файл Hive, а затем сохраните его:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще этого не сделали.

5. Нажмите на редактор скрипта и выберите **Hive: Interactive,** чтобы отправить запрос, или используйте ярлык клавиатуры Ctrl-Alt-I.  Выберите **Hive: Пакет** для отправки скрипта или используйте ярлык клавиатуры Ctrl-Alt-H.  

6. Если не указан кластер по умолчанию, выберите кластер. Инструменты также позволяют отправлять блок кода вместо всего файла скрипта с помощью контекстного меню. Через несколько мгновений результаты запроса отображаются в новой вкладке:

   ![Интерактивный результат запроса Apache Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTS** панель: Вы можете сохранить весь результат в виде CSV, JSON, или Excel файл на локальном пути или просто выбрать несколько строк.

    - **ПАНЕЛЬ MESSAGES:** При выборе номера **строки** он переходит к первой строке работаемого скрипта.

## <a name="submit-interactive-pyspark-queries"></a>Отправка интерактивных запросов PySpark

Чтобы отправить интерактивные запросы PySpark, выполните следующие действия:

1. Повторно откройте папку **HDexample,** которая обсуждалась [ранее,](#open-a-work-folder)если она закрыта.  

2. Создайте новый **файл HelloWorld.py,** следуя [более ранним](#open-a-work-folder) шагам.

3. Скопируйте и вставьте в файл скрипта следующий код:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще этого не сделали.

5. Выберите весь код, нажмите вправо на редактор скрипта и выберите **Spark: PySpark Interactive,** чтобы отправить запрос. Или используйте ярлык Ctrl-Alt-I.

   ![контекстное меню интерактивной среды pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Выберите кластер, если не указан кластер по умолчанию. Через несколько мгновений результаты **Python Interactive** отображаются в новой вкладке. Инструменты также позволяют отправлять блок кода вместо всего файла скрипта с помощью контекстного меню:

   ![интерактивная среда pyspark, интерактивное окно python](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Введите **информацию %%,** а затем нажмите Shift-Enter, чтобы просмотреть информацию о заданиях (необязательно):

   ![pyspark интерактивный вид информации о работе](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Инструмент также поддерживает запрос **Spark S'L:**

   ![Результат интерактивного просмотра Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Статус представления отображается слева от панели нижнего статуса при запуске запросов. Если отображается состояние **PySpark Kernel (busy)** (Ядро PySpark (занято)), не отправляйте другие запросы.  

   > [!NOTE]
   >
   > При **удалении расширения Python** в настройках (он выбран по умолчанию) в представленных результатах взаимодействия pyspark будет использоваться старое окно:
   >
   > ![интерактивная среда pyspark, расширение python отключено](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Отправка пакетного задания PySpark

1. Повторно откройте папку **HDexample,** которую вы обсуждали [ранее,](#open-a-work-folder)если они закрыты.  

2. Создайте новый **файл BatchFile.py,** выдвивая [более ранние](#open-a-work-folder) шаги.

3. Скопируйте и вставьте в файл скрипта следующий код:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще этого не сделали.

5. Нажмите на редактор скрипта, а затем выберите **Spark: PySpark Batch**или используйте ярлык клавиатуры Ctrl-Alt-H.

6. Выберите кластер, чтобы отправить задание PySpark:

   ![Отправка результатов результатов работы Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

После отправки задания Python журналы отправки отображаются в окне **вывода** в Visual Studio Code. Также отображаются URL-адрес искрометный uI Spark и URL-адрес yarn. Вы можете открыть этот URL-адрес в браузере для отслеживания состояния задания.

## <a name="apache-livy-configuration"></a>Конфигурация Apache Livy

Поддерживается конфигурация [Apache Livy.](https://livy.incubator.apache.org/) Вы можете настроить его в **. Файл VSCode-settings.json** в папке рабочей области. Сейчас конфигурация Livy поддерживает только скрипт Python. Для получения более подробной информации, [см.](https://github.com/cloudera/livy/blob/master/README.rst )

<a id="triggerlivyconf"></a>**Как вызвать конфигурацию Livy**

Метод 1  
1. Из панели меню перейдите в > **настройки настройки** **файлов.** > **Preferences**
2. В поле **настройки поиска,** введите **HDInsight Работа Представление: Livy Conf**.  
3. Выберите **Изменить в settings.json** для соответствующего результата поиска.

Метод 2 Отправить файл и заметить, что папка .vscode автоматически добавляется в рабочую папку. Вы можете увидеть конфигурацию Livy, выбрав **.vscode'settings.json.**

+ Параметры проекта:

    ![КОНФИГУРАЦИя HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Для **параметров driverMemory** и **executorMemory** установите значение и единицу. Например: 1g или 1024m.

+ Поддерживаемые конфигурации Livy:

    **POST /batches** Запрос тела

    | name | description | type |
    | :- | :- | :- |
    | файл | Файл, содержащий приложение для выполнения | Путь (требуется) |
    | proxyUser | Пользователь, олицетворяемый при выполнении задания | Строка |
    | className | Класс main приложения в Java/Spark | Строка |
    | args | Аргументы командной строки для приложения | Список строк |
    | jars | Банки, которые будут использоваться в этой сессии | Список строк | 
    | pyFiles | Файлы Python для использования в этом сеансе | Список строк |
    | files | Файлы, которые будут использоваться в этом сеансе | Список строк |
    | driverMemory | Объем памяти, используемый для процесса драйвера | Строка |
    | driverCores | Число ядер, используемых для процесса драйвера | Int |
    | executorMemory | Объем памяти, используемый для каждого процесса исполнителя | Строка |
    | executorCores | Число ядер, используемых для каждого исполнителя | Int |
    | numExecutors | Число исполнителей, которые должны быть запущены для этого сеанса | Int |
    | archives | Архивы для использования в этом сеансе | Список строк |
    | очередь | Имя очереди YARN, которая будет представлена| Строка |
    | name | Название этой сессии | Строка |
    | conf | Свойства конфигурации Spark | Сопоставление key=val |

    Тело реагирования Созданный объект пакета.

    | name | description | type |
    | :- | :- | :- |
    | идентификатор | Идентификатор сеанса | Int |
    | appId | Идентификатор приложения этой сессии | Строка |
    | appInfo | Подробная информация о заявках | Сопоставление key=val |
    | log | Линии журнала | Список строк |
    | state |Состояние пакета | Строка |

    > [!NOTE]
    > Назначенная конфигурация Livy отображается в выходном стеколе при отправке скрипта.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Интеграция с Azure HDInsight в проводнике

Вы можете просмотреть таблицу Hive в кластерах непосредственно через исследователь **Azure HDInsight:**

1. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure, если вы еще этого не сделали.

2. Выберите значок **Azure** из самой левой колонки.

3. С левой панели расширьте **АЗURE: HDINSIGHT**. Перечислены доступные подписки и кластеры.

4. Расширьте кластер для просмотра базы данных метаданных Hive и таблицы.

5. Нажмите правой кнопкой мыши таблицу Hive. Например: **hivesampletable**. Выберите **Предварительный просмотр**.

   ![Искра & Hive для визуальной студии код предварительный улей таблице](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Открывается окно **предварительных результатов:**

   ![Искра & Hive для окна предварительных результатов визуальных версий кода](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- РЕЗУЛТАТИОНС панели

   Вы можете сохранить весь результат в файле CSV, JSON или Excel на локальном пути или просто выбрать несколько строк.

- ПАНЕЛЬ MESSAGES
   1. Когда количество строк в таблице превышает 100, вы видите следующее сообщение: «Первые 100 строк отображаются для таблицы Hive».
   2. Когда количество строк в таблице меньше или равно 100, вы видите сообщение, как следующее: "60 строк отображаются для таблицы Hive".
   3. Когда в таблице нет содержимого, отображается следующее сообщение: «Для таблицы Hive отображаются 0 строк».

        >[!NOTE]
        >
        >В Linux установите xclip для включения данных таблицы копирования.
        >
        >![Искра & Hive для визуального кода студии в Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Дополнительные функции

Spark & Hive for Visual Studio Code также поддерживает следующие функции:

- **Автозаполнение IntelliSense**. Предложения всплывают для ключевых слов, методов, переменных и других элементов программирования. Разные значки обозначают разные объекты:

    ![Искра & Hive Tools для объектов Visual Studio Code IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Маркер ошибки IntelliSense**. Языковой сервис подчеркивает ошибки редактирования в скрипте Hive.     
- **Синтаксис подчеркивается**. Языковая служба использует различные цвета для дифференциации переменных, ключевых слов, типа данных, функций и других элементов программирования:

    ![Выделение синтаксиса в Spark & Hive Tools для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Роль только для чтения

Пользователи, которым назначена роль только для чтения для кластера, больше не могут отправлять задания в кластер HDInsight, а также они не могут просматривать базу данных Hive. Свяжитесь с администратором кластера, чтобы обновить свою роль до [**оператора кластера HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) на [портале Azure.](https://ms.portal.azure.com/) Если у вас есть действительные учетные данные Ambari, вы можете вручную связать кластер, используя следующее руководство.

### <a name="browse-the-hdinsight-cluster"></a>Просмотрите кластер HDInsight  

При выборе исследователя Azure HDInsight для расширения кластера HDInsight необходимо связать кластер, если у вас есть роль только для читателя для кластера. Используйте следующий метод для ссылки на кластер, используя свои учетные данные Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Отправить задание в кластер HDInsight

При отправке задания в кластер HDInsight требуется связать кластер, если вы находитесь в роли только для чтения для кластера. Используйте следующие шаги для соединения с кластером, используя учетные данные Ambari.

### <a name="link-to-the-cluster"></a>Ссылка на кластер

1. Введите действительное имя пользователя Ambari.
2. Введите действительный пароль.

   ![Искра & Hive Инструменты для визуальной студии код Username](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Искра & Hive Инструменты для визуальной студии код пароль](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Можно использовать `Spark / Hive: List Cluster` для проверки связанного кластера:
  >
  >![Искра & Hive Инструменты для визуальной студии код Reader Связанные](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2-го поколения

### <a name="browse-a-data-lake-storage-gen2-account"></a>Просмотр учетной записи Data Lake Storage Gen2

При выборе исследователя Azure HDInsight для расширения учетной записи Data Lake Storage Gen2 вам будет предложено ввести ключ доступа к хранилищу, если учетная запись Azure не имеет доступа к хранению Gen2. После проверки ключа доступа учетная запись Data Lake Storage Gen2 автоматически расширяется.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Отправка заданий в кластер HDInsight с помощью Data Lake Storage Gen2

Когда вы отправляете задание в кластер HDInsight с помощью Data Lake Storage Gen2, вам предлагается ввести ключ доступа к хранилищу, если ваша учетная запись Azure не имеет доступа к хранению Gen2. После проверки ключа доступа задание будет успешно отправлено.

![Искра & Hive Инструменты для визуальной студии код AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Ключ доступа к учетной записи хранилища можно получить на портале Azure. См. сведения о том, как [управлять ключами доступа к учетной записи хранения](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Удаление связи кластера

1. Из меню бар, перейдите к **Просмотр** > **команды палитра**, а затем ввести **Spark / Улей: Отменить кластер**.  

2. Выберите кластер для развязывании.  

3. Просмотрите представление **OUTPUT** для проверки.  

## <a name="sign-out"></a>Выход  

Из панели меню перейдите в**панель команд** **просмотра,** > а затем введите **Azure: Выйдите из.**

## <a name="next-steps"></a>Дальнейшие действия

Для видео, которое демонстрирует использование Spark & Hive для визуальной студии кода, [см. Spark & Hive для визуального кода студии](https://go.microsoft.com/fwlink/?linkid=858706).
