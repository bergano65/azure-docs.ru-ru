---
title: Azure HDInsight для Visual Studio Code
description: Сведения о создании и отправке запросов и скриптов с помощью инструментов Spark & Hive (Azure HDInsight) для Visual Studio Code.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 226862c51417e311bc39feefa9f5e860d55b7dc1
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034107"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Использование Spark & средств Hive для Visual Studio Code

Узнайте, как использовать Spark & средства Hive для Visual Studio Code создания и отправки Apache Hive пакетных заданий, интерактивных запросов Hive и скриптов PySpark для Apache Spark. Сначала мы расскажем, как установить в Visual Studio Code & средства Hive для Spark, а затем покажу, как отправлять задания в Spark & средства Hive.  

Средства Spark & Hive можно установить на платформах, которые поддерживаются Visual Studio Code, включая Windows, Linux и macOS. Обратите внимание на следующие компоненты для различных платформ.


## <a name="prerequisites"></a>Предварительные требования

Для выполнения действий в этом руководстве необходимы такие компоненты:

- Кластер Azure HDInsight. Сведения о создании кластера см. в статье о [начале работы с HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Или используйте кластер Spark и Hive, поддерживающий конечную точку Apache Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono требуется только для Linux и macOS.
- [PySpark интерактивную среду для Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Локальный каталог с именем **HDexample**.  В этой статье используется **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Установка средств Hive & в Spark

После выполнения необходимых условий можно установить Spark & средства Hive для Visual Studio Code, выполнив следующие действия.

1. Откройте Visual Studio Code.

2. В строке меню выберите **Просмотреть** > **расширения**.

3. В поле поиска введите **Spark & Hive**.

4. Выберите **Spark & средства Hive** в результатах поиска и нажмите кнопку **установить**.

   ![Куст & Spark для Visual Studio Code установки Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. При необходимости выберите **перезагрузить** .


## <a name="open-a-work-folder"></a>Открытие рабочей папки

Чтобы открыть рабочую папку и создать файл в Visual Studio Code, выполните следующие действия.

1. В строке меню выберите **файл** > **Открыть папку** > **к:\хд\хдексампле**, а затем нажмите кнопку **выбрать папку** . Папка отобразится в представлении **обозревателя** слева.

2. В представлении **обозревателя** выберите папку **хдексампле** , а затем щелкните значок **новый файл** рядом с рабочей папкой:

   ![Создание файла](./media/hdinsight-for-vscode/new-file.png)

3. Присвойте новому файлу имя с помощью команды `.hql` (запросы Hive) `.py` или расширения файла (скрипт Spark). В этом примере используется файл **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Настройка среды Azure

Для пользователя национального облака выполните следующие действия, чтобы сначала настроить среду Azure, а затем использовать **Azure:**  Команда входа для входа в Azure:
   
1. Выберите **филе\преференцес\сеттингс**.
2. Выполните поиск по следующей строке: **Общие сведения Вычислений**
3. Выберите Национальный облако из списка:

   ![Задание конфигурации записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Подключение к учетной записи Azure

Прежде чем отправлять скрипты в кластеры из Visual Studio Code, необходимо подключиться к учетной записи Azure или связать кластер (с помощью учетных данных Apache Ambari имя пользователя и пароль или учетной записи, присоединенной к домену). Чтобы подключиться к Azure, выполните следующие действия.

1. В строке меню выберите **вид** > **Палитра команд**и введите **Azure:** Вход:

    ![Spark & средства Hive для входа Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Следуйте инструкциям по входу, чтобы войти в Azure. После подключения имя учетной записи Azure отобразится в строке состояния в нижней части окна Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Связывание кластера

### <a name="link-azure-hdinsight"></a>Ссылку Azure HDInsight

Вы можете связать Стандартный кластер с помощью имени пользователя, управляемого [Apache Ambari](https://ambari.apache.org/), или связать свой корпоративный пакет безопасности в кластере Hadoop с помощью имени пользователя домена (например: `user1@contoso.com`).

1. В строке меню выберите **вид** > **Палитра команд**и введите **Spark/Hive: Связывание кластера**.

   ![команда связывания кластера](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Выберите связанный тип кластера **Azure HDInsight**.

3. Введите URL-адрес кластера HDInsight.

4. Введите имя пользователя Ambari. значение по умолчанию — **Admin**.

5. Введите пароль Ambari.

6. Выберите тип кластера.

7. Задайте отображаемое имя кластера (необязательно).

8. Просмотрите представление **вывода**, чтобы проверить сведения.

   > [!NOTE]  
   > Связанные имя пользователя и пароль используются, если кластер входит в подписку Azure и связан с кластером.  


### <a name="link-generic-livy-endpoint"></a>Ссылку Универсальная конечная точка Livy

1. В строке меню выберите **вид** > **Палитра команд**и введите **Spark/Hive: Связывание кластера**.

2. Выберите связанный тип кластера **Generic Livy Endpoint** (Общая конечная точка Livy).

3. Введите универсальную конечную точку Livy. Например: HTTP\://10.172.41.42:18080.

4. Выберите тип авторизации **Базовый** или **Нет**.  Если выбрать **базовый**:  
    &emsp;а. Введите имя пользователя Ambari. значение по умолчанию — **Admin**.  
    &emsp;Б. Введите пароль Ambari.

5. Просмотрите представление **вывода**, чтобы проверить сведения.

## <a name="list-clusters"></a>Получение списка кластеров

1. В строке меню выберите **вид** > **Палитра команд**и введите **Spark/Hive: List Cluster** (HDInsight: перечисление кластеров).

2. Выберите нужную подписку.

3. Просмотрите представление **вывода**. В этом представлении отображается связанный кластер (или кластеры) и все кластеры в подписке Azure.

    ![Установка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-the-default-cluster"></a>Настройка кластера по умолчанию

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Выберите созданный [ранее](#open-a-work-folder)файл **HelloWorld. HQL** . Он открывается в редакторе скриптов.

3. Щелкните правой кнопкой мыши редактор скриптов и выберите **Spark/Hive: Задание кластера по умолчанию**.  

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Выберите кластер в качестве кластера по умолчанию для текущего файла сценария. Средства автоматически обновляют **.** Файл конфигурации вскоде\сеттингс.жсон:

   ![Задание конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Отправка интерактивных запросов Hive и пакетных скриптов Hive

С помощью Spark & средств Hive для Visual Studio Code можно отправлять Интерактивные запросы Hive и пакетные скрипты Hive в кластеры.

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Выберите созданный [ранее](#open-a-work-folder)файл **HelloWorld. HQL** . Он открывается в редакторе скриптов.


3. Скопируйте и вставьте следующий код в файл Hive, а затем сохраните его:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Щелкните правой кнопкой мыши редактор скриптов и выберите **Hive: Interactive** , чтобы отправить запрос, или используйте сочетание клавиш CTRL + ALT + I.  Выберите **куст: Пакетная** отправка сценария или использование сочетания клавиш CTRL + ALT + H.  

6. Если вы не указали кластер по умолчанию, выберите кластер. Эти средства также позволяют отправить блок кода вместо всего файла скрипта с помощью контекстного меню. Через несколько секунд результаты запроса отобразятся на новой вкладке:

   ![Результат Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Панель **Результаты**: Можно сохранить весь результат в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

    - Панель **Сообщения**: При выборе номера **строки** он переходит к первой строке выполняемого скрипта.

## <a name="submit-interactive-pyspark-queries"></a>Отправка интерактивных запросов PySpark

Чтобы отправить Интерактивные запросы PySpark, выполните следующие действия.

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Создайте новый файл **HelloWorld.py** , следуя приведенным [выше](#open-a-work-folder) инструкциям.

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

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Выделите весь код, щелкните правой кнопкой мыши редактор скриптов и выберите **Spark: PySpark Interactive** , чтобы отправить запрос. Или используйте сочетание клавиш CTRL + ALT + I.

   ![Интерактивное контекстное меню pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Выберите кластер, если вы не указали кластер по умолчанию. Через несколько секунд Интерактивные результаты **Python** отобразятся на новой вкладке. Эти средства также позволяют отправить блок кода вместо всего файла скрипта с помощью контекстного меню:

   ![интерактивное окно Python pyspark Interactive](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Введите **%% info**, а затем нажмите клавиши SHIFT + ВВОД, чтобы просмотреть сведения о задании (необязательно):

   ![Просмотр сведений о задании](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Средство также поддерживает запрос **Spark SQL** :

   ![Результат интерактивного представления Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Состояние отправки отображается слева от нижней строки состояния при выполнении запросов. Если отображается состояние **PySpark Kernel (busy)** (Ядро PySpark (занято)), не отправляйте другие запросы.  

   > [!NOTE] 
   >
   > Когда **расширение Python включено** в параметрах (выбрано по умолчанию), отправленные результаты взаимодействия pyspark будут использовать старое окно:
   >
   > ![Расширенное интерактивное расширение Python pyspark отключено](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Отправка пакетного задания PySpark

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Создайте новый файл **BatchFile.py** , выполнив [предыдущие](#open-a-work-folder) шаги.

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

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Щелкните правой кнопкой мыши редактор скриптов и выберите **Spark: PySpark пакет**или используйте сочетание клавиш CTRL + ALT + H. 

6. Выберите кластер для отправки задания PySpark:

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

После отправки задания Python в окне **вывода** в Visual Studio Code отобразятся журналы отправки. Также отображаются URL-адрес пользовательского интерфейса Spark и URL-адрес пользовательского интерфейса Yarn. URL-адрес можно открыть в веб-браузере, чтобы отследить состояние задания.

## <a name="apache-livy-configuration"></a>Конфигурация Apache Livy

Конфигурация [Apache Livy](https://livy.incubator.apache.org/) поддерживается. Его можно настроить в **. Вскоде\сеттингс.жсон** файл в папке рабочей области. В настоящее время Конфигурация Livy поддерживает только скрипт Python. Дополнительные сведения см. в разделе [readme Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Как активировать конфигурацию Livy**

Метод 1  
1. В строке меню выберите **файл** >  > **настройки параметры**.  
2. В поле **Поиск параметров** введите **отправка задания HDInsight: Livy Conf** (Отправка задания HDInsight: конфигурация Livy).  
3. Выберите **Edit in settings.json** (Редактировать в файле settings.json) для соответствующего результата поиска.

Метод 2   
Отправьте файл и обратите внимание, что папка. vscode автоматически добавляется в рабочую папку. Чтобы просмотреть конфигурацию Livy, выберите **. вскоде\сеттингс.жсон**.

+ Параметры проекта:

    ![Конфигурация Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

    >[!NOTE]
    >Для параметров **дривермемори** и **ексекутормемори** задайте значение и единицу. Пример: 1 ГБ или 1024m.

+ Поддерживаемые конфигурации Livy:   

    **Пакеты POST**   
    Текст запроса

    | name | description | type | 
    | :- | :- | :- | 
    | Файл | Файл, содержащий выполнение приложения | Путь (обязательно) |
    | proxyUser | Пользователь для олицетворения при выполнении задания | Строковое |
    | className | Основной класс Java или Spark приложения | Строковое |
    | args | Аргументы командной строки для приложения | Список строк |
    | jars | JAR для использования в этом сеансе | Список строк | 
    | pyFiles | Файлы Python для использования в этом сеансе | Список строк |
    | files | Файлы, используемые в этом сеансе | Список строк |
    | driverMemory | Объем памяти для процесса драйвера | Строковое |
    | driverCores | Число ядер для процесса драйвера | int |
    | executorMemory | Объем памяти для каждого процесса исполнителя | Строковое |
    | executorCores | Число ядер для каждого исполнителя | int |
    | numExecutors | Количество исполнителей для запуска в этом сеансе | int |
    | archives | Архивы для использования в этом сеансе | Список строк |
    | queue | Имя очереди YARN для отправки| Строковое |
    | name | Имя этого сеанса | Строковое |
    | conf | Свойства конфигурации Spark. | Сопоставление key=val |

    Тело ответа   
    Созданный объект пакетной службы

    | name | description | type |
    | :- | :- | :- | 
    | id | Идентификатор сеанса | int | 
    | appId | Идентификатор приложения для этого сеанса | Строковое |
    | appInfo | Подробные сведения о приложении | Сопоставление key=val |
    | журнал | Строки журнала | Список строк |
    | state |Пакетное состояние | Строковое |

    >[!NOTE]
    >Назначенная конфигурация Livy отображается в области вывода при отправке скрипта.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Интеграция с Azure HDInsight в проводнике

**Azure HDInsight** добавлен в представление обозревателя. Вы можете просматривать кластеры и управлять ими напрямую с помощью **Azure HDInsight**.

1. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

2. В строке меню перейдите в**Обозреватель** **представлений** > .

3. В левой области разверните **AZURE HDINSIGHT**.  Доступные подписки и кластеры (поддерживаются Spark, Hadoop и HBase):

   ![Подписка HDInsight Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Разверните кластер, чтобы просмотреть базу данных метаданных Hive и схему таблицы:

   ![Кластер HDInsight Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Предварительный просмотр таблицы Hive
Вы можете предварительно просмотреть таблицу Hive в кластерах напрямую с помощью **Azure HDInsight** Explorer:
1. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure, если вы еще этого не сделали.

2. Щелкните значок **Azure** из крайнего левого столбца.

3. В левой области разверните **AZURE HDINSIGHT**. В списке перечислены доступные подписки и кластеры.

4. Разверните кластер, чтобы просмотреть базу данных метаданных Hive и схему таблицы.

5. Щелкните правой кнопкой мыши таблицу Hive. Например: **hivesampletable**. Выберите **Предварительный просмотр**. 

   ![& Hive для Visual Studio Code предварительной версии таблицы Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Откроется окно **предварительного просмотра результатов** :

   ![Окно результатов & Hive для Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- Панель результатов

   Можно сохранить весь результат в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

- Панель сообщений
   1. Если число строк в таблице превышает 100, отображается следующее сообщение: "Первые 100 строк отображаются для таблицы Hive".
   2. Если число строк в таблице меньше или равно 100, вы увидите примерно следующее сообщение: "60 строк отображаются для таблицы Hive".
   3. Если в таблице нет содержимого, отобразится следующее сообщение: "0 строк отображается для таблицы Hive".

        >[!NOTE]
        >
        >В Linux установите ксклип, чтобы включить данные копии таблицы.
        >
        >![Spark & Hive для Visual Studio Code в Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
    ## <a name="additional-features"></a>Дополнительные функции

В Spark & Hive для Visual Studio Code также поддерживаются следующие функции.

- **Автозаполнение IntelliSense**. Всплывающие предложения для ключевых слов, методов, переменных и других программных элементов. Разные значки обозначают разные объекты:

    ![Spark & средства Hive для Visual Studio Code типов объектов IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Маркер ошибки IntelliSense**. Языковая служба подчеркивает редактирование ошибок в скрипте Hive.     
- **Подсветка синтаксиса**. Языковая служба использует разные цвета для различения переменных, ключевых слов, типов данных, функций и других программных элементов.

    ![В Spark & средства Hive для Visual Studio Code выделение синтаксиса](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Роль только для чтения

Пользователи, которым назначена роль только для чтения для кластера, больше не могут отправлять задания в кластер HDInsight и не могут просматривать базу данных Hive. Обратитесь к администратору кластера, чтобы обновить роль на [**оператор кластера HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) в [портал Azure](https://ms.portal.azure.com/). Если у вас есть действительные учетные данные Ambari, можно связать кластер вручную, следуя приведенным ниже рекомендациям.

### <a name="browse-the-hdinsight-cluster"></a>Обзор кластера HDInsight  

При выборе обозревателя Azure HDInsight для развертывания кластера HDInsight вам будет предложено связать кластер, если у вас есть роль только для чтения для кластера. Используйте следующий метод для связи с кластером с помощью учетных данных Ambari. 

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Отправка задания в кластер HDInsight

При отправке задания в кластер HDInsight вам будет предложено связать кластер, если вы находитесь в роли только для чтения кластера. Чтобы создать ссылку на кластер с помощью учетных данных Ambari, выполните следующие действия.

### <a name="link-to-the-cluster"></a>Ссылка на кластер

1.  Введите допустимое имя пользователя Ambari.
2.  Введите допустимый пароль.

   ![Spark & средства Hive для Visual Studio Code имени пользователя](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & средства Hive для Visual Studio Code пароля](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Для проверки связанного `Spark / Hive: List Cluster` кластера можно использовать:
  >
  >![& Spark средства Hive для связанного модуля чтения Visual Studio Code](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2-го поколения

### <a name="browse-a-data-lake-storage-gen2-account"></a>Обзор учетной записи Data Lake Storage 2-го поколения

При выборе обозревателя Azure HDInsight для расширения учетной записи Data Lake Storage 2-го поколения вам будет предложено ввести ключ доступа к хранилищу, если у вашей учетной записи Azure нет доступа к хранилищу Gen2. После проверки ключа доступа учетная запись Data Lake Storage 2-го поколения разворачивается в автоматическом состоянии.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Отправка заданий в кластер HDInsight с Data Lake Storage 2-го поколения

При отправке задания в кластер HDInsight с помощью Data Lake Storage 2-го поколения вам будет предложено ввести ключ доступа к хранилищу, если учетная запись Azure не имеет доступа на запись в хранилище Gen2. После проверки ключа доступа задание будет успешно отправлено.

![Spark & средства Hive для Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Ключ доступа для учетной записи хранения можно получить из портал Azure. Дополнительные сведения см. в разделе [Просмотр и копирование ключей доступа](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Удаление связи кластера

1. В строке меню выберите **вид** > **Палитра команд**, а затем введите **Spark/Hive: Unlink a Cluster** (HDInsight: удалить связь кластера).  

2. Выберите кластер для разрыва связи.  

3. Проверьте представление **вывода** для проверки.  

## <a name="sign-out"></a>Выйти  

В строке меню выберите **вид** > **Палитра команд**, а затем введите **Azure: Выйдите**из нее.


## <a name="next-steps"></a>Следующие шаги
Видеоролик, демонстрирующий использование Spark & Hive для Visual Studio Code, см. в разделе [spark & Hive для Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
