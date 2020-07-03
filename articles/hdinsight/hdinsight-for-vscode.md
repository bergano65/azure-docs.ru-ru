---
title: Azure HDInsight для Visual Studio Code
description: Узнайте, как использовать средства & Hive (Azure HDInsight) Spark для Visual Studio Code. Используйте средства для создания и отправки запросов и сценариев.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: de433d85c2f04a7140fbcb918730218ac3a05e54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878635"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Использование Spark & средств Hive для Visual Studio Code

Узнайте, как использовать Apache Spark & средства Hive для Visual Studio Code. Используйте средства для создания и отправки Apache Hive пакетных заданий, интерактивных запросов Hive и скриптов PySpark для Apache Spark. Сначала мы покажем, как установить в Visual Studio Code & средства Hive для Spark. Затем мы рассмотрим, как отправлять задания в Spark & средства Hive.  

На платформах, которые поддерживаются Visual Studio Code, можно установить средства & Hive Spark. Обратите внимание на следующие компоненты для различных платформ.

## <a name="prerequisites"></a>Предварительные условия

Для выполнения действий, описанных в этой статье, необходимо следующее:

- Кластер Azure HDInsight. Сведения о создании кластера см. в статье о [начале работы с HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Или используйте кластер Spark и Hive, поддерживающий конечную точку Apache Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Моно](https://www.mono-project.com/docs/getting-started/install/). Mono требуется только для Linux и macOS.
- [PySpark интерактивную среду для Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Локальный каталог. В этой статье используется **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Установка Spark & Hive Tools

После выполнения необходимых условий можно установить Spark & средства Hive для Visual Studio Code, выполнив следующие действия.

1. Откройте Visual Studio Code.

2. В строке меню перейдите к **просмотру** > **расширения**.

3. В поле поиска введите **Spark & Hive**.

4. Выберите **Spark & средства Hive** в результатах поиска и нажмите кнопку **установить**.

   ![Куст & Spark для Visual Studio Code установки Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. При необходимости выберите **перезагрузить** .

## <a name="open-a-work-folder"></a>Открытие рабочей папки

Чтобы открыть рабочую папку и создать файл в Visual Studio Code, выполните следующие действия.

1. В строке меню выберите **файл** > **Открыть папку...**  >  **К:\хд\хдексампле**, а затем нажмите кнопку **выбрать папку** . Папка отображается в представлении **проводника** слева.

2. В представлении **обозревателя** выберите папку **хдексампле** , а затем щелкните значок **новый файл** рядом с рабочей папкой:

   ![значок нового файла Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Присвойте новому файлу имя с помощью команды `.hql` (запросы Hive) или расширения `.py` файла (скрипт Spark). В этом примере используется файл **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Настройка среды Azure

Для пользователя национального облака выполните следующие действия, чтобы сначала настроить среду Azure, а затем используйте команду **Azure: Sign** on для входа в Azure.

1. Выберите **файл** >  > **настройки параметры**.**Preferences**
2. Выполните поиск по следующей строке: **Azure: Cloud**.
3. Выберите Национальный облако из списка:

   ![Задание конфигурации записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Подключение к учетной записи Azure

Прежде чем отправлять скрипты в кластеры из Visual Studio Code, необходимо подключиться к учетной записи Azure или привязать кластер. Используйте Apache Ambari имя пользователя и пароль или учетную запись, присоединенную к домену. Чтобы подключиться к Azure, выполните следующие действия.

1. В строке меню перейдите к разделу **Просмотр** > **палитры команд...** и введите **Azure: вход**:

    ![Spark & средства Hive для входа Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Следуйте инструкциям по входу, чтобы войти в Azure. После подключения имя учетной записи Azure отобразится в строке состояния в нижней части окна Visual Studio Code.  

## <a name="link-a-cluster"></a>Связывание кластера

### <a name="link-azure-hdinsight"></a>Ссылка: Azure HDInsight

Вы можете связать Стандартный кластер с помощью имени пользователя, управляемого [Apache Ambari](https://ambari.apache.org/), или связать свой корпоративный пакет безопасности в кластере Hadoop с помощью имени пользователя домена (например: `user1@contoso.com`).

1. В строке меню перейдите к разделу **Просмотр** > **палитры команд...** и введите **Spark/Hive: Свяжите кластер**.

   ![Команда "связать кластер" в палитре команд](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Выберите связанный тип кластера **Azure HDInsight**.

3. Введите URL-адрес кластера HDInsight.

4. Введите имя пользователя Ambari. значение по умолчанию — **Admin**.

5. Введите пароль Ambari.

6. Выберите тип кластера.

7. Задайте отображаемое имя кластера (необязательно).

8. Просмотрите представление **OUTPUT** для проверки.

   > [!NOTE]  
   > Связанные имя пользователя и пароль используются, если кластер входит в подписку Azure и связан с кластером.  

### <a name="link-generic-livy-endpoint"></a>Ссылка: Универсальная конечная точка Livy

1. В строке меню перейдите к разделу **Просмотр** > **палитры команд...** и введите **Spark/Hive: Свяжите кластер**.

2. Выберите связанный тип кластера **Generic Livy Endpoint** (Общая конечная точка Livy).

3. Введите универсальную конечную точку Livy. Например: HTTP\://10.172.41.42:18080.

4. Выберите тип авторизации **Базовый** или **Нет**.  Если выбрать **базовый**:  
    &emsp;а. Введите имя пользователя Ambari. значение по умолчанию — **Admin**.  
    &emsp;Б. Введите пароль Ambari.

5. Просмотрите представление **OUTPUT** для проверки.

## <a name="list-clusters"></a>список кластеров

1. В строке меню перейдите к панели **Просмотр** > **палитры команд...** и введите **Spark/Hive: List Cluster**.

2. Выберите нужную подписку.

3. Просмотрите представление **OUTPUT**. В этом представлении отображается связанный кластер (или кластеры) и все кластеры в подписке Azure.

    ![Настройка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Настройка кластера по умолчанию

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Выберите созданный [ранее](#open-a-work-folder)файл **HelloWorld. HQL** . Он открывается в редакторе скриптов.

3. Щелкните правой кнопкой мыши редактор скриптов и выберите **Spark/Hive: задать кластер по умолчанию**.  

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Выберите кластер в качестве используемого по умолчанию для текущего файла скрипта. Средства автоматически обновляют **. **Файл конфигурации вскоде\сеттингс.жсон:

   ![Настройка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Отправка интерактивных запросов Hive и пакетных скриптов Hive

С помощью Spark & средств Hive для Visual Studio Code можно отправлять Интерактивные запросы Hive и пакетные скрипты Hive в кластеры.

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Выберите созданный [ранее](#open-a-work-folder)файл **HelloWorld. HQL** . Он открывается в редакторе скриптов.

3. Скопируйте и вставьте следующий код в файл Hive, а затем сохраните его:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Щелкните правой кнопкой мыши редактор скриптов и выберите **куст: Interactive** , чтобы отправить запрос, или используйте сочетание клавиш CTRL + ALT + I.  Выберите **Hive: пакет** для отправки сценария или используйте сочетание клавиш CTRL + ALT + H.  

6. Если вы не указали кластер по умолчанию, выберите кластер. Эти средства также позволяют отправить блок кода вместо всего файла скрипта с помощью контекстного меню. Через несколько секунд результаты запроса отобразятся на новой вкладке:

   ![Интерактивный Apache Hive результатов запроса](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Панель **результатов** : можно сохранить весь результат в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

    - Панель **сообщений** : при выборе номера **строки** она переходит к первой строке выполняемого скрипта.

## <a name="submit-interactive-pyspark-queries"></a>Отправка интерактивных запросов PySpark

Чтобы отправить Интерактивные запросы PySpark, выполните следующие действия.

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Создайте новый файл **HelloWorld.py** , следуя приведенным [выше](#open-a-work-folder) инструкциям.

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

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Выделите весь код, щелкните правой кнопкой мыши редактор скриптов и выберите **Spark: PySpark Interactive** , чтобы отправить запрос. Или используйте сочетание клавиш CTRL + ALT + I.

   ![контекстное меню интерактивной среды pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Выберите кластер, если вы не указали кластер по умолчанию. Через несколько секунд **Интерактивные результаты Python** отобразятся на новой вкладке. Эти средства также позволяют отправить блок кода вместо всего файла скрипта с помощью контекстного меню:

   ![интерактивная среда pyspark, интерактивное окно python](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Введите **%% info**, а затем нажмите клавиши SHIFT + ВВОД, чтобы просмотреть сведения о задании (необязательно):

   ![сведения о задании интерактивного просмотра pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Средство также поддерживает запрос **Spark SQL** :

   ![Результат интерактивного представления Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Состояние отправки отображается слева от нижней строки состояния при выполнении запросов. Если отображается состояние **PySpark Kernel (busy)** (Ядро PySpark (занято)), не отправляйте другие запросы.  

   > [!NOTE]
   >
   > Когда **расширение Python включено** в параметрах (выбрано по умолчанию), отправленные результаты взаимодействия pyspark будут использовать старое окно:
   >
   > ![интерактивная среда pyspark, расширение python отключено](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Отправка пакетного задания PySpark

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Создайте новый файл **BatchFile.py** , выполнив [предыдущие](#open-a-work-folder) шаги.

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

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Щелкните правой кнопкой мыши редактор скриптов, выберите команду **Spark: PySpark Batch**или нажмите сочетание клавиш CTRL + ALT + H.

6. Выберите кластер для отправки задания PySpark:

   ![Отправка выходных данных результатов задания Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

После отправки задания Python журналы отправки отображаются в окне **вывода** в Visual Studio Code. Также отображаются URL-адрес пользовательского интерфейса Spark и URL-адрес пользовательского интерфейса Yarn. Вы можете открыть этот URL-адрес в браузере для отслеживания состояния задания.

## <a name="apache-livy-configuration"></a>Конфигурация Apache Livy

Конфигурация [Apache Livy](https://livy.incubator.apache.org/) поддерживается. Его можно настроить в **. Вскоде\сеттингс.жсон** файл в папке рабочей области. Сейчас конфигурация Livy поддерживает только скрипт Python. Дополнительные сведения см. в разделе [readme Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Как активировать конфигурацию Livy**

Метод 1  
1. В строке меню выберите **файл** > **Preferences** > **настройки параметры**.
2. В поле **Поиск параметров** введите **Отправка задания HDInsight: Livy CONF**.  
3. Выберите **Изменить в settings.json** для соответствующего результата поиска.

Метод 2. Отправьте файл и обратите внимание, `.vscode` что папка автоматически добавляется в рабочую папку. Чтобы просмотреть конфигурацию Livy, выберите **. вскоде\сеттингс.жсон**.

+ Параметры проекта:

    ![Настройка HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Для параметров **дривермемори** и **ексекутормемори** задайте значение и единицу. Например: 1 ГБ или 1024m.

+ Поддерживаемые конфигурации Livy:

    **Опубликовать/батчес** Текст запроса

    | name | description | type |
    | --- | --- | --- |
    | файл | Файл, содержащий приложение для выполнения | Путь (обязательно) |
    | proxyUser | Пользователь, олицетворяемый при выполнении задания | Строка |
    | className | Класс main приложения в Java/Spark | Строка |
    | args | Аргументы командной строки для приложения | Список строк |
    | jars | JAR для использования в этом сеансе | Список строк | 
    | pyFiles | Файлы Python для использования в этом сеансе | Список строк |
    | files | Файлы, используемые в этом сеансе | Список строк |
    | driverMemory | Объем памяти, используемый для процесса драйвера | Строка |
    | driverCores | Число ядер, используемых для процесса драйвера | Int |
    | executorMemory | Объем памяти, используемый для каждого процесса исполнителя | Строка |
    | executorCores | Число ядер, используемых для каждого исполнителя | Int |
    | numExecutors | Число исполнителей, которые должны быть запущены для этого сеанса | Int |
    | archives | Архивы для использования в этом сеансе | Список строк |
    | очередь | Имя очереди YARN для отправки| Строка |
    | name | Имя этого сеанса | Строка |
    | conf | Свойства конфигурации Spark | Сопоставление key=val |

    Текст ответа созданного объекта пакета.

    | name | description | type |
    | --- | ---| --- |
    | ID | Идентификатор сеанса | Int |
    | appId | Идентификатор приложения для этого сеанса | Строка |
    | appInfo | Подробные сведения о приложении | Сопоставление key=val |
    | log | Строки журнала | Список строк |
    | state |Пакетное состояние | Строка |

    > [!NOTE]
    > Назначенная конфигурация Livy отображается в области вывода при отправке скрипта.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Интеграция с Azure HDInsight в проводнике

Вы можете предварительно просмотреть таблицу Hive в кластерах напрямую с помощью **Azure HDInsight** Explorer:

1. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure, если это еще не сделано.

2. Щелкните значок **Azure** из крайнего левого столбца.

3. В левой области разверните узел **Azure: HDINSIGHT**. В списке перечислены доступные подписки и кластеры.

4. Разверните кластер, чтобы просмотреть базу данных метаданных Hive и схему таблицы.

5. Щелкните правой кнопкой мыши таблицу Hive. Например: **hivesampletable**. Выберите **Предварительный просмотр**.

   ![& Hive для Visual Studio Code предварительной версии таблицы Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Откроется окно **предварительного просмотра результатов** :

   ![Окно результатов & Hive для Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Панель результатов

   Можно сохранить весь результат в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

- Панель сообщений
   1. Если число строк в таблице превышает 100, отображается следующее сообщение: "первые 100 строк отображаются для таблицы Hive".
   2. Если число строк в таблице меньше или равно 100, отображается следующее сообщение: "60 строк для таблицы Hive".
   3. Если в таблице нет содержимого, появится следующее сообщение: "`0 rows are displayed for Hive table.`"

        >[!NOTE]
        >
        >В Linux установите ксклип, чтобы включить данные копии таблицы.
        >
        >![Spark & Hive для Visual Studio Code в Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Дополнительные возможности

В Spark & Hive для Visual Studio Code также поддерживаются следующие функции.

- **Автозаполнение IntelliSense**. Всплывающие предложения для ключевых слов, методов, переменных и других программных элементов. Разные значки обозначают разные объекты:

    ![Spark & средства Hive для Visual Studio Code объектов IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Маркер ошибки IntelliSense**. Языковая служба подчеркивает редактирование ошибок в скрипте Hive.     
- **Подсветка синтаксиса**. Языковая служба использует разные цвета для различения переменных, ключевых слов, типов данных, функций и других программных элементов.

    ![Выделение синтаксиса в Spark & Hive Tools для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Роль только для чтения

Пользователи, которым назначена роль только для чтения для кластера, не могут отправлять задания в кластер HDInsight и не просматривать базу данных Hive. Обратитесь к администратору кластера, чтобы обновить роль на [**оператор кластера HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) в [портал Azure](https://ms.portal.azure.com/). Если у вас есть действительные учетные данные Ambari, можно связать кластер вручную, следуя приведенным ниже рекомендациям.

### <a name="browse-the-hdinsight-cluster"></a>Обзор кластера HDInsight  

При выборе обозревателя Azure HDInsight для развертывания кластера HDInsight вам будет предложено связать кластер, если у вас есть роль только для чтения для кластера. Используйте следующий метод для связи с кластером с помощью учетных данных Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Отправка задания в кластер HDInsight

При отправке задания в кластер HDInsight вам будет предложено связать кластер, если вы находитесь в роли только для чтения кластера. Чтобы создать ссылку на кластер с помощью учетных данных Ambari, выполните следующие действия.

### <a name="link-to-the-cluster"></a>Ссылка на кластер

1. Введите допустимое имя пользователя Ambari.
2. Введите допустимый пароль.

   ![Spark & средства Hive для Visual Studio Code имени пользователя](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & средства Hive для Visual Studio Code пароля](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Для проверки связанного `Spark / Hive: List Cluster` кластера можно использовать:
  >
  >![& Spark средства Hive для связанного модуля чтения Visual Studio Code](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2-го поколения

### <a name="browse-a-data-lake-storage-gen2-account"></a>Обзор учетной записи Data Lake Storage 2-го поколения

Выберите обозреватель Azure HDInsight, чтобы развернуть учетную запись Data Lake Storage 2-го поколения. Вам будет предложено ввести ключ доступа к хранилищу, если у вашей учетной записи Azure нет доступа к хранилищу Gen2. После проверки ключа доступа учетная запись Data Lake Storage 2-го поколения разворачивается в автоматическом состоянии.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Отправка заданий в кластер HDInsight с Data Lake Storage 2-го поколения

Отправка задания в кластер HDInsight с помощью Data Lake Storage 2-го поколения. Вам будет предложено ввести ключ доступа к хранилищу, если учетная запись Azure не имеет доступа на запись к хранилищу Gen2. После проверки ключа доступа задание будет успешно отправлено.

![Spark & средства Hive для Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Ключ доступа для учетной записи хранения можно получить из портал Azure. См. сведения о том, как [управлять ключами доступа к учетной записи хранения](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Удаление связи кластера

1. В строке меню выберите **вид** > **Палитра команд**, а затем введите **Spark/Hive: удалите связь с кластером**.  

2. Выберите кластер для разрыва связи.  

3. Проверьте представление **вывода** для проверки.  

## <a name="sign-out"></a>Выход  

В строке меню выберите **Просмотреть** > **палитру команд**, а затем введите **Azure: выйти**.

## <a name="next-steps"></a>Дальнейшие шаги

Видеоролик, демонстрирующий использование Spark & Hive для Visual Studio Code, см. в разделе [spark & Hive для Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
