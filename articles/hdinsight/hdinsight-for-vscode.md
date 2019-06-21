---
title: Средства Azure HDInsight — использование Visual Studio Code для Hive, LLAP и PySpark
description: Сведения о создании и отправке запросов и скриптов с помощью средств Azure HDInsight для Visual Studio Code.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: aadfae9a7b74986fd0ac8857669dd3ccaf62af1f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166128"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Использование средств Azure HDInsight для Visual Studio Code

Сведения об использовании средств Azure HDInsight для Visual Studio Code с целью создания и отправки пакетных заданий Hive, интерактивных запросов Apache Hive и сценариев PySpark для Apache Spark. Сначала мы покажем, как установить средства HDInsight в Visual Studio Code, а затем рассмотрим способы отправки заданий в Hive и Spark.  

Средства Azure HDInsight можно устанавливать на всех платформах, поддерживаемых Visual Studio Code, включая Windows, Linux и macOS. Далее описаны предварительные требования для различных платформ.


## <a name="prerequisites"></a>Технические условия

Для выполнения действий в этом руководстве необходимы такие компоненты:

- Кластер HDInsight. Сведения о создании кластера см. в статье о [начале работы с HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono требуется только для Linux и macOS.
- [Расширение учетной записи Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) для Visual Studio Code.
- [Настройка интерактивной среды PySpark для Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Локальный каталог с именем **HDexample**.  В этой статье используется **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Установка средств Azure HDInsight

После установки всех необходимых компонентов можно переходить к установке средств Azure HDInsight для Visual Studio Code.  Чтобы установить средства Azure HDInsight, сделайте следующее:

1. Откройте Visual Studio Code.

2. В строке меню выберите **Вид** > **Расширения**.

3. В поле поиска введите **HDInsight**.

4. Выберите **Средства Azure HDInsight** в результатах поиска, а затем щелкните **Установить**.  

   ![Установка расширения Python средств HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Выберите **Перезагрузить**, чтобы активировать расширение **Средства Azure HDInsight** после его установки.


## <a name="open-hdinsight-work-folder"></a>Открытие рабочей папки HDInsight

Выполните следующие шаги, чтобы открыть рабочую папку и создать файл в Visual Studio Code:

1. В строке меню выберите **Файл** > **Открыть папку...**  > **C:\HD\HDexample**, а затем нажмите кнопку **Выбрать папку**. Папка отобразится в представлении **обозревателя** слева.

2. В представлении **обозревателя** выберите папку **HDexample**, а затем щелкните значок **Создать файл** рядом с рабочей папкой.

   ![Создание файла](./media/hdinsight-for-vscode/new-file.png)

3. Назовите новый файл с помощью `.hql` (запросов Hive) или `.py` расширение файла (сценарий Spark).  В этом примере используется файл **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Настройка среды Azure

1. [Подключение](#connect-to-azure-account) подписки Azure с учетной записи или связывание кластера в том случае, если вы еще не сделали.

2. В строке меню выберите **Вид** > **Палитра команд...** и введите **HDInsight: Задание среды Azure**.

3. Выберите среду в качестве записи для входа по умолчанию.

4. В свою очередь, средство уже сохранило запись для входа по умолчанию в файле **.VSCode\settings.json**. Вы также можете изменить ее напрямую в файле конфигурации. 

   ![Задание конфигурации записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Подключение к учетной записи Azure

Прежде чем отправлять сценарии для кластеров HDInsight из Visual Studio Code, необходимо подключиться к учетной записи Azure либо связать кластер (с помощью имени пользователя и пароля Ambari или учетной записи присоединения к домену).  Чтобы подключиться к Azure, выполните следующие действия:

1. В строке меню выберите **Вид** > **Палитра команд...** и введите **HDInsight: Вход**.

    ![Вход в средства HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Выполните вход согласно указаниям **ВЫВОДА** области.
    + Для глобальной среды Azure команда **HDInsight: Вход** активирует действие **Войти в Azure** в обозревателе HDInsight и наоборот.

        ![Инструкции по входу в Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Для других сред выполните вход согласно указаниям.

        ![Инструкции по входу для других сред](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   После подключения имя учетной записи Azure будет отображаться в строке состояния в левом нижнем углу окна Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Связывание кластера

### <a name="link-azure-hdinsight"></a>Ссылка: Azure HDInsight

Вы можете связать обычный кластер с помощью управляемого имени пользователя [Apache Ambari](https://ambari.apache.org/), а защищенный кластер Hadoop с Корпоративным пакетом безопасности — с помощью имени пользователя домена (например, user1@contoso.com).

1. В строке меню выберите **Вид** > **Палитра команд...** и введите **HDInsight: Связывание кластера**.

   ![команда связывания кластера](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Выберите связанный тип кластера **Azure HDInsight**.

3. Введите URL-адрес кластера HDInsight.

4. Введите имя пользователя Ambari. Значение по умолчанию — **admin**.

5. Введите пароль Ambari.

6. Выберите тип кластера.

7. Просмотрите представление **вывода**, чтобы проверить сведения.

   > [!NOTE]  
   > Если кластер зарегистрирован в подписке Azure и связан, используется имя пользователя и пароль для связывания.  



### <a name="link-generic-livy-endpoint"></a>Ссылка: общая конечная точка Livy

1. В строке меню выберите **Вид** > **Палитра команд...** и введите **HDInsight: Связывание кластера**.

2. Выберите связанный тип кластера **Generic Livy Endpoint** (Общая конечная точка Livy).

3. Введите универсальную конечную точку Livy, например http\://10.172.41.42:18080.

4. Выберите тип авторизации **Базовый** или **Нет**.  В случае выбора типа **Базовый**:  
    &emsp;а. Введите имя пользователя Ambari. Значение по умолчанию — **admin**.  
    &emsp;Б. Введите пароль Ambari.

5. Просмотрите представление **вывода**, чтобы проверить сведения.

## <a name="list-hdinsight-clusters"></a>Получение списка кластеров HDInsight

1. В строке меню выберите **Вид** > **Палитра команд...** и введите **HDInsight: List Cluster** (HDInsight: перечисление кластеров).

2. Выберите нужную подписку.

3. Просмотрите представление **вывода**.  В представлении будут отображаться связанные кластеры и все кластеры в подписке Azure.

    ![Установка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Задание кластера по умолчанию

1. Повторно откройте папку **HDexample**, созданную [ранее](#open-hdinsight-work-folder), если она закрыта.  

2. Выберите файл **HelloWorld.hql**, созданный [ранее](#open-hdinsight-work-folder), и он откроется в редакторе сценариев.

3. Щелкните редактор сценариев правой кнопкой мыши и выберите **HDInsight: Задание кластера по умолчанию**.  

4. [Подключение](#connect-to-azure-account) подписки Azure с учетной записи или связывание кластера в том случае, если вы еще не сделали.

5. Выберите кластер в качестве кластера по умолчанию для текущего файла сценария. Средства автоматически обновят файл конфигурации **.VSCode\settings.json**. 

   ![Задание конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Отправка интерактивных запросов Hive и пакетных сценариев Hive

Средства HDInsight для Visual Studio Code позволяют отправлять интерактивные запросы Hive и пакетные сценарии Hive в кластеры HDInsight.

1. Повторно откройте папку **HDexample**, созданную [ранее](#open-hdinsight-work-folder), если она закрыта.  

2. Выберите файл **HelloWorld.hql**, созданный [ранее](#open-hdinsight-work-folder), и он откроется в редакторе сценариев.


3. Скопируйте следующий код и вставьте его в файл Hive, а затем сохраните файл.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Подключение](#connect-to-azure-account) подписки Azure с учетной записи или связывание кластера в том случае, если вы еще не сделали.

5. Щелкните редактор скриптов правой кнопкой мыши и выберите **HDInsight: Hive Interactive** чтобы отправить запрос, или воспользуйтесь сочетанием клавиш **Ctrl+Alt+I**.  Выберите **HDInsight: Hive Batch** (HDInsight: пакетный сценарий Hive), чтобы отправить сценарий, или воспользуйтесь сочетанием клавиш **CTRL+ALT+H**.  

6. Выберите кластер, если вы не указали кластер по умолчанию. Средства также позволяют отправить блок кода вместо целого файла сценария с помощью контекстного меню. Через несколько секунд на новой вкладке появятся результаты запроса.

   ![Результат Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Панель **Результаты**: Вы можете сохранить все результаты в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

    - Панель **Сообщения**: Выбрав номер **строки**, можно перейти к нужной строке выполняемого скрипта.

## <a name="submit-interactive-pyspark-queries"></a>Отправка интерактивных запросов PySpark

Можно отправлять интерактивные запросы PySpark, выполнив следующие действия:

1. Повторно откройте папку **HDexample**, созданную [ранее](#open-hdinsight-work-folder), если она закрыта.  

2. Создайте новый файл **HelloWorld.py**, выполнив [предыдущие](#open-hdinsight-work-folder) действия.

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

4. [Подключение](#connect-to-azure-account) подписки Azure с учетной записи или связывание кластера в том случае, если вы еще не сделали.

5. Выберите весь код, щелкните правой кнопкой мыши редактор сценариев, нажмите кнопку **HDInsight: PySpark Interactive**, чтобы отправить запрос, или воспользуйтесь клавишами **CTRL+ALT+I**.

   ![Щелкните правой кнопкой мыши интерактивных pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Выберите кластер, если вы не указали кластер по умолчанию. Через несколько секунд **Python интерактивные результаты** появляются в новой вкладке. Средства также позволяют отправить блок кода вместо целого файла сценария с помощью контекстного меню. 

   ![интерактивное окно python в интерактивный pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Ввод **«%% info»** , а затем нажмите клавишу **Shift + Ввод** для просмотра сведений о заданиях. (Дополнительно)

   ![Просмотр сведений о задании](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Это средство также поддерживает **Spark SQL** запроса.

   ![Интерактивной среды Pyspark результат представления](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Статус отправки отображается слева от нижней строке состояния при выполнении запросов. Если отображается состояние **PySpark Kernel (busy)** (Ядро PySpark (занято)), не отправляйте другие запросы.  

   > [!NOTE] 
   >
   > Когда **включить расширение Python** не установлен в параметрах (параметр по умолчанию установлен), результаты взаимодействия отправленного pyspark будет использовать старое окна.
   >
   > ![Отключить расширения интерактивный python pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Отправка пакетного задания PySpark

1. Повторно откройте папку **HDexample**, созданную [ранее](#open-hdinsight-work-folder), если она закрыта.  

2. Создайте новый файл **BatchFile.py**, выполнив [предыдущие](#open-hdinsight-work-folder) действия.

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

4. [Подключение](#connect-to-azure-account) подписки Azure с учетной записи или связывание кластера в том случае, если вы еще не сделали.

5. Щелкните редактор скриптов правой кнопкой мыши, а затем выберите **HDInsight: Пакетная служба PySpark**, или воспользуйтесь сочетанием клавиш **Ctrl+Alt+I**. 

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
    Текст запроса

    | name | description | type | 
    | :- | :- | :- | 
    | file | Файл, содержащий выполнение приложения | path (обязательный параметр) | 
    | proxyUser | Пользователь для олицетворения при выполнении задания | string | 
    | className | Основной класс Java или Spark приложения | string |
    | args | Аргументы командной строки для приложения | Список строк | 
    | jars | jars для использования в этом сеансе | Список строк | 
    | pyFiles | Файлы Python для использования в этом сеансе | Список строк |
    | files | Файлы для использования в этом сеансе | Список строк |
    | driverMemory | Объем памяти для процесса драйвера | string |
    | driverCores | Число ядер для процесса драйвера | int |
    | executorMemory | Объем памяти для каждого процесса исполнителя | string |
    | executorCores | Число ядер для каждого исполнителя | int |
    | numExecutors | Количество исполнителей для запуска в этом сеансе | int |
    | archives | Архивы для использования в этом сеансе | Список строк |
    | очередь | Имя очереди YARN для отправки | string |
    | name | Имя этого сеанса | string |
    | conf | Свойства конфигурации Spark. | Сопоставление key=val |

    Текст ответа   
    Созданный объект пакетной службы

    | name | description | type | 
    | :- | :- | :- | 
    | id | Идентификатор сеанса | int | 
    | appId | Идентификатор приложения для этого сеанса |  String |
    | appInfo | Информация о приложении | Сопоставление key=val |
    | log | Строки журнала | Список строк |
    | состояние |   Состояние пакета | string |

>[!NOTE]
>При отправке скрипта присвоенная конфигурация Livy отобразится в области выходных данных.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Интеграция с Azure HDInsight в проводнике

Раздел **Azure HDInsight** добавлен в представление обозревателя. Вы можете переходить к кластерам и управлять ими напрямую с помощью **Azure HDInsight**.

1. [Подключение](#connect-to-azure-account) подписки Azure с учетной записи или связывание кластера в том случае, если вы еще не сделали.

2. В строке меню выберите **Вид** > **Обозреватель**.

3. В левой области разверните **AZURE HDINSIGHT**.  Отобразится список доступных подписок и кластеров (поддерживаются Spark, Hadoop и HBase). 

   ![Подписка HDInsight Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Разверните кластер, чтобы просмотреть базу метаданных Hive и схему таблиц.

   ![Кластер Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Предварительный просмотр таблицы Hive
Предварительный просмотр таблицы Hive в вашей кластеров с напрямую с помощью **Azure HDInsight** explorer.
1. [Подключитесь](#connect-to-azure-account) к учетной записи Azure, если вы еще этого не сделали.

2. Нажмите кнопку **Azure** значок в крайнем левом столбце.

3. В левой области разверните AZURE HDINSIGHT. Будут перечислены доступные подписки и кластеров.

4. Разверните кластер, чтобы просмотреть базу метаданных Hive и схему таблиц.

5. Щелкните правой кнопкой мыши в таблице Hive, например hivesampletable. Выберите **предварительной версии**. 

   ![HDInsight для vscode Предварительный просмотр hive таблицы](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **Предварительный просмотр результатов** откроется окно.

   ![Окно результатов HDInsight для vscode предварительной версии](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **РЕЗУЛЬТАТЫ** панели

   Вы можете сохранить все результаты в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

- **СООБЩЕНИЯ** панели
   1. Количество строк в таблице больше, чем 100 строк, отображается сообщение: **Для таблицы Hive отображаются первые 100 строк**.
   2. Количество строк в таблице меньше или равно 100 строк, отображается сообщение: **60 строки отображаются для таблицы Hive**.
   3. В таблице отсутствует содержимое, отображается сообщение: **для таблицы Hive отображается 0 строк**.

>[!NOTE]
>
>В Linux установите xclip, чтобы включить копирование таблицы данных.
>
>![HDInsight для vscode в linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Дополнительные функции

HDInsight для Visual Studio Code поддерживает перечисленные ниже функции:

- **Автозавершение IntelliSense**. Появляются предложения для ключевых слов, методов, переменных и т. д. Разные значки обозначают объекты разных типов.

    ![Типы объектов IntelliSencse в средствах HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Маркер ошибки IntelliSense**. Языковая служба подчеркивает ошибки редактирования для сценария Hive.     
- **Подсветка синтаксиса**. Языковая служба выделяет разными цветами переменные, ключевые слова, типы данных, функции и т. д. 

    ![Подсветка синтаксиса в средствах HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Только роль читателя

Пользователи с кластером **чтения** **только** **роли** больше не могут отправить задание в кластер HDInsight, ни просмотреть базу данных Hive. Вам необходимо обратиться к администратору кластера для обновления вашей роли для [ **HDInsight** **кластера** **оператор** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) в [ Портал Azure](https://ms.portal.azure.com/). Если вы знаете Ambari учетные данные, вы можете вручную связать кластера, следуя приведенным ниже инструкциям.

### <a name="browse-hdinsight-cluster"></a>Обзор кластера HDInsight  

При щелчке обозревателя Azure HDInsight, чтобы развернуть кластер HDInsight, будет предложено связать кластер, если вы являетесь единственным роль читателя для кластера. Выполните следующие действия, чтобы связать с кластером с помощью учетных данных Ambari. 

### <a name="submit-job-to-hdinsight-cluster"></a>Отправить задание в кластер HDInsight

При отправке задания в кластер HDInsight, вам будет предложено связать кластер, если вы являетесь единственным роль читателя для кластера. Выполните следующие действия, чтобы связать с кластером с помощью учетных данных Ambari. 

### <a name="link-to-cluster"></a>Ссылка на кластер

1.  Введите имя пользователя Ambari 
2.  Введите Ambari пользователя, пароль.

   ![Средства HDInsight для Visual Studio с кодовым названием пользователя](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Средства HDInsight для Visual Studio код пароля](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Можно использовать HDInsight: Список кластеров, чтобы проверить связанный кластер.
>
>![Связанные средства HDInsight для чтения кода Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-reader-linked.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Хранилище Озера данных Azure 2-го поколения (поколения 2 ADLS)

### <a name="browse-an-adls-gen2-account"></a>Обзор учетной записи ADLS 2-го поколения

При щелчке в обозревателе Azure HDInsight, чтобы развернуть учетную запись ADLS Gen2, вам будет предложено ввести хранилища **ключ доступа** Если учетной записи Azure не имеет доступа к хранилищу Gen2. Учетная запись ADLS Gen2 будет автоматически развернут после успешной проверки ключ доступа. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Отправка заданий в кластер HDInsight с ADLS Gen2

При отправке задания в кластер HDInsight с ADLS Gen2, вам будет предложено ввести хранилища **ключ доступа** Если учетной записи Azure не имеет записи доступа к хранилищу Gen2.  Задание успешно отправлено после успешной проверки ключ доступа. 

![Средства HDInsight для Visual Studio код AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Ключ доступа для учетной записи хранения можно получить на портале Azure. Сведения см. в разделе [Просмотр и копирование ключей доступа к](https://docs.microsoft.com/azure/storage/common/storage-account-manage#view-and-copy-access-keys).

## <a name="unlink-cluster"></a>Удаление связи кластера

1. В строке меню выберите **Вид** > **Палитра команд...** , а затем введите **HDInsight: Unlink a Cluster** (HDInsight: удалить связь кластера).  

2. Выберите кластер, связь которого вы хотите удалить.  

3. Просмотрите представление **вывода**, чтобы проверить сведения.  

## <a name="sign-out"></a>Выход  

В строке меню выберите **Вид** > **Палитра команд...** , а затем введите **HDInsight: Logout** (ADL: выход).  В правом нижнем углу появится всплывающее окно с сообщением **Logout successfully!** (Выход выполнено успешно!).


## <a name="next-steps"></a>Дальнейшие действия
Демонстрационные видео использования HDInsight для Visual Studio Code см. [здесь](https://go.microsoft.com/fwlink/?linkid=858706).
