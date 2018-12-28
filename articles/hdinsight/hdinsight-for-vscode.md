---
title: Средства Azure HDInsight — использование Visual Studio Code для Hive, LLAP или PySpark | Документация Майкрософт
description: Сведения о создании и отправке запросов и скриптов с помощью средств Azure HDInsight для Visual Studio Code.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: fe186966dbddb5aa743e465f897aa5a1a0e07db1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840220"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Использование средств Azure HDInsight для Visual Studio Code

Сведения об использовании средств Azure HDInsight для Visual Studio Code (VS Code) с целью создания и отправки пакетных заданий Hive, интерактивных запросов Apache Hive и скриптов PySpark для Apache Spark. Сначала мы покажем, как установить средства HDInsight в VS Code, а затем рассмотрим способы отправки заданий в Hive и Spark. 

Средства Azure HDInsight можно устанавливать на всех платформах, поддерживаемых VS Code, включая Windows, Linux и MacOS. Далее описаны предварительные требования для различных платформ.


## <a name="prerequisites"></a>Предварительные требования

Для выполнения действий в этом руководстве необходимы такие компоненты:

- Кластер HDInsight. Сведения о создании кластера см. в статье о [начале работы с HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono требуется только для Linux и macOS.

## <a name="install-the-hdinsight-tools"></a>Установка средств HDInsight
   
После установки всех необходимых компонентов можно переходить к установке средств Azure HDInsight для VS Code. 

### <a name="to-install-azure-hdinsight-tools"></a>Установка средств Azure HDInsight

1. Откройте Visual Studio Code.

2. В левой области выберите **Extensions** (Расширения). В поле поиска введите **HDInsight**.

3. Рядом с элементом **Azure HDInsight tools** (Средства Azure HDInsight) нажмите кнопку **Установить**. Через несколько секунд кнопка **Установить** изменится на **Перезагрузить**.

4. Выберите **Перезагрузить**, чтобы активировать расширение **Средства Azure HDInsight**.

5. Выберите **Перезагрузить окно** для подтверждения. В области **Расширения** появится элемент **Azure HDInsight Tools** (Средства Azure HDInsight).

   ![Установка расширения Python средств HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Откройте рабочую область HDInsight

Перед подключением к Azure создайте рабочую область в VS Code.

### <a name="to-open-a-workspace"></a>Открытие рабочей области

1. В меню **Файл** выберите **Открыть папку**. Затем назначьте имеющуюся папку в качестве рабочей или создайте папку. Папка появится в левой области.

2. В левой области выберите значок **Создать файл** рядом с рабочей папкой.

   ![Создание файла](./media/hdinsight-for-vscode/new-file.png)

3. Назовите новый файл, используя расширение HQL (запросы Hive) или PY (сценарий Spark). 

## <a name="connect-to-hdinsight-cluster"></a>Подключение к кластеру HDInsight

Прежде чем отправлять скрипты для кластеров HDInsight из VS Code, необходимо подключиться к учетной записи Azure либо связать кластер (с помощью имени пользователя и пароля Ambari или учетной записи присоединения к домену).

### <a name="to-connect-to-azure"></a>Подключение к Azure

1. Создайте рабочую папку и файл сценария, если у вас их еще нет.

2. Щелкните редактор сценариев правой кнопкой мыши, а затем в контектном меню выберите **HDInsight: Вход**. Вы также можете ввести **Ctrl+Shift+P**, а затем — **HDInsight: Вход**.

    ![Вход в средства HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Чтобы войти, следуйте инструкциям на панели **вывода**.
    + В глобальном окружении вход в HDInsight вызовет процедуру входа в Azure.

        ![Инструкции по входу в Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Для других окружений следуйте инструкциям по входу.

        ![Инструкции по входу для других сред](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

    После подключения имя учетной записи Azure будет отображаться в строке состояния в левом нижнем углу окна VS Code. 

    > [!NOTE]
    > Из-за известной проблемы с проверкой подлинности в Azure браузер следует открывать в режиме конфиденциальности или в режиме инкогнито. Если для учетной записи Azure используется двухфакторная проверка подлинности, мы рекомендуем выбрать проверку подлинности через телефон, а не ПИН-код.
  

4. Щелкните редактор скриптов правой кнопкой мыши, чтобы открыть контекстное меню. В контекстном меню можно выполнять следующие задачи:

    - выход из системы;
    - Получение списка кластеров
    - настройка кластеров по умолчанию;
    - Отправка интерактивных запросов Hive
    - Отправка пакетных сценариев Hive
    - Отправка интерактивных запросов PySpark
    - отправка пакетных сценариев PySpark;
    - задание конфигурации.

<h3 id="linkcluster">Связывание кластера</h3>

Вы можете связать обычный кластер с помощью управляемого имени пользователя [Apache Ambari](https://ambari.apache.org/), а защищенный кластер Hadoop с Корпоративным пакетом безопасности — с помощью имени пользователя домена (например, user1@contoso.com).
1. Откройте палитру команд, нажав кнопку **CTRL+SHIFT+P**, и введите **HDInsight: Связывание кластера**.

   ![команда связывания кластера](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Введите URL-адрес кластера HDInsight, имя пользователя и пароль и выберите тип кластера. Если проверка пройдена, отобразится сообщение об успешном выполнении.
   
   ![диалоговое окно связывания кластера](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Если кластер зарегистрирован в подписке Azure и связан, используется имя пользователя и пароль для связывания. 
   
3. Связанный кластер можно просмотреть с помощью команды **List Cluster**. Теперь в этот связанный кластер можно отправить скрипт.

   ![связанный кластер](./media/hdinsight-for-vscode/linked-cluster.png)

4. Вы также можете удалить связь кластера путем введения **HDInsight: Удалить связь кластера** из палитры команд.


### <a name="to-link-a-generic-apache-livy-endpoint"></a>Связывание универсальной конечной точки Apache Livy

1. Откройте палитру команд, нажав кнопку **CTRL+SHIFT+P**, и введите **HDInsight: Связывание кластера**.
2. Выберите **Generic Livy Endpoint** (Общая конечная точка Livy).
3. Введите универсальную конечную точку Livy, например http://10.172.41.42:18080.
4. Если для универсальной конечной точки Livy требуется авторизация, выберите **Basic** (Базовая). Если нет, выберите **None** (Нет).
5. Введите имя пользователя, если на шаге 4 вы выбрали **Basic** (Базовая).
6. Введите пароль, если на шаге 4 вы выбрали **Basic** (Базовая).
7. Связывание общей конечной точки Livy выполнено успешно.

   ![Связанный общий кластер Livy](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>Получение списка кластеров HDInsight

Чтобы проверить подключение, можно составить список кластеров HDInsight.

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>Получение списка кластеров HDInsight в подписке Azure
1. Откройте рабочую область, а затем подключитесь к Azure. Дополнительную информацию см. в разделе [Переход к рабочей области HDInsight](#open-hdinsight-workspace) и [Подключение к Azure](#connect-to-hdinsight-cluster).

2. Щелкните редактор скриптов правой кнопкой мыши, а затем выберите **HDInsight: В контекстном меню выберите Список кластеров**. 

3. Кластеры HDInsight отобразятся в области **вывода**.

    ![Установка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Установка кластера по умолчанию
1. Откройте рабочую область и подключитесь к Azure. См. разделы [Переход к рабочей области HDInsight](#open-hdinsight-workspace) и [Подключение к Azure](#connect-to-hdinsight-cluster).

2. Щелкните редактор скриптов правой кнопкой мыши, а затем выберите **HDInsight: Задание кластера по умолчанию**. 

3. Выберите кластер в качестве кластера по умолчанию для текущего файла сценария. Средства автоматически обновят файл конфигурации **.VSCode\settings.json**. 

   ![Задание конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Настройка среды Azure
1. Откройте палитру команд, нажав клавиши **CTRL+SHIFT+P**.

2. Введите **HDInsight: Задание среды Azure**.

3. Выберите среду, например "Azure" или "AzureChina", в качестве записи для входа по умолчанию.

4. В свою очередь, средство уже сохранило запись для входа по умолчанию в файле **.VSCode\settings.json**. Вы также можете изменить ее напрямую в файле конфигурации. 

   ![Задание конфигурации записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Отправка интерактивных запросов Hive и пакетных сценариев Hive

Средства HDInsight для VS Code позволяют отправлять интерактивные запросы Hive и пакетные сценарии Hive в кластеры HDInsight.

1. Создайте рабочую папку и файл сценария Hive, если у вас их еще нет.

2. Подключитесь к своей учетной записи Azure или связанным кластерам.

3. Скопируйте следующий код и вставьте его в файл Hive, а затем сохраните файл.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Щелкните редактор скриптов правой кнопкой мыши и выберите **HDInsight: Hive Interactive** чтобы отправить запрос, или воспользуйтесь сочетанием клавиш **Ctrl+Alt+I**. Выберите **HDInsight: Hive Batch** (HDInsight: пакетный сценарий Hive), чтобы отправить сценарий, или воспользуйтесь сочетанием клавиш **CTRL+ALT+H**. 

5. Выберите кластер, если вы не указали кластер по умолчанию. Средства также позволяют отправить блок кода вместо целого файла сценария с помощью контекстного меню. Через несколько секунд результаты запроса появятся на новой вкладке.

   ![Результат Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Панель **Результаты**: Вы можете сохранить все результаты в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

    - Панель **Сообщения**: Выбрав номер **строки**, можно перейти к нужной строке выполняемого скрипта.

## <a name="submit-interactive-pyspark-queries"></a>Отправка интерактивных запросов PySpark

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>Отправка интерактивных запросов PySpark в кластеры HDInsight Spark

1. Создайте рабочую папку и файл сценария с расширением PY, если у вас их еще нет.

2. Подключитесь к учетной записи Azure, если вы еще этого не сделали.

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
4. Выделите этот скрипт. Затем щелкните редактор скриптов правой кнопкой мыши и выберите **HDInsight: Интерактивная среда PySpark**, или воспользуйтесь сочетанием клавиш **Ctrl+Alt+I**.

5. Если вы еще не установили расширение **Python** в VS Code, нажмите кнопку **Установить**, как показано на следующей иллюстрации:

    ![Установка расширения Python средств HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Установите среду Python в своей системе, если вы еще этого не сделали. 
   - Для Windows скачайте и установите [Python](https://www.python.org/downloads/). Затем убедитесь, что `Python` и `pip` добавлены в системную переменную PATH.

   - Инструкции для macOS и Linux можно найти в статье [Настройка интерактивной среды PySpark для Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Выберите кластер, к которому будет отправлен запрос PySpark. Вскоре после этого результат запроса отображается на новой вкладке справа:

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Средство также поддерживает запросы **SQL-предложений**.

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) При выполнении запросов статус отправки отображается слева от нижней строки состояния. Если отображается состояние **PySpark Kernel (busy)** (Ядро PySpark (занято)), не отправляйте другие запросы. 

>[!NOTE]
>Кластеры могут сохранять сведения о сеансе. Определенная переменная, функция и соответствующие значения хранятся в сеансе, поэтому к ним можно обращаться в нескольких вызовах службы одного кластера. 

### <a name="to-disable-environment-check"></a>Отключение проверки окружения

По умолчанию при отправке интерактивных запросов PySpark средства HDInsight проверяют окружение и устанавливают пакеты зависимостей. Чтобы отключить проверку среды, установите для параметра **hdinsight.disablePysparkEnvironmentValidation** значение **yes** в разделе **Параметры пользователя**.

   ![Установка проверки окружения в настройках](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check.png)

Также можно нажать копку **Disable Validation** (Отключить проверку) во всплывающем диалоговом окне.

   ![Установка проверки окружения в диалоговом окне](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check-dialog.png)

### <a name="pyspark3-is-not-supported-with-spark2223"></a>Spark версий 2.2 и 2.3 не поддерживает PySpark3

Кластеры Spark версий 2.2 и 2.3 больше не поддерживают PySpark3. Для языка Python поддерживается только PySpark. Невозможность отправки запросов в Spark версий 2.2 и 2.3 при использовании Python 3 — это известная проблема.

   ![Ошибка при отправке запросов из Python 3](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Чтобы использовать Python 2.x, выполните следующие шаги: 

1. Установите Python 2.7 на локальный компьютер и добавьте его в системную переменную PATH.

2. Перезапустите VSCode.

3. Переключитесь на Python 2, щелкнув **Python XXX** в строке состояния, а затем выберите целевую версию Python.

   ![Выбор версии Python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

## <a name="submit-pyspark-batch-job"></a>Отправка пакетного задания PySpark

1. Создайте рабочую папку и файл сценария с расширением PY, если у вас их еще нет.

2. Подключитесь к учетной записи Azure, если вы еще этого не сделали.

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
4. Щелкните редактор скриптов правой кнопкой мыши, а затем выберите **HDInsight: Пакетная служба PySpark**, или воспользуйтесь сочетанием клавиш **Ctrl+Alt+I**. 

5. Выберите кластер, к которому будет отправлено задание PySpark. 

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

После отправки задания Python в окне **вывода** в VS Code отобразятся журналы отправки. Также отображаются **URL-адрес пользовательского интерфейса Spark** и **URL-адрес пользовательского интерфейса Yarn**. URL-адрес можно открыть в веб-браузере, чтобы отследить состояние задания.

## <a name="apache-livy-configuration"></a>Конфигурация Apache Livy

Поддерживается конфигурация [Apache Livy](https://livy.incubator.apache.org/). Ее можно настроить в файле **.VSCode\settings.json**, который находится в папке рабочей области. Сейчас конфигурация Livy поддерживает только скрипт Python. Дополнительные сведения см. в [файле сведений Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Активация конфигурации Livy**
   
В меню **Файл** выберите **Параметры**, также выберите **Параметры** в контекстном меню. Щелкните вкладку **Параметры рабочей области**. Здесь можно настроить конфигурацию Livy.

Также можно отправить файл. Обратите внимание, что в рабочую папку автоматически добавлена подпапка .vscode. Чтобы найти конфигурацию Livy, щелкните **.vscode\settings.json**.

+ Параметры проекта:

    ![Конфигурация Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Для параметров **driverMomory** и **executorMomry** значения нужно указывать с единицей измерения, например 1g или 1024m. 

+ Поддерживаемые конфигурации Livy:   

    **Пакеты POST**   
    Текст запроса

    | name | description | Тип | 
    | :- | :- | :- | 
    | file | Файл, содержащий выполнение приложения | path (обязательный параметр) | 
    | proxyUser | Пользователь для олицетворения при выполнении задания | строка | 
    | className | Основной класс Java или Spark приложения | строка |
    | args | Аргументы командной строки для приложения | Список строк | 
    | jars | jars для использования в этом сеансе | Список строк | 
    | pyFiles | Файлы Python для использования в этом сеансе | Список строк |
    | файлов | Файлы для использования в этом сеансе | Список строк |
    | driverMemory | Объем памяти для процесса драйвера | строка |
    | driverCores | Число ядер для процесса драйвера | int |
    | executorMemory | Объем памяти для каждого процесса исполнителя | строка |
    | executorCores | Число ядер для каждого исполнителя | int |
    | numExecutors | Количество исполнителей для запуска в этом сеансе | int |
    | archives | Архивы для использования в этом сеансе | Список строк |
    | очередь | Имя очереди YARN для отправки | строка |
    | name | Имя этого сеанса | строка |
    | conf | Свойства конфигурации Spark. | Сопоставление key=val |

    Текст ответа   
    Созданный объект пакетной службы

    | name | description | Тип | 
    | :- | :- | :- | 
    | id | Идентификатор сеанса | int | 
    | appId | Идентификатор приложения для этого сеанса |  Строка |
    | appInfo | Информация о приложении | Сопоставление key=val |
    | log | Строки журнала | Список строк |
    | state |   Состояние пакета | строка |

>[!NOTE]
>При отправке скрипта присвоенная конфигурация Livy отобразится в области выходных данных.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Интеграция с Azure HDInsight в проводнике

Раздел Azure HDInsight добавлен на левую панель. Вы можете просматривать кластер и управлять им непосредственно с панели.

1. Разверните раздел **Azure HDInsight**. Если вы не вошли в Azure, отобразится ссылка **Войти в Azure…**

    ![Снимок экрана со ссылкой входа](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. Щелкните **Войти в Azure**. В правом нижнем углу отобразится всплывающее окно со ссылкой и кодом для входа.

    ![Инструкции по входу для других сред](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. Нажмите кнопку **Copy & Open** (Скопировать и открыть). Когда откроется браузер, вставьте код и нажмите кнопку **Продолжить**. Вы увидите сообщение о том, что вход выполнен успешно.

4. После входа в разделе **Azure HDInsight** отобразится список доступных подписок и кластеров (поддерживаются Spark, Hadoop и HBase). 

   ![Подписка HDInsight Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. Разверните кластер, чтобы просмотреть базу метаданных Hive и схему таблиц.

   ![Кластер Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="additional-features"></a>Дополнительные функции

HDInsight для VSCode поддерживает перечисленные ниже функции.

- **Автоматическое завершение IntelliSense**. Появляются предложения для ключевых слов, методов, переменных и т. д. Разные значки обозначают объекты разных типов.

    ![Типы объектов IntelliSencse в средствах HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Маркер ошибки IntelliSense**. Языковая служба подчеркивает ошибки редактирования для сценария Hive.     
- **Подсветка синтаксиса**. Языковая служба выделяет разными цветами переменные, ключевые слова, типы данных, функции и т. д. 

    ![Подсветка синтаксиса в средствах HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Дополнительная информация

### <a name="demo"></a>Демонстрация
* HDInsight для VS Code: [Видео](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Средства и расширения

* [Удаленная отладка приложений Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Удаленная или локальная отладка приложений Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ через SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в Azure Toolkit for Eclipse для создания приложений Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Визуализация данных Apache Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualize Interactive Query Hive data with Microsoft Power BI using DirectQuery in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive из кластера Interactive Query с помощью Microsoft Power BI и DirectQuery в Azure HDInsight).
* [Настройка интерактивной среды PySpark для Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Сценарии
* [Apache Spark и бизнес-аналитика. Анализ данных Apache Spark с использованием Power BI в HDInsight](spark/apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Использование Spark в HDInsight для анализа температуры здания, с помощью данных системы кондиционирования](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Использование Spark в HDInsight для прогнозирования результатов проверки продуктов питания](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](spark/apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](spark/apache-spark-job-debugging.md)



