---
title: Azure HDInsight для Visual Studio Code
description: Узнайте, как использовать средства & Hive (Azure HDInsight) Spark для Visual Studio Code. Используйте средства для создания и отправки запросов и сценариев.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2020
ms.custom: tracking-python
ms.openlocfilehash: d00e4ad8b0a9f1f50c30144867babcd8c782734d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087135"
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

2. В строке меню перейдите к **просмотру**  >  **расширения**.

3. В поле поиска введите **Spark & Hive**.

4. Выберите **Spark & средства Hive** в результатах поиска и нажмите кнопку **установить**.

   ![Куст & Spark для Visual Studio Code установки Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. При необходимости выберите **перезагрузить** .

## <a name="open-a-work-folder"></a>Открытие рабочей папки

Чтобы открыть рабочую папку и создать файл в Visual Studio Code, выполните следующие действия.

1. В строке меню выберите **файл**  >  **Открыть папку...**  >  **К:\хд\хдексампле**, а затем нажмите кнопку **выбрать папку** . Папка отображается в представлении **проводника** слева.

2. В представлении **обозревателя** выберите папку **хдексампле** , а затем щелкните значок **новый файл** рядом с рабочей папкой:

   ![значок нового файла Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Присвойте новому файлу имя с помощью команды `.hql` (запросы Hive) или `.py` расширения файла (скрипт Spark). В этом примере используется файл **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Настройка среды Azure

Для пользователя национального облака выполните следующие действия, чтобы сначала настроить среду Azure, а затем используйте команду **Azure: Sign** on для входа в Azure.

1. Выберите **файл**  >  **Preferences**  >  **настройки параметры**.
2. Выполните поиск по следующей строке: **Azure: Cloud**.
3. Выберите Национальный облако из списка:

   ![Задание конфигурации записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Подключение к учетной записи Azure

Прежде чем отправлять скрипты в кластеры из Visual Studio Code, пользователь может войти в подписку Azure или [связать кластер HDInsight](#link-a-cluster). Для подключения к кластеру HDInsight используйте имя пользователя, пароль или учетные данные, присоединенные к домену, для кластера ESP. Чтобы подключиться к Azure, выполните следующие действия.

1. В строке меню перейдите к разделу **Просмотр**  >  **палитры команд...** и введите **Azure: вход**:

    ![Spark & средства Hive для входа Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Следуйте инструкциям по входу, чтобы войти в Azure. После подключения имя учетной записи Azure отобразится в строке состояния в нижней части окна Visual Studio Code.  

## <a name="link-a-cluster"></a>Связывание кластера

### <a name="link-azure-hdinsight"></a>Ссылка: Azure HDInsight

Вы можете связать Стандартный кластер с помощью имени пользователя, управляемого [Apache Ambari](https://ambari.apache.org/), или связать свой корпоративный пакет безопасности в кластере Hadoop с помощью имени пользователя домена (например: `user1@contoso.com` ).

1. В строке меню перейдите к разделу **Просмотр**  >  **палитры команд...** и введите **Spark/Hive: Свяжите кластер**.

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

1. В строке меню перейдите к разделу **Просмотр**  >  **палитры команд...** и введите **Spark/Hive: Свяжите кластер**.

2. Выберите связанный тип кластера **Generic Livy Endpoint** (Общая конечная точка Livy).

3. Введите универсальную конечную точку Livy. Например: HTTP \: //10.172.41.42:18080.

4. Выберите тип авторизации **Базовый** или **Нет**.  Если выбрать **базовый**:  
    &emsp;а. Введите имя пользователя Ambari. значение по умолчанию — **Admin**.  
    &emsp;Б. Введите пароль Ambari.

5. Просмотрите представление **OUTPUT** для проверки.

## <a name="list-clusters"></a>список кластеров

1. В строке меню перейдите к панели **Просмотр**  >  **палитры команд...** и введите **Spark/Hive: List Cluster**.

2. Выберите нужную подписку.

3. Просмотрите представление **OUTPUT**. В этом представлении отображается связанный кластер (или кластеры) и все кластеры в подписке Azure.

    ![Настройка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Настройка кластера по умолчанию

1. Повторно откройте папку **хдексампле** , которая была рассмотрена [ранее](#open-a-work-folder), если она была закрыта.  

2. Выберите созданный [ранее](#open-a-work-folder)файл **HelloWorld. HQL** . Он открывается в редакторе скриптов.

3. Щелкните правой кнопкой мыши редактор скриптов и выберите **Spark/Hive: задать кластер по умолчанию**.  

4. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

5. Выберите кластер в качестве используемого по умолчанию для текущего файла скрипта. Средства автоматически обновляют **.VSCode\settings.jsв** файле конфигурации:

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

Пользователи могут выполнять PySpark в интерактивном режиме следующими способами:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>Использование интерактивной команды PySpark в файле КОРРЕКТИРОВКи
Чтобы отправить запросы с помощью интерактивной команды PySpark, выполните следующие действия.

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

4. Запрос на установку ядра PySpark отображается в правом нижнем углу окна. Можно нажать кнопку " **установить** ", чтобы перейти к установке PySpark. чтобы пропустить этот шаг, нажмите кнопку **пропустить** .

   ![Установка ядра pyspark](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. Если потребуется установить его позже, можно перейти к **File**  >  **Preference**  >  **параметрам настройки**файла, а затем снять флажок **Hdinsight: включить пропуск Pyspark установки** в параметрах. 
    
    ![Установка ядра pyspark](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. Если на шаге 4 Установка прошла успешно, в правом нижнем углу окна появится окно сообщения "PySpark успешно установлено". Нажмите кнопку " **перезагрузить** ", чтобы перезагрузить окно.
    ![pyspark успешно установлен](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)

7. [Подключитесь](#connect-to-an-azure-account) к учетной записи Azure или свяжите кластер, если вы еще не сделали этого.

8. Выделите весь код, щелкните правой кнопкой мыши редактор скриптов и выберите **Spark: PySpark Interactive** , чтобы отправить запрос. Или используйте сочетание клавиш CTRL + ALT + I.

    ![контекстное меню интерактивной среды pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

9. Выберите кластер, если вы не указали кластер по умолчанию. Через несколько секунд **Интерактивные результаты Python** отобразятся на новой вкладке. Щелкните PySpark, чтобы переключить ядро на **PySpark**, и код будет выполнен успешно. Эти средства также позволяют отправить блок кода вместо всего файла скрипта с помощью контекстного меню:

   ![интерактивная среда pyspark, интерактивное окно python](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

10. Введите **%% info**, а затем нажмите клавиши SHIFT + ВВОД, чтобы просмотреть сведения о задании (необязательно):

    ![сведения о задании интерактивного просмотра pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

Средство также поддерживает запрос **Spark SQL** :

   ![результат интерактивного представления pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Выполнить интерактивный запрос в файле КОРРЕКТИРОВКи с помощью комментария #%%

1. Добавьте **#%%** перед кодом корректировки, чтобы получить возможность работы с записной книжкой.

    ![Добавить #%%](./media/hdinsight-for-vscode/run-cell.png)

2. Щелкните **запустить ячейку**. Через несколько секунд на новой вкладке отображаются результаты интерактивного окна Python.

   ![результаты выполнения ячеек](./media/hdinsight-for-vscode/run-cell-get-results.png)

   > [!NOTE]  
   > Когда ядро или параметры изменяются, используйте **интерпретатор Python: SELECT для запуска команды Jupyter Server** и **перезапустите ядро IPython Notebook**, а затем перезагрузите VSCode, чтобы устранить проблему.

## <a name="leverage-ipynb-support-from-python-extension"></a>Использование поддержки IPYNB из расширения Python

1. Можно создать Jupyter Notebook с помощью команды из палитры команд или путем создания нового ipynb-файла в рабочей области. Дополнительные сведения см. [в разделе Работа с записными книжками Jupyter в Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Щелкните PySpark, чтобы переключить ядро в **PySpark**, а затем щелкните **запустить ячейку**, после чего отобразится результат.

   ![выполнение результатов ipynb](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
>
>MS-Python >= версия 2020.5.78807 не поддерживается в этой области, является [известной проблемой](#known-issues).

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

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Интеграция с брокером удостоверений HDInsight (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Подключение к кластеру HDInsight ESP с помощью ID Broker (ХИБ)

Вы можете выполнить обычные действия для входа в подписку Azure, чтобы подключиться к кластеру HDInsight ESP с ИД брокером (ХИБ). После входа вы увидите список кластеров в Azure Explorer. Указания см. в разделе [Подключение к кластеру HDInsight](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Выполнение задания Hive или PySpark в кластере HDInsight ESP с ИД брокером (ХИБ)

Для запуска задания Hive можно выполнить обычные действия, чтобы отправить задание в кластер HDInsight ESP с ИДЕНТИФИКАТОРом брокера (ХИБ). Дополнительные инструкции см. в статье [Отправка интерактивных запросов Hive и пакетных сценариев Hive](#submit-interactive-hive-queries-and-hive-batch-scripts) .

Для запуска интерактивного задания PySpark можно выполнить обычные действия, чтобы отправить задание в кластер HDInsight ESP с ИДЕНТИФИКАТОРом брокера (ХИБ). Дополнительные инструкции см. в статье [Отправка интерактивных запросов PySpark](#submit-interactive-pyspark-queries) .

Для выполнения пакетного задания PySpark можно выполнить обычные действия, чтобы отправить задание в кластер HDInsight ESP с ИДЕНТИФИКАТОРом брокера (ХИБ). Дополнительные инструкции см. в разделе [отправка пакетного задания PySpark](#submit-pyspark-batch-job) .


## <a name="apache-livy-configuration"></a>Конфигурация Apache Livy

Конфигурация [Apache Livy](https://livy.incubator.apache.org/) поддерживается. Его можно настроить в файле **.VSCode\settings.jsдля** файла в папке рабочей области. Сейчас конфигурация Livy поддерживает только скрипт Python. Дополнительные сведения см. в разделе [readme Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Как активировать конфигурацию Livy**

Метод 1  
1. В строке меню выберите **файл**  >  **Preferences**  >  **настройки параметры**.
2. В поле **Поиск параметров** введите **Отправка задания HDInsight: Livy CONF**.  
3. Выберите **Изменить в settings.json** для соответствующего результата поиска.

Метод 2. Отправьте файл и обратите внимание, что `.vscode` папка автоматически добавляется в рабочую папку. Чтобы просмотреть конфигурацию Livy, выберите **.vscode\settings.json**.

+ Параметры проекта:

    ![Настройка HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Для параметров **дривермемори** и **ексекутормемори** задайте значение и единицу. Например: 1 ГБ или 1024m.

+ Поддерживаемые конфигурации Livy:

    **Опубликовать/батчес** Текст запроса

    | name | description | тип |
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

    | name | description | тип |
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
   3. Если в таблице нет содержимого, появится следующее сообщение: " `0 rows are displayed for Hive table.` "

        >[!NOTE]
        >
        >В Linux установите ксклип, чтобы включить данные копии таблицы.
        >
        >![Spark & Hive для Visual Studio Code в Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Дополнительные функции

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
  >`Spark / Hive: List Cluster`Для проверки связанного кластера можно использовать:
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

1. В строке меню выберите **вид**  >  **Палитра команд**, а затем введите **Spark/Hive: удалите связь с кластером**.  

2. Выберите кластер для разрыва связи.  

3. Проверьте представление **вывода** для проверки.  

## <a name="sign-out"></a>Выйти  

В строке меню выберите **Просмотреть**  >  **палитру команд**, а затем введите **Azure: выйти**.

## <a name="known-issues"></a>Известные проблемы
### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>MS-Python >= версия 2020.5.78807 не поддерживается для этого расширения 

"Не удалось подключиться к записной книжке Jupyter". является известной проблемой для Python версии >= 2020.5.78807. Для предотвращения этой проблемы пользователям рекомендуется использовать **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** версию MS-Python.

![Известные проблемы](./media/hdinsight-for-vscode/known-issue.png)

## <a name="next-steps"></a>Дальнейшие шаги

Видеоролик, демонстрирующий использование Spark & Hive для Visual Studio Code, см. в разделе [spark & Hive для Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
