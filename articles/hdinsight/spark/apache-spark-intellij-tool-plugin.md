---
title: 'Создание приложений Spark для кластера HDInsight с помощью Azure Toolkit for IntelliJ '
description: Сведения о разработке приложений Spark на языке Scala и их отправке в кластер HDInsight Spark с помощью набора средств Azure для IntelliJ.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: maxluk
ms.openlocfilehash: b2bf79d90c741e09c683e4520b05b31ba2fee1da
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582774"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Создание приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for IntelliJ

Подключаемый модуль из набора Azure Toolkit for IntelliJ позволяет разрабатывать приложения [Apache Spark](https://spark.apache.org/) на языке [Scala](https://www.scala-lang.org/) и отправлять их в кластер HDInsight Spark непосредственно из интегрированной среды разработки (IDE) IntelliJ. С помощью подключаемого модуля можно выполнять следующие действия:

* разрабатывать и отправлять приложения Scala Spark в кластер HDInsight Spark;
* получать доступ к ресурсам кластера Azure HDInsight Spark;
* разрабатывать и запускать приложения Scala Spark в локальной среде.

Чтобы создать проект, просмотрите видео о [создании приложений Apache Spark с помощью Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ).

> [!IMPORTANT]
> Вы можете использовать этот подключаемый модуль, чтобы создавать и отправлять приложения только в кластер HDInsight Spark на Linux.
> 

## <a name="prerequisites"></a>Предварительные требования

- Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
- Комплект разработчика Oracle Java. Его можно установить с [веб-сайта Oracle](https://aka.ms/azure-jdks).
- IntelliJ IDEA. В этой статье используется версия 2017.1. Его можно установить с [веб-сайта JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Установка набора средств Azure для IntelliJ
Инструкции по установке см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Начало работы
Пользователь может [войти в подписку Azure](#sign-in-to-your-azure-subscription) или [связать кластер HDInsight](#link-a-cluster) с помощью имени пользователя и пароля Ambari или учетных данных присоединения к домену.


## <a name="sign-in-to-your-azure-subscription"></a>Войдите в свою подписку Azure.

1. Запустите IntelliJ IDE и откройте Azure Explorer. В меню **View** (Вид) выберите **Tool Windows** (Окна инструментов) и выберите **Azure Explorer**.
       
   ![Ссылка на Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Щелкните правой кнопкой мыши узел **Azure**, а затем выберите **Sign In** (Войти).

1. В диалоговом окне **Azure Sign In** (Вход в Azure) нажмите кнопку **Sign in** (Войти), а затем введите свои учетные данные Azure.

    ![Диалоговое окно входа в Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. После входа в диалоговом окне **Select Subscriptions** (Выбор подписок) будут перечислены все подписки Azure, связанные с указанными учетными данными. Нажмите кнопку **Select** (Выбрать).

    ![Диалоговое окно выбора подписок](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. На вкладке **Azure Explorer** разверните **HDInsight**, чтобы просмотреть кластеры HDInsight Spark в своей подписке.
   
    ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. Далее можно развернуть узел имени кластера, чтобы увидеть ресурсы (например, учетные записи хранения), связанные с ним.
   
    ![Развернутый узел имени кластера](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Связывание кластера
Можно связать обычный кластер HDInsight с помощью управляемого имени пользователя Apache Ambari. Аналогичным образом, присоединенный к домену кластер HDInsight можно связать с помощью домена и имени пользователя, например user1@contoso.com. Также вы можете связать кластер службы Livy.

1. Выберите **Link a cluster** (Связывание кластера) в **обозревателе Azure**.

   ![контекстное меню связывания кластера](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Существует два варианта для связывания кластеров. 

   * Для связывания кластера HDInsight в поле **HDInsight Cluster** выберите **Сведения о кластере**, введите **Название кластера / URL-адрес**, **Имя пользователя** и **Пароль**.

      ![диалоговое окно связывания кластера HDInsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * Для связывания кластера службы Livy выберите в поле **Служба Livy** **Сведения о кластере**, введите **Конечную точку Livy** и **имя кластера**. **Конечная точка Yarn** является дополнительной. В поле **Проверка подлинности** предоставляются два варианта: **обычная проверка подлинности** и **без проверки подлинности**. При выборе **обычной проверки подлинности** необходимо указать **имя пользователя** и **пароль**. В случае сбоя аутентификации проверьте имя пользователя и пароль.
      
      ![диалоговое окно связывания кластера Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. Если данные введены правильно, связанный кластер отобразится в узле **HDInsight**. Теперь в этот связанный кластер можно отправить приложение.

   ![связанный кластер](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. В **обозревателе Azure** также можно удалить связь кластера.
   
   ![кластер с удаленной связью](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Создание приложения Spark Scala в кластере HDInsight Spark

1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне **Новый проект** выполните следующие шаги: 

   a. Выберите **HDInsight** > **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)).

   b. В списке **средств сборки** выберите один из следующих вариантов:

      * **Maven.** Для поддержки мастера создания проекта Scala.
      * **SBT.** Для управления зависимостями и создания проекта Scala.

    ![Диалоговое окно нового проекта](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Щелкните **Далее**.

1. Мастер создания проектов Scala автоматически определяет, установлен ли подключаемый модуль Scala. Щелкните **Установить**.

   ![Проверка подключаемого модуля Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Чтобы скачать подключаемый модуль Scala, нажмите кнопку **ОК**. Следуйте инструкциям, чтобы перезапустить IntelliJ. 

   ![Диалоговое окно установки подключаемого модуля Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. В окне **нового проекта** сделайте следующее:  

    ![Выбор пакета SDK для Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Введите имя и расположение проекта.

   b. В раскрывающемся списке для параметра **Project SDK** (Пакет SDK проекта) выберите **Java 1.8** для кластера Spark 2.x или **Java 1.7** для кластера Spark 1.x.

   c. В раскрывающемся списке для параметра **Spark version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK для Spark и пакета SDK для Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x**. В этом примере используется **Spark 2.0.2 (Scala 2.11.8)**.

1. Выберите **Готово**.

1. Проект Spark автоматически создаст артефакт. Чтобы просмотреть артефакт, сделайте следующее:

   a. В меню **File** (Файл) выберите **Project Structure** (Структура проекта).

   b. В диалоговом окне **Project Structure** (Структура проекта) щелкните **артефакты**, чтобы просмотреть созданный по умолчанию артефакт. Вы также можете создать собственный артефакт, щелкнув значок "плюс" (**+**).

      ![Сведения об артефакте в диалоговом окне](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Добавьте исходный код приложения, сделав следующее:

   a. В обозревателе проектов щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **New** (Создать) и выберите **Scala Class** (Класс Scala).
      
      ![Команды для создания класса Scala в обозревателе проектов](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. В диалоговом окне **Create New Scala Class** (Создание класса Scala) введите имя, в поле **Kind** (Вид) выберите **Object** (Объект) и нажмите кнопку **ОК**.
      
      ![Диалоговое окно создания класса Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. Вставьте в файл **MyClusterApp.scala** следующий код. Этот код считывает данные из файла HVAC.csv (доступного для всех кластеров HDInsight Spark), извлекает строки, содержащие только одну цифру в седьмом столбце CSV-файла, и записывает результат в **/HVACOut** в используемом по умолчанию контейнере хранилища для кластера.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Запуск приложения Spark Scala в кластере HDInsight Spark
После создания приложение Scala можно отправить в кластер.

1. В обозревателе проектов разместите файл Java или Scala, а затем выберите в контекстном меню **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).
    
      ![Команда Submit Spark Application to HDInsight (Отправить приложение Spark в HDInsight)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. В диалоговом окне конфигурации укажите следующие значения, а затем нажмите кнопку **SparkJobRun**.

      ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * В поле **Spark clusters (Linux only)**(Кластеры Spark (только для Linux)) выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.

    * Выберите артефакт из проекта IntelliJ или с жесткого диска.

    * Поле **Имя основного класса**. В выбранном файле основной класс является значением по умолчанию. Класс можно изменить, выбрав кнопку с многоточием (**...**) и другой класс.   

    * Поле **Задание конфигурации**. Значения по умолчанию устанавливаются, как показано выше на изображении. Вы можете изменить значение или добавить новый ключ и значение для отправки задания. Дополнительные сведения см. в разделе [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![Диалоговое окно отправки в Spark, конфигурация задания](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * Поле **Аргументы командной строки**. При необходимости вы можете ввести значения аргументов для основного класса, разделив их пробелом.

    * Поля **Ссылка на JAR-файлы** и **Ссылка на файлы**. Вы можете указать пути ссылки на JAR-файлы и обычные файлы, если они есть. Дополнительные сведения о конфигурации Apache Spark см. [здесь](https://spark.apache.org/docs/latest/configuration.html#runtime-environment). 

      ![Диалоговое окно отправки в Spark, JAR-файлы](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]
       > Для получения дополнительных сведений об отправке ссылок на JAR-файлы и обычные файлы ознакомьтесь со статьей [о загрузке ресурсов для кластера](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)
                         
    * **Передача пути**. Можно указать местоположение хранилища для подачи ресурсов проекта Jar или Scala. Поддерживаются четыре типа хранилища: **Azure Blob**, **использование интерактивного сеанса Spark для отправки артефактов**, **использование учетной записи хранения кластера по умолчанию** и **ADLS 1-го поколения**. Снимок экрана, приведенный ниже, является примером для Azure Blob.

        ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. Щелкните **SparkJobRun**, чтобы отправить проект для выбранного кластера. Вкладка **Удаленное задание Spark в кластере** отображает ход выполнения задания в нижней части. Вы можете остановить приложение, щелкнув красную кнопку. В разделе "Доступ к кластерам HDInsight Spark и управление ими с помощью набора средств Azure для IntelliJ" ниже в этой статье показано, как получить доступ к выходным данным задачи.
      
     ![Окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Локальная и удаленная отладка приложений Apache Spark в кластере HDInsight 
Мы также рекомендуем еще один способ отправки приложения Spark в кластер. Он заключается в задании параметров **конфигураций запуска и отладки** в интегрированной среде разработки. Дополнительные сведения см. в статье об [удаленной и локальной отладке приложений Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ через SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Доступ к кластерам HDInsight Spark и управление ими с помощью набора средств Azure для IntelliJ
При помощи набора средств Azure для IntelliJ можно выполнять разные операции.

### <a name="access-the-job-view"></a>Доступ к представлению задания
1. В Azure Explorer разверните **HDInsight**, а затем выберите имя кластера Spark, после чего щелкните **Задания**.  

    ![Узел представления задания](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. В области справа на вкладке **Spark Job View** (Просмотр заданий Spark) отображаются все приложения, запускаемые в кластере. Выберите имя приложения, дополнительные сведения о котором вы хотите просмотреть.

    ![Сведения о приложении](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

1. Чтобы отобразить базовые сведения о выполняющемся задании, наведите указатель мыши на граф задания. Чтобы просмотреть этапы графа и сведения, создаваемые каждым заданием, выберите узел на графе задания.

    ![Сведения об этапе задания](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Выберите вкладку **Журнал**, чтобы просмотреть часто используемые журналы, включая *Driver Stderr*, *Driver Stdout* и *Directory Info*.

    ![Подробные сведения журнала](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. Вы также можете просмотреть пользовательский интерфейс журнала Spark и пользовательский интерфейс YARN (на уровне приложения), щелкнув ссылку в верхней части окна.

### <a name="access-the-spark-history-server"></a>Доступ к серверу журнала Spark
1. В Azure Explorer разверните **HDInsight**, щелкните имя кластера Spark правой кнопкой мыши и выберите **Open Spark History UI** (Открыть пользовательский интерфейс журнала Spark). 

1. При появлении запроса введите учетные данные администратора для кластера, указанные при настройке кластера.

1. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено, по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("MyClusterApp")`. Следовательно, приложение Spark называется **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Запуск портала Ambari
1. В Azure Explorer разверните **HDInsight**, щелкните имя кластера Spark правой кнопкой мыши и выберите **Open Cluster Management Portal (Ambari)** (Открыть портал управления кластерами (Ambari)). 

1. При появлении запроса введите учетные данные администратора для кластера. Вы указали эти учетные данные во время установки кластера.

### <a name="manage-azure-subscriptions"></a>Управление подписками Azure
По умолчанию набор средств Azure для IntelliJ содержит список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, к которым необходимо получить доступ. 

1. В Azure Explorer щелкните правой кнопкой мыши корневой узел **Azure**, а затем выберите **Управление подписками**. 

1. В диалоговом окне снимите флажки напротив подписок, доступ к которым вам не требуется, и выберите **Закрыть**. Если вы хотите выйти из своей подписки Azure, выберите **Выйти**.

## <a name="spark-console"></a>Консоль Spark
Можно запустить консоль (Scala) Spark в локальном режиме или в интерактивном режиме Livy.

### <a name="spark-local-consolescala"></a>Консоль (Scala) Spark в локальном режиме
1. Настройте конфигурацию, если у вас ее нет. В окне **Run/Debug Configurations** (Конфигурации запуска и отладки) щелкните **+**->**Azure HDInsight Spark**, выберите вкладку **Locally Run** (Локальный запуск) и **Remotely Run in Cluster** (Удаленный запуск в кластере), выберите основной класс, а затем щелкните **OК**.

    ![Локальная консоль. Настройка конфигурации](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
2. Откройте файл, соответствующий файлу основного класса, и щелкните правой кнопкой мыши **Spark Console** (Консоль Spark), а затем — **Run Spark Local Console(Scala)** (Запустить локальную консоль Spark(Scala)). Или для запуска консоли перейдите к меню **Средства**->**Консоль Spark**->**Запустить локальную консоль Spark (Scala)**. Затем появятся два диалоговых окна для запроса об автоматическом исправлении зависимостей. Просто нажмите кнопку **Auto Fix** (Автоматически исправить).

    ![Автоматическое исправление Spark1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Автоматическое исправление Spark2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

    ![Точка входа Spark в локальном режиме](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

3. После запуска локальной консоли. Это выглядит следующим образом. Можно осуществить любые действия. Например, введите **sc.appName**, нажмите клавиши CTRL+ВВОД и результат отобразится. Вы можете завершить работу локальной консоли, нажав красную кнопку.

    ![Результат локальной консоли](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Консоль (Scala) Spark в интерактивном режиме Livy
Она поддерживается только на IntelliJ 2018.2 и 2018.3.

1. Настройте конфигурацию, если у вас ее нет. В окне **Конфигурации запуска и отладки** нажмите кнопку **+**->**Azure HDInsight Spark**, выберите вкладку **Удаленный запуск в кластере**, выберите имя кластера и основной класс, а затем нажмите кнопку **ОК**.

    ![Интерактивная консоль. Добавление конфигурации записи](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![Интерактивная консоль. Настройка конфигурации](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. Откройте файл, соответствующий основному классу, и щелкните правой кнопкой мыши **Консоль Spark**, затем нажмите **Запустить консоль (Scala) Spark в интерактивном режиме Livy**. Или для запуска консоли перейдите к меню **Средства**, затем нажмите **Консоль Spark**, затем **Запустить консоль (Scala) Spark в интерактивном режиме Livy**.

3. После запуска консоли можно сделать все необходимые действия. Например, введите **sc.appName**, нажмите клавиши CTRL+ВВОД и результат отобразится.

    ![Результат интерактивной консоли](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Отправка выделенного фрагмента в консоль Spark
Удобно предвидеть результат сценария, отправив коды в локальную консоль или консоль интерактивного сеанса Livy(Scala). Вы можете выделить некоторые коды в файле Scala, а затем щелкнуть правой кнопкой мыши **Send Selection To Spark Console** (Отправка выделенного фрагмента в консоль Spark). Выбранные коды будут отправлены в консоль и выполнены. Результат появится после кодов в консоли. Консоль проверит ошибки, если они существуют. 

   ![Отправка выделенного фрагмента в консоль Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Преобразование имеющихся приложений IntelliJ IDEA для использования набора средств Azure для IntelliJ
Имеющиеся приложения Spark Scala, созданные в IntelliJ IDEA, можно преобразовать и сделать совместимыми с набором средств Azure для IntelliJ. Затем вы сможете использовать подключаемый модуль для отправки приложений в кластер HDInsight Spark.

1. Для имеющегося приложения Spark Scala, созданного с помощью IntelliJ IDEA, откройте соответствующий IML-файл.

1. На корневом уровне вы увидите элемент **module** следующего вида:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Измените элемент, добавив `UniqueKey="HDInsightTool"` , чтобы элемент **module** выглядел следующим образом.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Сохраните изменения. Ваше приложение станет совместимым с набором средств Azure для IntelliJ. Это можно проверить, щелкнув имя проекта в обозревателе проектов правой кнопкой мыши. Во всплывающем меню должен появиться пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Ошибка *Please use a larger heap size* (Используйте больший размер кучи) в локальной среде
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
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [создание приложений Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [удаленная отладка приложений Apache Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Руководство. Создание приложения машинного обучения Apache Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark MLlib для создания приложения машинного обучения и анализа набора данных](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью пользовательской библиотеки Python и кластера Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленная отправка заданий Spark в кластер Azure HDInsight с помощью Apache Spark REST API](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Удаленная отладка приложений Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Удаленная или локальная отладка приложений Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ через SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Создание приложений Spark для кластера HDInsight с помощью Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
