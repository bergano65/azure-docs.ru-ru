---
title: 'Набор средств Azure для IntelliJ: создание приложений Spark для кластера HDInsight '
description: Сведения о разработке приложений Spark на языке Scala и их отправке в кластер HDInsight Spark с помощью набора средств Azure для IntelliJ.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 57a3ea73783af1edaee069bf2770d5c58f81772a
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496106"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Создание приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for IntelliJ

Подключаемый модуль из набора Azure Toolkit for IntelliJ позволяет разрабатывать приложения [Apache Spark](https://spark.apache.org/) на языке [Scala](https://www.scala-lang.org/) и отправлять их в кластер HDInsight Spark непосредственно из интегрированной среды разработки (IDE) IntelliJ. С помощью подключаемого модуля можно выполнять следующие действия:

* разрабатывать и отправлять приложения Scala Spark в кластер HDInsight Spark;
* получать доступ к ресурсам кластера Azure HDInsight Spark;
* разрабатывать и запускать приложения Scala Spark в локальной среде.

## <a name="prerequisites"></a>Технические условия

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
* [Комплект разработчика Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  В этом руководстве используется Java версии 8.0.202.
* IntelliJ IDEA. В этой статье используется среда [IntelliJ IDEA версии  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).
* WINUTILS.EXE.  Дополнительные сведения см. в статье [Problems running Hadoop on Windows](https://wiki.apache.org/hadoop/WindowsProblems) (Проблемы с запуском Hadoop в Windows).

## <a name="install-scala-plugin-for-intellij-idea"></a>Установка подключаемого модуля Scala для IntelliJ IDEA
Чтобы установить подключаемый модуль Scala, сделайте следующее:

1. Откройте IntelliJ IDEA.

2. На экране приветствия выберите **Configure** (Настройка) > **Plugins** (Подключаемые модули), чтобы открыть окно **подключаемых модулей**.
   
    ![Включение подключаемого модуля Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

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

    |  Свойство   | Описание   |  
    | ----- | ----- |  
    |Имя проекта| Введите имя.  В этом руководстве используется `myApp`.|  
    |Project&nbsp;location (Расположение проекта)| Введите необходимое расположение для сохранения проекта.|
    |Project SDK (Пакет SDK проекта)| Это поле может быть пустым при первом использовании IDEA.  Выберите **New...** (Создать...) и перейдите к JDK.|
    |Версия Spark|Мастер создания интегрирует правильную версию пакетов SDK для Spark и Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x**. В этом примере используется **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Выбор пакета SDK для Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Выберите **Готово**.  Может пройти несколько минут, прежде чем проект станет доступным.

8. Проект Spark автоматически создаст артефакт. Чтобы просмотреть артефакт, сделайте следующее:

   a. В строке меню выберите **File** (Файл) > **Project Structure...** (Структура проекта…).

   2. В окне **структуры проекта** щелкните **Artifacts** (Артефакты).  

   c. После просмотра артефакта щелкните **Cancel** (Отменить).

      ![Сведения об артефакте в диалоговом окне](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Добавьте исходный код приложения, сделав следующее:

    a. В иерархии проекта перейдите к **myApp** > **src** > **main** > **scala**.  

    2. Щелкните правой кнопкой мыши папку **scala**, а затем выберите **New** (Новый) > **Scala Class** (Класс Scala).

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
       
   ![Ссылка на Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. В Azure Explorer щелкните правой кнопкой мыши узел **Azure**, а затем выберите **Войти**.
   
   ![Ссылка на Azure Explorer](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)


3. В **вход в Azure** диалоговое окно, выберите **входа на устройство**, а затем выберите **вход**.

    ![Диалоговое окно входа в Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. В **входа на устройство Azure** диалоговом окне щелкните **Копировать & откройте**.
   
   ![Диалоговое окно входа в Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-5.png)

5. В интерфейсе браузера, вставьте код и нажмите кнопку **Далее**.
   
   ![Диалоговое окно входа в Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-6.png)

6. Введите свои учетные данные Azure, а затем закройте браузер.
   
   ![Диалоговое окно входа в Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-7.png)

7. После входа в диалоговом окне **Select Subscriptions** (Выбор подписок) будут перечислены все подписки Azure, связанные с указанными учетными данными. Выберите свою подписку, а затем нажмите кнопку **Выбрать**.

    ![Диалоговое окно выбора подписок](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Из **Azure Explorer**, разверните **HDInsight** Чтобы просмотреть кластеры HDInsight Spark в ваших подписках.

    ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

9.  Далее можно развернуть узел имени кластера, чтобы увидеть ресурсы (например, учетные записи хранения), связанные с ним.

    ![Развернутый узел имени кластера](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Связывание кластера
Можно связать кластер HDInsight с помощью управляемого имени пользователя Apache Ambari. Аналогичным образом, присоединенный к домену кластер HDInsight можно связать с помощью домена и имени пользователя, например user1@contoso.com. Также вы можете связать кластер службы Livy.

1. В строке меню выберите **Представление** > **Окно инструментов** > **Azure Explorer**.

2. В Azure Explorer щелкните правой кнопкой мыши узел **HDInsight**, а затем — **Link A Cluster** (Связать кластер).

   ![контекстное меню связывания кластера](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. Доступные параметры в окне **Link A Cluster** (Связывание кластера) зависят от значения, которое вы выбрали из раскрывающегося списка **Link Resource Type** (Связать тип ресурса).  Введите свои значения, а затем нажмите кнопку **ОК**.

    * **Кластер HDInsight**  
  
        |Свойство |Value |
        |----|----|
        |Link Resource Type (Связать тип ресурса)|Из раскрывающегося списка выберите **HDInsight Cluster** (Кластер HDInsight).|
        |Cluster Name/URL (Имя или URL-адрес кластера)| Введите имя кластера.|
        |тип проверки подлинности;| Оставьте значение **Basic Authentication** (Обычная аутентификация)|
        |Имя пользователя| Введите имя пользователя кластера. Значение по умолчанию — admin.|
        |Пароль| Введите пароль для этого имени пользователя.|
    
        ![связать диалоговое окно для кластера Hdinsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy Service** (Служба Livy)  
  
        |Свойство |Value |
        |----|----|
        |Link Resource Type (Связать тип ресурса)|Из раскрывающегося списка выберите **Livy Service** (Служба Livy).|
        |Livy Endpoint (Конечная точка Livy)| Введите конечную точку Livy|
        |Имя кластера,| Введите имя кластера.|
        |Yarn Endpoint (Конечная точка Yarn)|Необязательный элемент.|
        |тип проверки подлинности;| Оставьте значение **Basic Authentication** (Обычная аутентификация)|
        |Имя пользователя| Введите имя пользователя кластера. Значение по умолчанию — admin.|
        |Пароль| Введите пароль для этого имени пользователя.|

        ![связать диалоговое окно кластера Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. В узле **HDInsight** отобразится связанный кластер.

   ![связанный кластер](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

2. В **обозревателе Azure** также можно удалить связь кластера.

   ![кластер с удаленной связью](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Запуск приложения Spark Scala в кластере HDInsight Spark
После создания приложение Scala можно отправить в кластер.

1. В иерархии проекта перейдите к **myApp** > **src** > **main** > **scala** > **myApp**.  Щелкните правой кнопкой мыши папку **myApp** и выберите **Submit Spark Application** (Запустить приложение Spark). Этот параметр скорее всего будет расположен в нижней части списка.
    
      ![Команда Submit Spark Application to HDInsight (Отправить приложение Spark в HDInsight)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. В диалоговом окне **Submit Spark Application** (Запустить приложение Spark) выберите **1. Spark on HDInsight** (1. Spark в HDInsight).

3. В окне **Edit configuration** (Изменить конфигурацию) укажите следующие значения, а затем щелкните **ОК**.

    |Свойство |Value |
    |----|----|
    |Spark clusters (Linux only) (Кластеры Spark (только для Linux))|Выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.|
    |Select an Artifact to submit (Выбор артефакта для запуска)|Оставьте параметр по умолчанию.|
    |Main class name (Имя основного класса)|В выбранном файле основной класс является значением по умолчанию. Класс можно изменить, выбрав кнопку с многоточием ( **...** ) и другой класс.|
    |Job configurations (Конфигурация заданий)|Вы можете изменить значения по умолчанию ключами или значениями. Дополнительные сведения см. в статье [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Аргументы командной строки|При необходимости можно ввести аргументы для основного класса, разделив их пробелом.|
    |Referenced Jars (Ссылки на JAR-файлы) и Referenced Files (Ссылки на файлы)|Вы можете указать пути ссылки на JAR-файлы и обычные файлы, если они есть. Дополнительные сведения [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) (Сведения о конфигурации Spark).  Ознакомьтесь также со статьей [Краткое руководство. Использование Обозревателя службы хранилища Azure для создания большого двоичного объекта в хранилище объекта](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Job Upload Storage (Хранилище обновлений заданий)|Разверните раздел, чтобы отобразить дополнительные параметры.|
    |тип хранилища;|Из раскрывающегося списка выберите **Use Azure Blob to upload** (Для передачи использовать большой двоичный объект Azure).|
    |Учетная запись хранения|Введите имя своей учетной записи хранения.|
    |Storage Key (Ключ хранилища)|Введите свой ключ к хранилищу данных.|
    |Контейнер хранилища|Если вы ввели значения в поля **Учетная запись хранения** и **Storage Key** (Ключ хранилища), из раскрывающегося списка выберите контейнер хранилища.|

    ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Щелкните **SparkJobRun**, чтобы отправить проект в выбранный кластер. Вкладка **Удаленное задание Spark в кластере** отображает ход выполнения задания в нижней части. Вы можете остановить приложение, щелкнув красную кнопку. В разделе "Доступ к кластерам HDInsight Spark и управление ими с помощью набора средств Azure для IntelliJ" ниже в этой статье показано, как получить доступ к выходным данным задачи.  
      
    ![Окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Локальная и удаленная отладка приложений Apache Spark в кластере HDInsight 
Мы также рекомендуем еще один способ отправки приложения Spark в кластер. Он заключается в задании параметров **конфигураций запуска и отладки** в интегрированной среде разработки. Дополнительные сведения см. в статье об [удаленной и локальной отладке приложений Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ через SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Доступ к кластерам HDInsight Spark и управление ими с помощью набора средств Azure для IntelliJ
При помощи набора средств Azure для IntelliJ можно выполнять разные операции.  Большинство операций инициируются из **Azure Explorer**.  В строке меню выберите **Представление** > **Окно инструментов** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Доступ к представлению задания

1. В Azure Explorer перейдите к **HDInsight** > \<<ваш кластер>**Задания**.

    ![Узел представления задания](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. В области справа на вкладке **Spark Job View** (Просмотр заданий Spark) отображаются все приложения, запускаемые в кластере. Выберите имя приложения, дополнительные сведения о котором вы хотите просмотреть.

    ![Сведения о приложении](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Чтобы отобразить базовые сведения о выполняющемся задании, наведите указатель мыши на граф задания. Чтобы просмотреть этапы графа и сведения, создаваемые каждым заданием, выберите узел на графе задания.

    ![Сведения об этапе задания](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Выберите вкладку **Журнал**, чтобы просмотреть часто используемые журналы, включая *Driver Stderr*, *Driver Stdout* и *Directory Info*.

    ![Подробные сведения журнала](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

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

    |Свойство |Value |
    |----|----|
    |Job main class (Основной класс задания)|В выбранном файле основной класс является значением по умолчанию. Класс можно изменить, выбрав кнопку с многоточием ( **...** ) и другой класс.|
    |Переменные среды|Убедитесь в правильности имени HADOOP_HOME.|
    |WINUTILS.exe location (Расположение WINUTILS.exe)|Убедитесь, что указан правильный путь.|

    ![Локальная консоль. Настройка конфигурации](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. В иерархии проекта перейдите к **myApp** > **src** > **main** > **scala** > **myApp**.  

6. В строке меню перейдите к **Tools** (Инструменты) > **Spark Console** (Консоль Spark) > **Run Spark Local Console(Scala)** (Запустить локальную консоль Spark(Scala)).

7. Затем могут появиться два диалоговых окна с запросом об автоматическом исправлении зависимостей. Если вы согласны, выберите **Auto Fix** (Автоматическое исправление).

    ![Автоматическое исправление Spark1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Автоматическое исправление Spark2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. Консоль должна выглядеть, как показано ниже. В окне консоли введите `sc.appName`, а затем нажмите сочетание клавиш CTRL+ENTER.  После этого отобразится результат. Вы можете завершить работу локальной консоли, нажав красную кнопку.

    ![Результат локальной консоли](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Консоль (Scala) Spark в интерактивном режиме Livy
Она поддерживается только на IntelliJ 2018.2 и 2018.3.

1. В строке меню перейдите к **Run** (Запуск) > **Edit Configurations...** (Изменить конфигурацию...).

2. В левой панели окна **Run/Debug Configurations** (Конфигурации запуска или отладки) перейдите к **Apache Spark on HDInsight** (Apache Spark в HDInsight) >  **[Spark on HDInsight] myApp** (myApp [Spark в HDInsight]).

3. В главном окне выберите вкладку **Remotely Run in Cluster** (Запустить удаленно в кластере).

4. Укажите следующие значения, а затем нажмите кнопку **OK**.

    |Свойство |Value |
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
Когда пользователи отправки задания в кластер с разрешением роли только для чтения, Ambari учетные данные не требуется.

### <a name="link-cluster-from-context-menu"></a>Кластер ссылку в контекстном меню

1. Вход с учетной записью, роли только для чтения.
       
2. Из **Azure Explorer**, разверните **HDInsight** Чтобы просмотреть кластеры HDInsight в подписке. Кластеры помечены **«Роли: Читатель»** только иметь разрешение роли только для чтения.

    ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-15.png)

3. Правой кнопкой мыши щелкните кластер, с разрешением роли только для чтения. Выберите **связать этот кластер** из контекстного меню для связывания кластера. Введите имя пользователя Ambari и пароль.

  
    ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-11.png)

4. Если кластер связан успешно, будет обновляться HDInsight.
   Этап кластера будет становятся связанными.
  
    ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-8.png)



### <a name="link-cluster-by-expanding-jobs-node"></a>Связь кластера, развернув узел "задания"

1. Нажмите кнопку **заданий** узел, **кластера задания отказано в доступе** всплывающее окно.
   
2. Нажмите кнопку **связать этот кластер** для связывания кластера.
   
    ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Связь кластера из окна конфигураций запуска и отладки

1. Создайте конфигурацию HDInsight. Затем выберите **удаленно запускать в кластере**.
   
2. Выберите кластер, которая имеет разрешение роли только для чтения для **(только Linux) кластеры Spark**. Предупреждающее сообщение показано. Можно щелкнуть **связать этот кластер** для связывания кластера.
   
   ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/create-config-1.png)
   
### <a name="view-storage-accounts"></a>Просмотреть учетные записи хранения

* Для кластеров с разрешением роли только для чтения, нажмите кнопку **учетные записи хранения** узел, **отказано в доступе хранилища** всплывающее окно. Можно щелкнуть **откройте обозреватель службы хранилища Azure** чтобы открыть обозреватель службы хранилища.
     
   ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-14.png)

   ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-10.png)

* Для связанного кластеров щелкните **учетные записи хранения** узел, **отказано в доступе хранилища** всплывающее окно. Можно щелкнуть **хранилища Azure, откройте** чтобы открыть обозреватель службы хранилища.
     
   ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-13.png)


   ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-12.png)
  

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Преобразование имеющихся приложений IntelliJ IDEA для использования набора средств Azure для IntelliJ

Имеющиеся приложения Spark Scala, созданные в IntelliJ IDEA, можно преобразовать и сделать совместимыми с набором средств Azure для IntelliJ. Затем вы сможете использовать подключаемый модуль для отправки приложений в кластер HDInsight Spark.

1. Для имеющегося приложения Spark Scala, созданного с помощью IntelliJ IDEA, откройте соответствующий IML-файл.

2. На корневом уровне вы увидите элемент **module** следующего вида:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Измените элемент, добавив `UniqueKey="HDInsightTool"` , чтобы элемент **module** выглядел следующим образом.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Сохраните изменения. Ваше приложение станет совместимым с набором средств Azure для IntelliJ. Это можно проверить, правой кнопкой мыши щелкнув имя проекта в структуре проекта. Во всплывающем меню должен появиться пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Ошибка в локальной среде. *Используйте больший размер кучи*.
В Spark 1.6 при использовании 32-разрядного пакета SDK для Java в локальной среде выполнения могут возникать следующие ошибки:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Это связано с тем, что размер кучи недостаточно велик для запуска Spark. Для Spark требуется не меньше 471 МБ. (Дополнительные сведения см. в статье о [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) Простое решение – это использовать 64-разрядный пакет SDK для Java. Кроме того, можно изменить параметры виртуальной машины Java в IntelliJ, добавив следующие параметры:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Добавление параметров в поле VM options (Параметры виртуальной машины) в IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Часто задаваемые вопросы
Если кластер занят, может появиться приведенная ниже ошибка.

![IntelliJ получает сообщение об ошибке, если кластер занят](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![IntelliJ получает сообщение об ошибке, если кластер занят](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Отзывы и известные проблемы
Сейчас просмотр выходных данных Spark напрямую не поддерживается.

С любыми отзывами и предложениями, а также в случае возникновения проблем при работе с этим подключаемым модулем вы можете отправить сообщение по электронному адресу hdivstool@microsoft.com.

## <a name="seealso"></a>Дальнейшие действия
* [Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [Создание приложений Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [Удаленная отладка приложений Apache Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Использование Apache Spark MLlib для Создание приложения машинного обучения Apache Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Apache Spark MLlib для создания приложения машинного обучения и анализа набора данных](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Удаленная отладка приложений Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Удаленная или локальная отладка приложений Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ через SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в Azure Toolkit for Eclipse для создания приложений Apache Spark](apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
