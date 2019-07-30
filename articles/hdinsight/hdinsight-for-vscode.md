---
title: Azure HDInsight для Visual Studio Code
description: Сведения о создании и отправке запросов и скриптов с помощью инструментов Spark & Hive (Azure HDInsight) для Visual Studio Code.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: da5cdd36e70166d274d50fcb093c0889cf534172
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489021"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Использование Spark & средств Hive для Visual Studio Code

Узнайте, как использовать Spark & средства Hive для Visual Studio Code создания и отправки Apache Hive пакетных заданий, интерактивных запросов Hive и скриптов PySpark для Apache Spark. Сначала мы расскажем, как установить средства Hive & в Visual Studio Code, а затем покажу, как отправлять задания в Hive и Spark.  

Средства Spark & Hive можно установить на платформах, которые поддерживаются Visual Studio Code, включая Windows, Linux и macOS. Далее описаны предварительные требования для различных платформ.


## <a name="prerequisites"></a>предварительные требования

Для выполнения действий в этом руководстве необходимы такие компоненты:

- Кластер HDInsight. Сведения о создании кластера см. в статье о [начале работы с HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Или кластер Spark/Hive, поддерживающий конечную точку Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono требуется только для Linux и macOS.
- [Настройка интерактивной среды PySpark для Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Локальный каталог с именем **HDexample**.  В этой статье используется **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Установка средств Hive & в Spark

После завершения предварительных требований можно установить средства & Hive для Visual Studio Code.  Выполните следующие действия, чтобы установить Spark & средства Hive.

1. Откройте Visual Studio Code.

2. В строке меню выберите **Вид** > **Расширения**.

3. В поле поиска введите **Spark & Hive**.

4. Выберите **Spark & средства Hive** в результатах поиска и нажмите кнопку **установить**.  

   ![Куст & Spark для Visual Studio Code установки Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. При необходимости перезагрузите.


## <a name="open-work-folder"></a>Открыть рабочую папку

Выполните следующие шаги, чтобы открыть рабочую папку и создать файл в Visual Studio Code:

1. В строке меню выберите **Файл** > **Открыть папку...**  > **C:\HD\HDexample**, а затем нажмите кнопку **Выбрать папку**. Папка отобразится в представлении **обозревателя** слева.

2. В представлении **обозревателя** выберите папку **HDexample**, а затем щелкните значок **Создать файл** рядом с рабочей папкой.

   ![Создание файла](./media/hdinsight-for-vscode/new-file.png)

3. Присвойте новому файлу `.hql` имя (запросы Hive) `.py` или расширение файла (скрипт Spark).  В этом примере используется файл **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Настройка среды Azure

Для пользователя National Cloud выполните действия, чтобы сначала настроить среду Azure, а затем использовать **Azure:**  Выполните команду входа, чтобы войти в Azure.
   
1. Щелкните **филе\преференцес\сеттингс**.
2. Поиск **в Azure: Вычислений**
3. Выберите Национальный облако из списка.

   ![Задание конфигурации записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Подключение к учетной записи Azure

Прежде чем отправлять скрипты в кластеры из Visual Studio Code, необходимо подключиться к учетной записи Azure или связать кластер (с помощью имени пользователя и пароля Ambari или учетной записи, присоединенной к домену).  Чтобы подключиться к Azure, выполните следующие действия:

1. В строке меню перейдите к разделу **Просмотр** > **палитры команд...** и **введите Azure: Войдите в**систему.

    ![Spark & средства Hive для входа Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Следуйте инструкциям по входу, чтобы войти в Azure. После подключения имя учетной записи Azure отобразится в строке состояния в нижней части окна Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Связывание кластера

### <a name="link-azure-hdinsight"></a>Ссылка: Azure HDInsight

Вы можете связать обычный кластер с помощью управляемого имени пользователя [Apache Ambari](https://ambari.apache.org/), а защищенный кластер Hadoop с Корпоративным пакетом безопасности — с помощью имени пользователя домена (например, `user1@contoso.com`).

1. В строке меню перейдите к разделу **Просмотр** > **палитры команд...** и **введите Spark/Hive: Связывание кластера**.

   ![команда связывания кластера](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Выберите связанный тип кластера **Azure HDInsight**.

3. Введите URL-адрес кластера HDInsight.

4. Введите имя пользователя Ambari. Значение по умолчанию — **admin**.

5. Введите пароль Ambari.

6. Выберите тип кластера.

7. Задайте отображаемое имя кластера (необязательно).

8. Просмотрите представление **вывода**, чтобы проверить сведения.

   > [!NOTE]  
   > Если кластер зарегистрирован в подписке Azure и связан, используется имя пользователя и пароль для связывания.  


### <a name="link-generic-livy-endpoint"></a>Ссылка: общая конечная точка Livy

1. В строке меню перейдите к разделу **Просмотр** > **палитры команд...** и **введите Spark/Hive: Связывание кластера**.

2. Выберите связанный тип кластера **Generic Livy Endpoint** (Общая конечная точка Livy).

3. Введите универсальную конечную точку Livy, например http\://10.172.41.42:18080.

4. Выберите тип авторизации **Базовый** или **Нет**.  В случае выбора типа **Базовый**:  
    &emsp;а. Введите имя пользователя Ambari. Значение по умолчанию — **admin**.  
    &emsp;Б. Введите пароль Ambari.

5. Просмотрите представление **вывода**, чтобы проверить сведения.

## <a name="list-clusters"></a>Получение списка кластеров

1. В строке меню перейдите к разделу **Просмотр** > **палитры команд...** и **введите Spark/Hive: List Cluster** (HDInsight: перечисление кластеров).

2. Выберите нужную подписку.

3. Просмотрите представление **вывода**.  В представлении будут отображаться связанные кластеры и все кластеры в подписке Azure.

    ![Установка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Задание кластера по умолчанию

1. Повторно откройте папку **HDexample**, созданную [ранее](#open-work-folder), если она закрыта.  

2. Выберите файл **HelloWorld.hql**, созданный [ранее](#open-work-folder), и он откроется в редакторе сценариев.

3. Щелкните правой кнопкой мыши редактор скриптов и выберите **Spark/Hive: Задание кластера по умолчанию**.  

4. [Подключитесь](#connect-to-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Выберите кластер в качестве кластера по умолчанию для текущего файла сценария. Средства автоматически обновят файл конфигурации **.VSCode\settings.json**. 

   ![Задание конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Отправка интерактивных запросов Hive и пакетных сценариев Hive

С помощью Spark & средств Hive для Visual Studio Code можно отправлять Интерактивные запросы Hive и пакетные скрипты Hive в кластеры.

1. Повторно откройте папку **HDexample**, созданную [ранее](#open-work-folder), если она закрыта.  

2. Выберите файл **HelloWorld.hql**, созданный [ранее](#open-work-folder), и он откроется в редакторе сценариев.


3. Скопируйте следующий код и вставьте его в файл Hive, а затем сохраните файл.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Подключитесь](#connect-to-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Щелкните правой кнопкой мыши редактор скриптов и выберите **Hive: Interactive** , чтобы отправить запрос, или используйте сочетание **клавиш CTRL + ALT + I**.  Выберите **куст: Пакетная** отправка сценария или использование сочетания **клавиш CTRL + ALT + H**.  

6. Выберите кластер, если вы не указали кластер по умолчанию. Средства также позволяют отправить блок кода вместо целого файла сценария с помощью контекстного меню. Через несколько секунд на новой вкладке появятся результаты запроса.

   ![Результат Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Панель **Результаты**: Вы можете сохранить все результаты в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

    - Панель **Сообщения**: Выбрав номер **строки**, можно перейти к нужной строке выполняемого скрипта.

## <a name="submit-interactive-pyspark-queries"></a>Отправка интерактивных запросов PySpark

Вы можете отправлять Интерактивные запросы PySpark, выполнив следующие действия:

1. Повторно откройте папку **HDexample**, созданную [ранее](#open-work-folder), если она закрыта.  

2. Создайте новый файл **HelloWorld.py**, выполнив [предыдущие](#open-work-folder) действия.

3. Скопируйте следующий код и вставьте его в файл сценария:
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

4. [Подключитесь](#connect-to-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Выберите весь код и щелкните правой кнопкой мыши редактор скриптов, выберите **Spark: PySpark Interactive**, чтобы отправить запрос, или воспользуйтесь клавишами **CTRL+ALT+I**.

   ![Интерактивное контекстное меню pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Выберите кластер, если вы не указали кластер по умолчанию. Через несколько секунд Интерактивные результаты **Python** отобразятся на новой вкладке. Средства также позволяют отправить блок кода вместо целого файла сценария с помощью контекстного меню. 

   ![интерактивное окно Python pyspark Interactive](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Введите **"%% info"** , а затем нажмите клавиши **SHIFT + ВВОД** , чтобы просмотреть сведения о задании. (Дополнительно)

   ![Просмотр сведений о задании](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Средство также поддерживает запрос **Spark SQL** .

   ![Результат интерактивного представления Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Состояние отправки отображается слева от нижней строки состояния при выполнении запросов. Если отображается состояние **PySpark Kernel (busy)** (Ядро PySpark (занято)), не отправляйте другие запросы.  

   > [!NOTE] 
   >
   > Если **расширение Python включено** в параметрах (параметр по умолчанию установлен), отправленные результаты взаимодействия pyspark будут использовать старое окно.
   >
   > ![Расширенное интерактивное расширение Python pyspark отключено](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Отправка пакетного задания PySpark

1. Повторно откройте папку **HDexample**, созданную [ранее](#open-work-folder), если она закрыта.  

2. Создайте новый файл **BatchFile.py**, выполнив [предыдущие](#open-work-folder) действия.

3. Скопируйте следующий код и вставьте его в файл сценария:

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

4. [Подключитесь](#connect-to-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Щелкните правой кнопкой мыши редактор скриптов и выберите **Spark: Пакетная служба PySpark**, или воспользуйтесь сочетанием клавиш **Ctrl+Alt+I**. 

6. Выберите кластер, к которому будет отправлено задание PySpark. 

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

После отправки задания Python в окне **вывода** в Visual Studio Code отобразятся журналы отправки. Также отображаются **URL-адрес пользовательского интерфейса Spark** и **URL-адрес пользовательского интерфейса Yarn**. URL-адрес можно открыть в веб-браузере, чтобы отследить состояние задания.

## <a name="apache-livy-configuration"></a>Конфигурация Apache Livy

Поддерживается конфигурация [Apache Livy](https://livy.incubator.apache.org/). Ее можно настроить в файле **.VSCode\settings.json**, который находится в папке рабочей области. Сейчас конфигурация Livy поддерживает только скрипт Python. Дополнительные сведения см. в [файле сведений Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Активация конфигурации Livy**

Метод 1  
1. В строке меню выберите **Файл** > **Параметры** > **Настройка**.  
2. В текстовом поле **Параметры поиска** введите **HDInsight Job Sumission: Livy Conf** (Отправка задания HDInsight: конфигурация Livy).  
3. Выберите **Edit in settings.json** (Редактировать в файле settings.json) для соответствующего результата поиска.

Метод 2   
Отправьте файл. Обратите внимание, что в рабочую папку автоматически добавлена подпапка .vscode. Чтобы найти конфигурацию Livy, щелкните **.vscode\settings.json**.

+ Параметры проекта:

    ![Конфигурация Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Для параметров **driverMomory** и **executorMomry** значения нужно указывать с единицей измерения, например 1g или 1024m. 

+ Поддерживаемые конфигурации Livy:   

    **Пакеты POST**   
    Тело запроса

    | name | description | type | 
    | :- | :- | :- | 
    | Файл | Файл, содержащий выполнение приложения | path (обязательный параметр) | 
    | proxyUser | Пользователь для олицетворения при выполнении задания | строка | 
    | className | Основной класс Java или Spark приложения | строка |
    | args | Аргументы командной строки для приложения | Список строк | 
    | jars | jars для использования в этом сеансе | Список строк | 
    | pyFiles | Файлы Python для использования в этом сеансе | Список строк |
    | files | Файлы для использования в этом сеансе | Список строк |
    | driverMemory | Объем памяти для процесса драйвера | строка |
    | driverCores | Число ядер для процесса драйвера | ssNoversion |
    | executorMemory | Объем памяти для каждого процесса исполнителя | строка |
    | executorCores | Число ядер для каждого исполнителя | ssNoversion |
    | numExecutors | Количество исполнителей для запуска в этом сеансе | ssNoversion |
    | archives | Архивы для использования в этом сеансе | Список строк |
    | queue | Имя очереди YARN для отправки | строка |
    | name | Имя этого сеанса | строка |
    | conf | Свойства конфигурации Spark. | Сопоставление key=val |

    Текст ответа   
    Созданный объект пакетной службы

    | name | description | type | 
    | :- | :- | :- | 
    | id | Идентификатор сеанса | ssNoversion | 
    | appId | Идентификатор приложения для этого сеанса |  Строковое |
    | appInfo | Информация о приложении | Сопоставление key=val |
    | журнал | Строки журнала | Список строк |
    | state |   Состояние пакета | строка |

>[!NOTE]
>При отправке скрипта присвоенная конфигурация Livy отобразится в области выходных данных.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Интеграция с Azure HDInsight в проводнике

Раздел **Azure HDInsight** добавлен в представление обозревателя. Вы можете переходить к кластерам и управлять ими напрямую с помощью **Azure HDInsight**.

1. [Подключитесь](#connect-to-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

2. В строке меню выберите **Вид** > **Обозреватель**.

3. В левой области разверните **AZURE HDINSIGHT**.  Отобразится список доступных подписок и кластеров (поддерживаются Spark, Hadoop и HBase). 

   ![Подписка HDInsight Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Разверните кластер, чтобы просмотреть базу метаданных Hive и схему таблиц.

   ![Кластер HDInsight Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Предварительный просмотр таблицы Hive
Вы можете предварительно просмотреть таблицу Hive в кластерах непосредственно через **Azure HDInsight** Explorer.
1. [Подключитесь](#connect-to-azure-account) к учетной записи Azure, если вы еще этого не сделали.

2. Щелкните значок **Azure** в крайнем левом столбце.

3. В левой области разверните **AZURE HDINSIGHT**. Будут перечислены доступные подписки и кластеры.

4. Разверните кластер, чтобы просмотреть базу метаданных Hive и схему таблиц.

5. Щелкните правой кнопкой мыши таблицу Hive, например hivesampletable. Выберите **Предварительный просмотр**. 

   ![Таблица Hive для предварительной версии Visual Studio Code в Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Откроется окно **предварительного просмотра результатов** .

   ![Окно "результаты просмотра" куста & Hive для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- Панель **результатов**

   Вы можете сохранить все результаты в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

- Панель **сообщений**
   1. Если число строк в таблице превышает 100 строк, в сообщении отображается следующее сообщение: **Первые 100 строк отображаются для таблицы Hive**.
   2. Если число строк в таблице меньше или равно 100 строк, в сообщении отображается следующее сообщение: **60 строк отображается для таблицы Hive**.
   3. Если в таблице нет содержимого, в сообщении отображается следующее: **для таблицы Hive отображается 0 строка**.

>[!NOTE]
>
>В Linux установите ксклип, чтобы включить копирование данных таблицы.
>
>![Spark & Hive для Visual Studio Code в Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Дополнительные функции

В Spark & Hive для Visual Studio Code поддерживаются следующие функции.

- **Автозаполнение IntelliSense**. Появляются предложения для ключевых слов, методов, переменных и т. д. Разные значки обозначают объекты разных типов.

    ![Spark & средства Hive для Visual Studio Code типов объектов IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Маркер ошибки IntelliSense**. Языковая служба подчеркивает ошибки редактирования для сценария Hive.     
- **Подсветка синтаксиса**. Языковая служба выделяет разными цветами переменные, ключевые слова, типы данных, функции и т. д. 

    ![В Spark & средства Hive для Visual Studio Code выделение синтаксиса](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Роль только для чтения

Пользователи с **ролью** **только** для **чтения** кластера больше не могут отправлять задания в кластер HDInsight и не просматривать базу данных Hive. Обратитесь к администратору кластера, чтобы обновить роль на [ **оператор** **кластера** **HDInsight** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) в [портал Azure](https://ms.portal.azure.com/). Если вы знакомы с учетными данными Ambari, вы можете вручную привязать кластер, выполнив приведенную ниже инструкцию.

### <a name="browse-hdinsight-cluster"></a>Обзор кластера HDInsight  

При щелчке в обозревателе Azure HDInsight для развертывания кластера HDInsight вам будет предложено связать кластер, если вы являетесь ролью только для чтения кластера. Выполните следующие действия, чтобы создать ссылку на кластер с помощью учетных данных Ambari. 

### <a name="submit-job-to-hdinsight-cluster"></a>Отправка задания в кластер HDInsight

При отправке задания в кластер HDInsight вам будет предложено связать кластер, если вы являетесь ролью только для чтения кластера. Выполните следующие действия, чтобы создать ссылку на кластер с помощью учетных данных Ambari. 

### <a name="link-to-cluster"></a>Ссылка на кластер

1.  Введите имя пользователя Ambari 
2.  Введите пароль пользователя Ambari.

   ![Spark & средства Hive для Visual Studio Code имя пользователя](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & средства Hive для Visual Studio Code пароля](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Вы можете использовать Spark или Hive: Список кластеров для проверки связанного кластера.
>
>![& Spark средства Hive для связанного модуля чтения Visual Studio Code](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage 2-го поколения (ADLS 2-го поколения)

### <a name="browse-an-adls-gen2-account"></a>Обзор учетной записи ADLS 2-го поколения

Если щелкнуть Azure HDInsight Explorer, чтобы развернуть учетную запись ADLS 2-го поколения, вам будет предложено ввести **ключ доступа** к хранилищу, если у вашей учетной записи Azure нет доступа к хранилищу Gen2. После успешной проверки ключа доступа учетная запись ADLS 2-го поколения будет развернута. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Отправка заданий в кластер HDInsight с ADLS 2-го поколения

При отправке задания в кластер HDInsight с ADLS 2-го поколения вам будет предложено ввести **ключ доступа к** хранилищу, если учетная запись Azure не имеет доступа на запись в хранилище Gen2.  После успешного проверки ключа доступа задание будет успешно отправлено. 

![Spark & средства Hive для Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Ключ доступа для учетной записи хранения можно получить из портал Azure. Дополнительные сведения см. в разделе [Просмотр и копирование ключей доступа](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Удаление связи кластера

1. В строке меню перейдите к разделу **Просмотр** > **палитры команд...** , а **затем введите Spark/Hive: Unlink a Cluster** (HDInsight: удалить связь кластера).  

2. Выберите кластер, связь которого вы хотите удалить.  

3. Просмотрите представление **вывода**, чтобы проверить сведения.  

## <a name="sign-out"></a>Выйти  

В строке меню перейдите к панели **Просмотр** > **палитры команд...** , а затем **введите Azure: Выйдите**из нее.


## <a name="next-steps"></a>Следующие шаги
Видеоролик об использовании Spark & Hive для Visual Studio Code см. в разделе [spark & Hive для Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
