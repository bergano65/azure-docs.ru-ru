---
title: 'Azure Toolkit for Eclipse. Создание приложений Scala для HDInsight Spark '
description: Использование средств HDInsight из набора средств Azure для Eclipse для разработки приложений Spark на языке Scala и их отправки в кластер HDInsight Spark непосредственно из интегрированной среды разработки Eclipse.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: 87ac2283e96081297fdd54c1c131aa5b78a5973a
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814258"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Создание приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for Eclipse

Использование средств HDInsight из набора Azure Toolkit for [Eclipse](https://www.eclipse.org/) для разработки приложений [Apache Spark](https://spark.apache.org/) на языке [Scala](https://www.scala-lang.org/) и их отправки в кластер Azure HDInsight Spark непосредственно из интегрированной среды разработки Eclipse. Подключаемый модуль средств HDInsight можно использовать по-разному.

* Для разработки и отправки приложений Scala Spark в кластер HDInsight Spark.
* Для доступа к ресурсам кластера Azure HDInsight Spark.
* Для разработки и локального запуска приложений Scala Spark.

> [!IMPORTANT]  
> Вы можете использовать этот инструмент, чтобы создавать и отправлять приложения только в кластер HDInsight Spark на Linux.
> 
> 

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
* Комплект разработчика Oracle Java версии 8, который используется для среды выполнения интегрированной среды разработки Eclipse. Его можно скачать с [веб-сайта Oracle](https://aka.ms/azure-jdks).
* Интегрированная среда разработки Eclipse. В этой статье используется Eclipse Neon. Его можно установить с [веб-сайта Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Установка средств HDInsight в наборе средств Azure для Eclipse и подключаемого модуля Scala

### <a name="install-azure-toolkit-for-eclipse"></a>Установка набора средств Azure для Eclipse
Средства HDInsight для Eclipse доступны в составе набора средств Azure для Eclipse. Инструкции по установке см. в статье [Установка набора средств Azure для Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Установка подключаемого модуля Scala
Когда вы открываете Eclipse, средство HDInsight автоматически определяет, установлен ли подключаемый модуль Scala. Щелкните **ОК**, чтобы продолжить, и следуйте инструкциям по установке подключаемого модуля из Eclipse Marketplace.

![Автоматическая установка подключаемого модуля Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Пользователь может [войти в подписку Azure](#sign-in-to-your-azure-subscription) или [связать кластер HDInsight](#link-a-cluster) с помощью имени пользователя и пароля Ambari или учетных данных присоединения к домену. 

## <a name="sign-in-to-your-azure-subscription"></a>Войдите в свою подписку Azure.
1. Запустите интегрированную среду разработки Eclipse и откройте Azure Explorer. В меню **Window** (Окно) выберите пункт **Show View** (Показать представление) и щелкните **Other** (Другие). В открывшемся диалоговом окне разверните **Azure** и щелкните **Azure Explorer**, а затем нажмите кнопку **ОК**.

   ![Диалоговое окно Show View (Показать представление)](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. Щелкните правой кнопкой мыши узел **Azure**, а затем выберите **Sign In** (Войти).
1. В диалоговом окне **Azure Sign In** (Вход в Azure) выберите режим аутентификации, нажмите кнопку **Sign in** (Войти) и введите свои учетные данные Azure.
   
   ![Диалоговое окно входа в Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. После входа в диалоговом окне **Select Subscriptions** (Выбор подписок) будут перечислены все подписки Azure, связанные с указанными учетными данными. Щелкните **Select** (Выбрать), чтобы закрыть диалоговое окно.

   ![Диалоговое окно выбора подписок](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. На вкладке **Azure Explorer** разверните **HDInsight**, чтобы просмотреть кластеры HDInsight Spark в своей подписке.
   
   ![Кластеры HDInsight Spark в Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. Далее можно развернуть узел имени кластера, чтобы увидеть ресурсы (например, учетные записи хранения), связанные с ним.
   
   ![Развертывание имени кластера для просмотра ресурсов](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Связывание кластера
Можно связать обычный кластер с помощью управляемого имени пользователя Ambari. Аналогичным образом, присоединенный к домену кластер HDInsight можно связать с помощью домена и имени пользователя, например `user1@contoso.com`.

1. Выберите **Link a cluster** (Связывание кластера) в **обозревателе Azure**.

   ![контекстное меню связывания кластера](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Введите **имя кластера**, **имя пользователя** и **пароль**, затем нажмите кнопку "ОК", чтобы связать кластер. При необходимости введите учетную запись хранения, ключ к хранилищу данных, а затем выберите в представлении в виде дерева контейнер хранилища для обозревателя хранилищ.
   
   ![диалоговое окно связывания кластера](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Если кластер зарегистрирован в подписке Azure и связан, используется ключ к хранилищу данных, имя пользователя и пароль для связывания.
   > ![обозреватель хранилищ в Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Если данные введены правильно, связанный кластер отобразится в узле **HDInsight** после нажатия кнопки "ОК". Теперь в этот связанный кластер можно отправить приложение.

   ![связанный кластер](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. В **обозревателе Azure** также можно удалить связь кластера.
   
   ![кластер с удаленной связью](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Настройка проекта Spark Scala для кластера HDInsight Spark

1. В рабочей области интегрированной среды разработки Eclipse выберите **File** (Файл) выберите **New** (Создать) и щелкните **Project** (Проект). 
1. В мастере "New Project" (Новый проект) разверните **HDInsight**, выберите **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)) и нажмите кнопку **Next** (Далее).

   ![Выбор проекта Spark в HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. Мастер создания проектов Scala автоматически определяет, установлен ли подключаемый модуль Scala. Щелкните **ОК**, чтобы продолжить скачивание подключаемого модуля Scala, а затем следуйте инструкциям, чтобы перезапустить Eclipse.

   ![Проверка Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. В диалоговом окне **New HDInsight Scala Project** (Новый проект Scala HDInsight) введите следующие значения, после чего нажмите кнопку **Next** (Далее).
   * Введите имя проекта.
   * Убедитесь, что в поле **JRE** параметр **Use an execution environment JRE** (Использовать среду выполнения JRE) имеет значение **JavaSE-1.7** или более позднюю версию.
   * В области **библиотеки Spark** вы можете выбрать вариант **Use Maven to configure Spark SDK** (Использовать Maven для настройки пакета SDK для Spark).  Наше средство интегрирует правильную версию пакета SDK для Spark и пакета SDK для Scala. Вы также можете выбрать вариант **Add Spark SDK manually** (Добавить пакет SDK для Spark вручную), скачать этот пакет и добавить его вручную.

   ![Диалоговое окно нового проекта Scala HDInsight](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. В следующем диалоговом окне нажмите кнопку **Finish** (Готово). 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Создание приложения Scala для кластера HDInsight Spark

1. В интегрированной среде разработки Eclipse в обозревателе пакетов разверните созданный ранее проект, щелкните правой кнопкой мыши **src**, выберите **New** (Создать) и щелкните **Other** (Другое).
1. В диалоговом окне **Select a wizard** (Выбор мастера) разверните **Scala Wizards** (Мастера Scala), щелкните **Scala Object** (Объект Scala) и нажмите кнопку **Next** (Далее).
   
   ![Диалоговое окно выбора мастера](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. В диалоговом окне **Create New File** (Создание файла) введите имя объекта и нажмите кнопку **Finish** (Готово).
   
   ![Диалоговое окно создания нового файла](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. Вставьте следующий код в текстовый редактор.
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
1. Запустите приложение в кластере HDInsight Spark.
   
   1\. В обозревателе пакетов щелкните имя проекта правой кнопкой мыши и выберите пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).        
   2\. В диалоговом окне **Spark Submission** (Отправка в Spark) введите следующие значения и нажмите кнопку **Submit** (Отправить).
      
   * В поле **Cluster Name**(Имя кластера) выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.
   * Выберите артефакт из проекта Eclipse или с жесткого диска. Значение по умолчанию зависит от элемента, который вы щелкнете правой кнопкой мыши в обозревателе пакетов.
   * В раскрывающемся списке **Main class name** (Имя класса main) в мастере отправки отображаются имена всех объектов из вашего проекта. Выберите или введите имя любого из объектов, который требуется запустить. Если вы выбрали артефакт с жесткого диска, необходимо ввести имя класса main вручную. 
   * Так как для кода приложения в этом примере не требуются аргументы командной строки, справочные JAR или файлы, остальные текстовые поля можно не заполнять.
        
     ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. На вкладке **Spark Submission** (Отправка в Spark) начнет отображаться ход выполнения. Приложение можно остановить, нажав красную кнопку в окне **Spark Submission** (Отправка в Spark). Можно также просмотреть журналы для данного запуска приложения, щелкнув значок глобуса (обозначен на рисунке синей рамкой).
      
   ![Окно Spark Submission (Отправка в Spark)](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Доступ к кластерам HDInsight Spark и управление ими с помощью средств HDInsight в наборе средств Azure для Eclipse
С помощью средств HDInsight можно выполнять различные операции, включая доступ к выходным данным заданий.

### <a name="access-the-job-view"></a>Доступ к представлению задания
1. В Azure Explorer разверните **HDInsight**, а затем выберите имя кластера Spark, после чего щелкните **Задания**. 

   ![Узел представления задания](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. Перейдите на вкладку **Задания**. Если версия Java ниже, чем **1.8**, средства HDInsight автоматически отправят напоминание об установке подключаемого модуля **E(fx)clipse**. Щелкните **ОК**, чтобы продолжить, и следуйте инструкциям мастера для установки подключаемого модуля из Eclipse Marketplace. По завершении перезапустите Eclipse. 

   ![Установка E(fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Откройте представление задания из узла **Задания**. В области справа на вкладке **Spark Job View** (Просмотр заданий Spark) отображаются все приложения, запускаемые в кластере. Выберите имя приложения, дополнительные сведения о котором вы хотите просмотреть.

   ![Сведения о приложении](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Затем можно использовать любое из следующих действий:

   * Наведите указатель мыши на граф задания. На нем отображаются основные сведения о выполняемом задании. Щелкните граф задания, и вы увидите его этапы и сведения, создаваемые каждым заданием.

     ![Сведения об этапе задания](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Выберите вкладку **Журнал**, чтобы просмотреть часто используемые журналы, включая **Driver Stderr**, **Driver Stdout** и **Directory Info**.

     ![Подробные сведения журнала](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Откройте пользовательский интерфейс журнала Spark и пользовательский интерфейс Apache Hadoop YARN (на уровне приложения), щелкнув ссылки в верхней части окна.

### <a name="access-the-storage-container-for-the-cluster"></a>Доступ к контейнеру хранилища для кластера
1. В Azure Explorer разверните корневой узел **HDInsight**, чтобы увидеть список доступных кластеров HDInsight Spark.
1. Разверните имя кластера, чтобы увидеть учетную запись хранилища и контейнер хранилища по умолчанию для кластера.
   
   ![Учетная запись хранения и контейнер по умолчанию](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. Выберите имя связанного с кластером контейнера хранилища. В области справа дважды щелкните папку **HVACOut**. Откройте один из файлов **part-** для просмотра выходных данных приложения.

### <a name="access-the-spark-history-server"></a>Доступ к серверу журнала Spark
1. В Azure Explorer щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Spark History UI** (Открыть пользовательский интерфейс журнала Spark). При появлении запроса введите учетные данные администратора для кластера. Вы указали их при подготовке кластера.
1. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено, по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("MyClusterApp")`. Следовательно, приложение Spark называлось **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Запуск портала Apache Ambari
1. В Azure Explorer щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Cluster Management Portal (Ambari)** (Открыть портал управления кластерами (Ambari)). 
1. При появлении запроса введите учетные данные администратора для кластера. Вы указали их при подготовке кластера.

### <a name="manage-azure-subscriptions"></a>Управление подписками Azure
По умолчанию средство HDInsight в наборе средств Azure для Eclipse содержит список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, кластеры из которых вас интересуют. 

1. В Azure Explorer щелкните правой кнопкой мыши корневой узел **Azure**, а затем выберите **Управление подписками**. 
1. В диалоговом окне снимите флажки напротив подписок, доступ к которым вам не требуется, и нажмите кнопку **Закрыть**. Если вы хотите выйти из своей подписки Azure, выберите **Выйти**.

## <a name="run-a-spark-scala-application-locally"></a>Запуск приложения Spark Scala на локальном компьютере
Средства HDInsight в наборе средств Azure для Eclipse позволяют запускать приложения Spark Scala локально на рабочей станции. Как правило, такие приложения не требуют доступа к ресурсам кластера, таким как контейнер хранилища, и могут запускаться и тестироваться локально.

### <a name="prerequisite"></a>Предварительное требование
При запуске локального приложения Spark Scala на компьютере с Windows может возникнуть исключение, описанное в [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Это исключение возникает, так как в Windows отсутствует файл **WinUtils.exe**. 

Чтобы устранить эту ошибку, необходимо [скачать исполняемый файл](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), например, в папку **C:\WinUtils\bin**, а затем добавить переменную среды **HADOOP_HOME** и задать **C\WinUtils** в качестве значения переменной.

### <a name="run-a-local-spark-scala-application"></a>Запуск локального приложения Spark Scala
1. Запустите Eclipse и создайте новый проект. В диалоговом окне **New Project** (Новый проект) установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next** (Далее).
   
   * В левой области выберите **HDInsight**.
   * В правой области выберите **Spark on HDInsight Local Run Sample (Scala)** (Пример локального запуска Spark в HDInsight (Scala)).

   ![Диалоговое окно "Новый проект"](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. Чтобы предоставить сведения о проекте, выполните шаги 3–6, как описано в разделе [Настройка проекта Spark Scala для кластера HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Шаблон добавляет пример кода (**LogQuery**) в папку **src**, который можно запустить локально на компьютере.
   
   ![Расположение LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. Щелкните правой кнопкой мыши приложение **LogQuery**, выберите пункт **Run As** (Запустить от имени) и щелкните **1 Scala Application** (1. Приложение Scala). На вкладке **Console** (Консоль) отобразятся выходные данные следующего вида.
   
   ![Результат локального запуска приложения Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Роль только для чтения
Когда пользователи отправляют задания в кластер с разрешением на роль только для чтения, требуются учетные данные Ambari.

### <a name="link-cluster-from-context-menu"></a>Связывание кластера из контекстного меню

1. Войдите с помощью учетной записи роли только для чтения.
       
2. В **Azure Explorer** разверните **HDInsight**, чтобы просмотреть кластеры HDInsight в своей подписке. Кластеры с пометкой **Role:Reader**, имеют только разрешение роли только для чтения.

    ![Кластеры HDInsight Spark в Azure Explorer роль читатель](./media/apache-spark-eclipse-tool-plugin/view-explorer-6.png)

3. Щелкните правой кнопкой мыши кластер с разрешением роль только для чтения. Выберите **Link this cluster** (Связать этот кластер) из контекстного меню, чтобы связать кластер. Введите имя пользователя и пароль Ambari.

    ![Ссылки на кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-7.png)

4. Если кластер связан успешно, HDInsight будет обновлен.
   Этап кластера станет связанным.
  
    ![Связывание кластеров HDInsight Spark в Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-8.png)



### <a name="link-cluster-by-expanding-jobs-node"></a>Связывание кластера путем развертывания узла "Задания"

1. Щелкните узел **Задания** и появится всплывающее окно **Cluster Job Access Denied** (Доступ к заданию кластера запрещен).
   
2. Щелкните **Link this cluster** (Связать этот кластер), чтобы связать кластер.
   
    ![Кластеры HDInsight Spark в Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Связывание кластера с окном отправки Spark

1. Создайте проект HDInsight.

2. Щелкните пакет правой кнопкой мыши. Затем выберите **отправить приложение Spark в HDInsight**.
   
   ![Отправка кластеров HDInsight Spark в Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-11.png)

3. Выберите кластер с разрешением роль только для чтения для **имени кластера**. Появится сообщение с предупреждением. Можно щелкнуть **связать этот кластер** с кластером.
   
   ![Создание кластеров HDInsight Spark в Azure Explorer ссылка](./media/apache-spark-eclipse-tool-plugin/view-explorer-15.png)
   
### <a name="view-storage-accounts"></a>Просмотр учетных записей хранения

* Для кластеров с разрешением роли только для чтения щелкните узел **Учетные записи хранения** и появится всплывающее окно **Storage Access Denied** (Доступ к хранилищу запрещен). 
     
   ![Кластеры HDInsight Spark в хранилище Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-13.png)

   ![Кластеры HDInsight Spark в Azure Explorer запрещены](./media/apache-spark-eclipse-tool-plugin/view-explorer-12.png)

* Для связанных кластеров щелкните узел **Учетные записи хранения** и появится всплывающее окно **Storage Access Denied** (Доступ к хранилищу запрещен). 
     
   ![Кластеры HDInsight Spark в Azure Explorer denied2](./media/apache-spark-eclipse-tool-plugin/view-explorer-14.png)

## <a name="known-problems"></a>Известные проблемы
Рекомендуем при связывании кластера указать учетные данные хранилища.

![Интерактивный вход](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Существует два режима отправки заданий. Если учетные данные хранилища указаны, для отправки задания будет использоваться пакетный режим. В противном случае будет использоваться интерактивный режим. Если кластер занят, может появиться приведенная ниже ошибка.

![Ошибка при получении Eclipse при занятости кластера](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "Ошибка при получении Eclipse при занятости кластера")

![Ошибка получения Eclipse при занятости кластера Yarn](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "Ошибка получения Eclipse при занятости кластера Yarn")

## <a name="seealso"></a>Дополнительные материалы
* [Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Использование Apache Spark MLlib для Создание приложения машинного обучения Apache Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Apache Spark MLlib для создания приложения машинного обучения и анализа набора данных](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Удаленная отладка приложений Apache Spark в HDInsight через SSH с помощью Azure Toolkit for IntelliJ](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование записных книжек Apache Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)

