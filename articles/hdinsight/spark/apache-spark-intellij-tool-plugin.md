---
title: 'Учебник по Azure Toolkit for IntelliJ: Приложение Spark для кластера HDInsight'
description: Учебник. Разработка приложений Spark на языке Scala и их отправка в кластер HDInsight Spark с помощью Azure Toolkit for IntelliJ.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: c19d867553290ba956515fddc50c4fd836877860
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995801"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Руководство по Использование Azure Toolkit for IntelliJ для создания приложений Apache Spark для кластера HDInsight

В этом учебнике показано, как с помощью подключаемого модуля Azure Toolkit for IntelliJ разрабатывать приложения Apache Spark на языке [Scala](https://www.scala-lang.org/) и отправлять их в кластер HDInsight Spark непосредственно из интегрированной среды разработки (IDE) IntelliJ. С помощью подключаемого модуля можно выполнять следующие действия:

* разрабатывать и отправлять приложения Scala Spark в кластер HDInsight Spark;
* получать доступ к ресурсам кластера Azure HDInsight Spark;
* разрабатывать и запускать приложения Scala Spark в локальной среде.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Использование подключаемого модуля Azure Toolkit for IntelliJ
> * Разработка приложений Apache Spark
> * Отправка приложения в кластер Azure HDInsight

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

* [Комплект разработчика Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  В этом руководстве используется Java версии 8.0.202.

* IntelliJ IDEA. В этой статье используется среда [IntelliJ IDEA версии  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Установка подключаемого модуля Scala для IntelliJ IDEA

Чтобы установить подключаемый модуль Scala, сделайте следующее:

1. Откройте IntelliJ IDEA.

2. На экране приветствия выберите **Configure** (Настройка) > **Plugins** (Подключаемые модули), чтобы открыть окно **подключаемых модулей**.

    ![Включение подключаемого модуля Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Выберите **Install** (Установить) в области подключаемого модуля Scala в новом окне.  

    ![Установка подключаемого модуля Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. После успешной установки подключаемого модуля необходимо перезапустить интегрированную среду разработки.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Создание приложения Spark Scala для кластера HDInsight Spark

1. Запустите IntelliJ IDEA и выберите **Create New Project** (Создать проект), чтобы открыть окно **New Project** (Новый проект).

2. На левой панели щелкните **Azure Spark/HDInsight**.

3. В главном окне выберите **Spark Project (Scala)** (Проект Spark (Scala)).

4. Из раскрывающегося списка **Build tool** (Инструмент сборки) выберите один из следующих вариантов:
   * **Maven** для поддержки мастера создания проекта Scala.
   * **SBT** для управления зависимостями и создания проекта Scala.

     ![Диалоговое окно нового проекта](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Щелкните **Далее**.

6. В окне **New Project** (Новый проект) укажите следующую информацию:  

    |  Свойство   | ОПИСАНИЕ   |  
    | ----- | ----- |  
    |Имя проекта| Введите имя.  В этом руководстве используется `myApp`.|  
    |Project&nbsp;location (Расположение проекта)| Введите необходимое расположение для сохранения проекта.|
    |Project SDK (Пакет SDK проекта)| Это поле может быть пустым при первом использовании IDEA.  Выберите **New...** (Создать...) и перейдите к JDK.|
    |Версия Spark|Мастер создания интегрирует правильную версию пакетов SDK для Spark и Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x**. В этом примере используется **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Выбор пакета SDK для Spark](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Выберите **Готово**.  Может пройти несколько минут, прежде чем проект станет доступным.

8. Проект Spark автоматически создаст артефакт. Чтобы просмотреть артефакт, сделайте следующее:

   a. В строке меню выберите **File** (Файл) > **Project Structure...** (Структура проекта…).

   b. В окне **структуры проекта** щелкните **Artifacts** (Артефакты).  

   c. После просмотра артефакта щелкните **Cancel** (Отменить).

      ![Сведения об артефакте в диалоговом окне](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Добавьте исходный код приложения, сделав следующее:

    a. В иерархии проекта перейдите к **myApp** > **src** > **main** > **scala**.  

    b. Щелкните правой кнопкой мыши папку **scala**, а затем выберите **New** (Новый) > **Scala Class** (Класс Scala).

   ![Команды для создания класса Scala в проекте](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. В диалоговом окне **Create New Scala Class** (Создание класса Scala) введите имя, в раскрывающемся списке **Kind** (Вид) выберите **Object** (Объект) и нажмите кнопку **ОК**.

     ![Диалоговое окно создания класса Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. После этого файл **MyApp.scala** откроется в главном представлении. Замените код по умолчанию на приведенный ниже:  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    Этот код считывает данные из файла HVAC.csv (доступного для всех кластеров HDInsight Spark), извлекает строки, содержащие только одну цифру в седьмом столбце CSV-файла, и записывает результат в `/HVACOut` в используемом по умолчанию контейнере хранилища для кластера.

## <a name="connect-to-your-hdinsight-cluster"></a>Подключение к кластеру HDInsight
Пользователь может [войти в подписку Azure](#sign-in-to-your-azure-subscription) или [связать кластер HDInsight](#link-a-cluster) с помощью имени пользователя и пароля Ambari или учетных данных присоединения к домену, чтобы подключить свой кластер HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Войдите в свою подписку Azure.

1. В строке меню выберите **Представление** > **Окно инструментов** > **Azure Explorer**.

   ![Отображение Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. В Azure Explorer щелкните правой кнопкой мыши узел **Azure**, а затем выберите **Войти**.

   ![Щелчок правой кнопкой мыши в Azure Explorer](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. В диалоговом окне **Вход в Azure** выберите **Имя пользователя устройства**, а затем — **Войти**.

    ![Вид Azure Explorer 2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. В диалоговом окне **Azure Device Login** (Вход в систему устройства Azure) щелкните **Copy&Open** (Копировать и открыть).

   ![Вид Azure Explorer 5](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. В интерфейсе браузера вставьте код и нажмите кнопку **Далее**.

   ![Вид Azure Explorer 6](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Введите учетные данные Azure и закройте браузер.

   ![Вид Azure Explorer 7](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. После входа в диалоговом окне **Select Subscriptions** (Выбор подписок) будут перечислены все подписки Azure, связанные с указанными учетными данными. Выберите свою подписку, а затем нажмите кнопку **Выбрать**.

    ![Диалоговое окно выбора подписок](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. В **Azure Explorer** разверните **HDInsight**, чтобы просмотреть кластеры HDInsight Spark в своей подписке.

    ![Вид Azure Explorer 3](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Далее можно развернуть узел имени кластера, чтобы увидеть ресурсы (например, учетные записи хранения), связанные с ним.

    ![Развернутый узел имени кластера](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Связывание кластера

Можно связать кластер HDInsight с помощью управляемого имени пользователя Apache Ambari. Аналогичным образом, присоединенный к домену кластер HDInsight можно связать с помощью домена и имени пользователя, например `user1@contoso.com`. Также вы можете связать кластер службы Livy.

1. В строке меню выберите **Представление** > **Окно инструментов** > **Azure Explorer**.

1. В Azure Explorer щелкните правой кнопкой мыши узел **HDInsight**, а затем — **Link A Cluster** (Связать кластер).

   ![контекстное меню связывания кластера](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Доступные параметры в окне **Link A Cluster** (Связывание кластера) зависят от значения, которое вы выбрали из раскрывающегося списка **Link Resource Type** (Связать тип ресурса).  Введите свои значения, а затем нажмите кнопку **ОК**.

    * **Кластер HDInsight**  
  
        |Свойство |Значение |
        |----|----|
        |Link Resource Type (Связать тип ресурса)|Из раскрывающегося списка выберите **HDInsight Cluster** (Кластер HDInsight).|
        |Cluster Name/URL (Имя или URL-адрес кластера)| Введите имя кластера.|
        |тип проверки подлинности;| Оставьте значение **Basic Authentication** (Обычная аутентификация)|
        |Имя пользователя| Введите имя пользователя кластера. Значение по умолчанию — admin.|
        |Пароль| Введите пароль для этого имени пользователя.|

        ![диалоговое окно связывания кластера HDInsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy Service** (Служба Livy)  
  
        |Свойство |Значение |
        |----|----|
        |Link Resource Type (Связать тип ресурса)|Из раскрывающегося списка выберите **Livy Service** (Служба Livy).|
        |Livy Endpoint (Конечная точка Livy)| Введите конечную точку Livy|
        |Имя кластера,| Введите имя кластера.|
        |Yarn Endpoint (Конечная точка Yarn)|Необязательный элемент.|
        |тип проверки подлинности;| Оставьте значение **Basic Authentication** (Обычная аутентификация)|
        |Имя пользователя| Введите имя пользователя кластера. Значение по умолчанию — admin.|
        |Пароль| Введите пароль для этого имени пользователя.|

        ![диалоговое окно связывания кластера Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. В узле **HDInsight** отобразится связанный кластер.

   ![связанный кластер](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. В **обозревателе Azure** также можно удалить связь кластера.

   ![кластер с удаленной связью](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Запуск приложения Spark Scala в кластере HDInsight Spark

После создания приложение Scala можно отправить в кластер.

1. В иерархии проекта перейдите к **myApp** > **src** > **main** > **scala** > **myApp**.  Щелкните правой кнопкой мыши папку **myApp** и выберите **Submit Spark Application** (Запустить приложение Spark). Этот параметр скорее всего будет расположен в нижней части списка.

      ![Команда Submit Spark Application to HDInsight (Отправить приложение Spark в HDInsight)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. В диалоговом окне **Submit Spark Application** (Запустить приложение Spark) выберите **1. Spark on HDInsight** (1. Spark в HDInsight).

3. В окне **Edit configuration** (Изменить конфигурацию) укажите следующие значения, а затем щелкните **ОК**.

    |Свойство |Значение |
    |----|----|
    |Spark clusters (Linux only) (Кластеры Spark (только для Linux))|Выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.|
    |Select an Artifact to submit (Выбор артефакта для запуска)|Оставьте параметр по умолчанию.|
    |Main class name (Имя основного класса)|В выбранном файле основной класс является значением по умолчанию. Класс можно изменить, выбрав кнопку с многоточием ( **...** ) и другой класс.|
    |Job configurations (Конфигурация заданий)|Вы можете изменить значения по умолчанию ключами или значениями. Дополнительные сведения см. в статье [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Аргументы командной строки|При необходимости можно ввести аргументы для основного класса, разделив их пробелом.|
    |Referenced Jars (Ссылки на JAR-файлы) и Referenced Files (Ссылки на файлы)|Вы можете указать пути ссылки на JAR-файлы и обычные файлы, если они есть. Вы можете также просматривать файлы в виртуальной файловой системе Azure, которая сейчас поддерживает только кластер ADLS 2 поколения. Дополнительные сведения [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) (Сведения о конфигурации Spark).  Ознакомьтесь также со статьей [Краткое руководство. Использование Обозревателя службы хранилища Azure для создания большого двоичного объекта в хранилище объекта](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Job Upload Storage (Хранилище обновлений заданий)|Разверните раздел, чтобы отобразить дополнительные параметры.|
    |тип хранилища;|Из раскрывающегося списка выберите **Use Azure Blob to upload** (Для передачи использовать большой двоичный объект Azure).|
    |Учетная запись хранения|Введите имя своей учетной записи хранения.|
    |Storage Key (Ключ хранилища)|Введите свой ключ к хранилищу данных.|
    |Контейнер хранилища|Если вы ввели значения в поля **Учетная запись хранения** и **Storage Key** (Ключ хранилища), из раскрывающегося списка выберите контейнер хранилища.|

    ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Щелкните **SparkJobRun**, чтобы отправить проект в выбранный кластер. Вкладка **Удаленное задание Spark в кластере** отображает ход выполнения задания в нижней части. Вы можете остановить приложение, щелкнув красную кнопку. В разделе "Доступ к кластерам HDInsight Spark и управление ими с помощью набора средств Azure для IntelliJ" ниже в этой статье показано, как получить доступ к выходным данным задачи.  

    ![Окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Локальная и удаленная отладка приложений Apache Spark в кластере HDInsight 

Мы также рекомендуем еще один способ отправки приложения Spark в кластер. Он заключается в задании параметров **конфигураций запуска и отладки** в интегрированной среде разработки. Дополнительные сведения см. в статье об [удаленной и локальной отладке приложений Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ через SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Доступ к кластерам HDInsight Spark и управление ими с помощью набора средств Azure для IntelliJ

При помощи набора средств Azure для IntelliJ можно выполнять разные операции.  Большинство операций инициируются из **Azure Explorer**.  В строке меню выберите **Представление** > **Окно инструментов** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Доступ к представлению задания

1. В Azure Explorer перейдите к **HDInsight** > \<<ваш кластер>**Задания**.

    ![Узел представления задания](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. В области справа на вкладке **Spark Job View** (Просмотр заданий Spark) отображаются все приложения, запускаемые в кластере. Выберите имя приложения, дополнительные сведения о котором вы хотите просмотреть.

    ![Сведения о приложении](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Чтобы отобразить базовые сведения о выполняющемся задании, наведите указатель мыши на граф задания. Чтобы просмотреть этапы графа и сведения, создаваемые каждым заданием, выберите узел на графе задания.

    ![Сведения об этапе задания](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Выберите вкладку **Журнал**, чтобы просмотреть часто используемые журналы, включая *Driver Stderr*, *Driver Stdout* и *Directory Info*.

    ![Подробные сведения журнала](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Вы также можете просмотреть пользовательский интерфейс журнала Spark и пользовательский интерфейс YARN (на уровне приложения), щелкнув ссылку в верхней части окна.

### <a name="access-the-spark-history-server"></a>Доступ к серверу журнала Spark

1. В Azure Explorer разверните **HDInsight**, щелкните имя кластера Spark правой кнопкой мыши и выберите **Open Spark History UI** (Открыть пользовательский интерфейс журнала Spark).  
2. При появлении запроса введите учетные данные администратора для кластера, указанные при настройке кластера.

3. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено, по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("myApp")`. Следовательно, приложение Spark называется **myApp**.

### <a name="start-the-ambari-portal"></a>Запуск портала Ambari

1. В Azure Explorer разверните **HDInsight**, щелкните имя кластера Spark правой кнопкой мыши и выберите **Open Cluster Management Portal (Ambari)** (Открыть портал управления кластерами (Ambari)).  

2. При появлении запроса введите учетные данные администратора для кластера. Вы указали эти учетные данные во время установки кластера.

### <a name="manage-azure-subscriptions"></a>Управление подписками Azure

По умолчанию набор средств Azure для IntelliJ содержит список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, к которым необходимо получить доступ.  

1. В Azure Explorer щелкните правой кнопкой мыши корневой узел **Azure**, а затем щелкните **Выбрать подписки**.  

2. В окне **Выбрать подписки** снимите флажки напротив подписок, доступ к которым вам не требуется, и выберите **Закрыть**.

## <a name="spark-console"></a>Консоль Spark

Можно запустить консоль (Scala) Spark в локальном режиме или в интерактивном режиме Livy.

### <a name="spark-local-consolescala"></a>Консоль (Scala) Spark в локальном режиме

Убедитесь, что вы выполнили предварительные условия WINUTILS.EXE.

1. В строке меню перейдите к **Run** (Запуск) > **Edit Configurations...** (Изменить конфигурацию...).

2. В левой панели окна **Run/Debug Configurations** (Конфигурации запуска или отладки) перейдите к **Apache Spark on HDInsight** (Apache Spark в HDInsight) >  **[Spark on HDInsight] myApp** (myApp [Spark в HDInsight]).

3. В главном окне выберите вкладку **Locally Run** (Локальный запуск).

4. Укажите следующие значения, а затем нажмите кнопку **OK**.

    |Свойство |Значение |
    |----|----|
    |Job main class (Основной класс задания)|В выбранном файле основной класс является значением по умолчанию. Класс можно изменить, выбрав кнопку с многоточием ( **...** ) и другой класс.|
    |Переменные среды|Убедитесь в правильности имени HADOOP_HOME.|
    |WINUTILS.exe location (Расположение WINUTILS.exe)|Убедитесь, что указан правильный путь.|

    ![Локальная консоль. Настройка конфигурации](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. В иерархии проекта перейдите к **myApp** > **src** > **main** > **scala** > **myApp**.  

6. В строке меню перейдите к **Tools** (Инструменты) > **Spark Console** (Консоль Spark) > **Run Spark Local Console(Scala)** (Запустить локальную консоль Spark(Scala)).

7. Затем могут появиться два диалоговых окна с запросом об автоматическом исправлении зависимостей. Если вы согласны, выберите **Auto Fix** (Автоматическое исправление).

    ![Автоматическое исправление Spark1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![Автоматическое исправление Spark2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. Консоль должна выглядеть, как показано ниже. В окне консоли введите `sc.appName`, а затем нажмите сочетание клавиш CTRL+ENTER.  После этого отобразится результат. Вы можете завершить работу локальной консоли, нажав красную кнопку.

    ![Результат локальной консоли](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Консоль (Scala) Spark в интерактивном режиме Livy

Она поддерживается только на IntelliJ 2018.2 и 2018.3.

1. В строке меню перейдите к **Run** (Запуск) > **Edit Configurations...** (Изменить конфигурацию...).

2. В левой панели окна **Run/Debug Configurations** (Конфигурации запуска или отладки) перейдите к **Apache Spark on HDInsight** (Apache Spark в HDInsight) >  **[Spark on HDInsight] myApp** (myApp [Spark в HDInsight]).

3. В главном окне выберите вкладку **Remotely Run in Cluster** (Запустить удаленно в кластере).

4. Укажите следующие значения, а затем нажмите кнопку **OK**.

    |Свойство |Значение |
    |----|----|
    |Spark clusters (Linux only) (Кластеры Spark (только для Linux))|Выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.|
    |Main class name (Имя основного класса)|В выбранном файле основной класс является значением по умолчанию. Класс можно изменить, выбрав кнопку с многоточием ( **...** ) и другой класс.|

    ![Интерактивная консоль. Настройка конфигурации](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. В иерархии проекта перейдите к **myApp** > **src** > **main** > **scala** > **myApp**.  

6. В строке меню перейдите к **Tools** (Инструменты) > **Spark Console** (Консоль Spark) > **Run Spark Livy Interactive Session Console(Scala)** (Запустить консоль интерактивного сеанса Livy Spark(Scala)).

7. Консоль должна выглядеть, как показано ниже. В окне консоли введите `sc.appName`, а затем нажмите сочетание клавиш CTRL+ENTER.  После этого отобразится результат. Вы можете завершить работу локальной консоли, нажав красную кнопку.

    ![Результат интерактивной консоли](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Отправка выделенного фрагмента в консоль Spark

Удобно предвидеть результат сценария, отправив код в локальную консоль или консоль интерактивного сеанса Livy(Scala). Вы можете выделить код в файле Scala, а затем щелкнуть правой кнопкой мыши **Send Selection To Spark Console** (Отправка выделенного фрагмента в консоль Spark). Выбранный код будет отправлен в консоль и выполнен. Результат появится после кода в консоли. Консоль проверит ошибки, если они существуют.  

   ![Отправка выделенного фрагмента в консоль Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="reader-only-role"></a>Роль только для чтения

Когда пользователи отправляют задания в кластер с разрешением на роль только для чтения, требуются учетные данные Ambari.

### <a name="link-cluster-from-context-menu"></a>Связывание кластера из контекстного меню

1. Войдите с помощью учетной записи роли только для чтения.

2. В **Azure Explorer** разверните **HDInsight**, чтобы просмотреть кластеры HDInsight в своей подписке. Кластеры с пометкой **Role:Reader**, имеют только разрешение роли только для чтения.

    ![Вид Azure Explorer 15](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Щелкните правой кнопкой мыши кластер с разрешением роли только для чтения. Выберите **Link this cluster** (Связать этот кластер) из контекстного меню, чтобы связать кластер. Введите имя пользователя и пароль Ambari.

    ![Вид Azure Explorer 11](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Если кластер связан успешно, HDInsight будет обновлен.
   Этап кластера станет связанным.
  
    ![Вид Azure Explorer 8](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Связывание кластера путем развертывания узла "Задания"

1. Щелкните узел **Задания** и появится всплывающее окно **Cluster Job Access Denied** (Доступ к заданию кластера запрещен).

2. Щелкните **Link this cluster** (Связать этот кластер), чтобы связать кластер.

    ![Вид Azure Explorer 9](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Связывание кластера из окна Run/Debug Configurations (Конфигурации запуска и отладки)

1. Создайте конфигурацию HDInsight. Выберите **Remotely Run in Cluster** (Удаленный запуск в кластере).

2. Выберите кластер, у которого есть разрешение роли только для чтения для **Spark clusters(Linux only)** (Кластеры Spark (только Linux)). Появится сообщение с предупреждением. Вы можете щелкнуть **Link this cluster** (Связать этот кластер), чтобы связать кластер.

   ![Создание конфигурации 1](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Просмотр учетных записей хранения

* Для кластеров с разрешением роли только для чтения щелкните узел **Учетные записи хранения** и появится всплывающее окно **Storage Access Denied** (Доступ к хранилищу запрещен). Выберите **Открыть обозреватель службы хранилища Azure**, чтобы открыть Обозреватель службы хранилища.

   ![Вид Azure Explorer 14](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Вид Azure Explorer 10](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Для связанных кластеров щелкните узел **Учетные записи хранения** и появится всплывающее окно **Storage Access Denied** (Доступ к хранилищу запрещен). Выберите **Открыть обозреватель службы хранилища Azure**, чтобы открыть Обозреватель службы хранилища.

   ![Вид Azure Explorer 13](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![Вид Azure Explorer 12](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Преобразование имеющихся приложений IntelliJ IDEA для использования набора средств Azure для IntelliJ

Имеющиеся приложения Spark Scala, созданные в IntelliJ IDEA, можно преобразовать и сделать совместимыми с набором средств Azure для IntelliJ. Затем вы сможете использовать подключаемый модуль для отправки приложений в кластер HDInsight Spark.

1. Для имеющегося приложения Spark Scala, созданного с помощью IntelliJ IDEA, откройте соответствующий IML-файл.

2. На корневом уровне вы увидите элемент **module** следующего вида:

        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Измените элемент, добавив `UniqueKey="HDInsightTool"` , чтобы элемент **module** выглядел следующим образом.

        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Сохраните изменения. Ваше приложение станет совместимым с набором средств Azure для IntelliJ. Это можно проверить, правой кнопкой мыши щелкнув имя проекта в структуре проекта. Во всплывающем меню должен появиться пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите созданный кластер, сделав следующее:

1. Войдите на [портале Azure](https://portal.azure.com/).

1. В поле **Поиск** в верхней части страницы введите **HDInsight**.

1. Выберите **Кластеры HDInsight** в разделе **Службы**.

1. В списке кластеров HDInsight, который отобразится, выберите **...** рядом с кластером, созданным при работе с этим учебником.

1. Нажмите кнопку **Удалить**. Выберите **Да**.

![Удаление кластера HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Удаление кластера HDInsight")

## <a name="next-steps"></a>Дополнительная информация

Из этого учебника вы узнали, как с помощью подключаемого модуля Azure Toolkit for IntelliJ разрабатывать приложения Apache Spark на языке [Scala](https://www.scala-lang.org/) и отправлять их в кластер HDInsight Spark непосредственно из интегрированной среды разработки (IDE) IntelliJ. Теперь переходите к следующей статье, в которой объясняется, как перенести зарегистрированные в Apache Spark данные в средство бизнес-аналитики, например в Power BI.

> [!div class="nextstepaction"]
> [Анализ данных с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
