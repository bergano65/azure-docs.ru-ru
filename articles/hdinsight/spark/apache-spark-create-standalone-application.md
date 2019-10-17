---
title: Руководство по Приложение Scala с использованием Maven для Spark с IntelliJ в Azure HDInsight
description: 'Учебник: создание приложения Spark, написанного на Scala, с использованием Apache Maven в качестве системы сборки и имеющегося архетипа Maven для Scala, который предоставляется IntelliJ IDEA.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.openlocfilehash: 667590120bba4676cbedf3d3bb2ff08e31656e1d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "71147020"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Руководство по Создание приложения Scala Maven для Apache Spark в HDInsight с помощью IntelliJ

Из этого руководства вы узнаете, как создать приложение [Apache Spark](https://spark.apache.org/) на языке [Scala](https://www.scala-lang.org/) в [Apache Maven](https://maven.apache.org/) с помощью IntelliJ IDEA. В качестве системы сборки в этой статье используется Apache Maven и изначально применяется существующий архетип Maven для Scala, который обеспечивает IntelliJ IDEA.  Создание приложения Scala в IntelliJ IDEA включает в себя следующие этапы:

* использование Maven в качестве системы сборки;
* обновление файла объектной модели проектов для разрешения зависимостей модуля Spark;
* написание приложения на языке Scala;
* создание JAR-файла, который можно отправить в кластеры HDInsight Spark;
* запуск приложений с помощью Livy в кластере Spark.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Установка подключаемого модуля Scala для IntelliJ IDEA
> * Разработка приложения Scala Maven с помощью IntelliJ.
> * Создание автономного проекта Scala

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

* [Комплект разработчика Oracle Java](https://www.azul.com/downloads/azure-only/zulu/).  В этом руководстве используется Java версии 8.0.202.

* Java IDE. В этой статье используется среда [IntelliJ IDEA версии  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Установка подключаемого модуля Scala для IntelliJ IDEA

Чтобы установить подключаемый модуль Scala, сделайте следующее:

1. Откройте IntelliJ IDEA.

2. На экране приветствия выберите **Configure** (Настройка) > **Plugins** (Подключаемые модули), чтобы открыть окно **подключаемых модулей**.

    ![Включение подключаемого модуля Scala в IntelliJ IDEA](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Выберите **Install** (Установить) в области подключаемого модуля Scala в новом окне.  

    ![Установка подключаемого модуля Scala в IntelliJ IDEA](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. После успешной установки подключаемого модуля необходимо перезапустить интегрированную среду разработки.

## <a name="use-intellij-to-create-application"></a>Создание приложения с помощью IntelliJ

1. Запустите IntelliJ IDEA и выберите **Create New Project** (Создать проект), чтобы открыть окно **New Project** (Новый проект).

2. На левой панели щелкните **Azure Spark/HDInsight**.

3. В главном окне выберите **Spark Project (Scala)** (Проект Spark (Scala)).

4. Из раскрывающегося списка **Build tool** (Инструмент сборки) выберите один из следующих вариантов:
      * **Maven** для поддержки мастера создания проекта Scala.
      * **SBT** для управления зависимостями и создания проекта Scala.

   ![Диалоговое окно нового проекта в IntelliJ](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. Щелкните **Далее**.

6. В окне **New Project** (Новый проект) укажите следующую информацию:  

  	|  Свойство   | ОПИСАНИЕ   |  
  	| ----- | ----- |  
  	|Имя проекта| Введите имя.|  
  	|Project&nbsp;location (Расположение проекта)| Введите необходимое расположение для сохранения проекта.|
  	|Project SDK (Пакет SDK проекта)| Это поле будет пустым при первом использовании IDEA.  Выберите **New...** (Создать...) и перейдите к JDK.|
  	|Версия Spark|Мастер создания интегрирует правильную версию пакетов SDK для Spark и Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x**. В этом примере используется **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Выбор пакета SDK Spark в IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Выберите **Готово**.

## <a name="create-a-standalone-scala-project"></a>Создание автономного проекта Scala

1. Запустите IntelliJ IDEA и выберите **Create New Project** (Создать проект), чтобы открыть окно **New Project** (Новый проект).

2. Выберите **Maven** в левой области.

3. Выберите пакет в поле **Project SDK** (Пакет SDK проекта). Если это поле пусто, щелкните **New...** (Создать...) и перейдите к каталогу установки Java.

4. Установите флажок **Create from archetype** (Создать на основе архетипа).  

5. В списке архетипов выберите **org.scala-tools.archetypes:scala-archetype-simple**. Этот архетип создает структуру каталога и скачивает зависимости по умолчанию, необходимые для написания программы Scala.

    ![Создание проекта Maven в IntelliJ IDEA](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. Щелкните **Далее**.

7. Введите соответствующие значения для параметров **GroupId**, **ArtifactId** и **Version**. В этом руководстве используются представленные ниже значения.

    - **GroupId:** com.microsoft.spark.example;
    - **ArtifactId:** SparkSimpleApp.

8. Щелкните **Далее**.

9. Проверьте параметры и нажмите кнопку **Next** (Далее).

10. Проверьте имя и расположение проекта, а затем выберите **Finish** (Готово).  Импорт этого проекта займет несколько минут.

11. После импорта проекта в области слева выберите **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **microsoft** > **spark** > **example**.  Щелкните **MySpec** правой кнопкой мыши и выберите **Delete...** (Удалить...). Этот файл не нужно использовать для приложения.  Нажмите кнопку **ОК** в диалоговом окне.
  
12. На следующих шагах описывается обновление файла **pom.xml** для определения зависимостей приложения Spark Scala. Чтобы автоматически скачать эти зависимости и разрешить их, необходимо соответствующим образом настроить Maven.

13. В меню **File** (Файл) выберите **Settings** (Параметры), чтобы открыть окно **параметров**.

14. В окне **Settings** (Параметры) выберите **Build, Execution, Deployment** (Сборка, выполнение, развертывание) > **Build Tools** (Средства сборки) > **Maven** > **Importing** (Импорт).

15. Установите флажок **Import Maven projects automatically**(Импортировать проекты Maven автоматически).

16. Нажмите кнопку **Apply** (Применить), а затем нажмите кнопку **ОК**.  Затем вы вернетесь обратно в окно проекта.

    ![Настройка автоматической загрузки Maven](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. В области слева выберите **src** > **main** > **scala** > **com.microsoft.spark.example**, а затем двойным щелчком выберите **App** (Приложение), чтобы открыть App.scala.

18. Замените имеющийся пример кода кодом ниже, а затем сохраните изменения. Этот код считывает данные из файла HVAC.csv (доступного во всех кластерах HDInsight Spark), извлекает строки, которые содержат только одну цифру в шестом столбце, и записывает выходные данные в папку **/HVACOut** , которая находится в используемом по умолчанию контейнере хранилища кластера.

        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
19. В левой области дважды щелкните **pom.xml**.  

20. Добавьте следующие сегменты в файл `<project>\<properties>`:

          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. Добавьте следующие сегменты в файл `<project>\<dependencies>`:

           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>

    Сохраните изменения в файле pom.xml.

22. Создайте JAR-файл. IntelliJ IDEA позволяет создавать JAR-файлы в качестве артефактов проекта. Выполните следующие действия.

    1. В меню **File** (Файл) выберите **Project Structure...** (Структура проекта...).

    2. В окне **Project Structure** (Структура проекта) выберите **Artifacts** (Артефакты) > **символ "плюс" +**  > **JAR** > **From modules with dependencies...** (Из модулей с зависимостями...).

        ![Добавление JAR-файла со структурой проекта в IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. В окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) выберите значок папки в текстовом поле **Main Class** (Основной класс).

    4. В окне **Select Main Class** (Выбор основного класса) выберите класс, который отображается по умолчанию, и щелкните **ОК**.

        ![Выбор класса со структурой проекта в IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. В окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) выберите вариант **extract to the target JAR** (Извлечь в целевой JAR-файл) и щелкните **ОК**.  В результате будет создан один JAR-файл, содержащий все зависимости.

        ![Добавление JAR-файла со структурой проекта из модуля в IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. На вкладке **Макет выходных данных** содержится список всех JAR-файлов, которые включены в проект Maven. Здесь можно выбрать и удалить файлы, от которых не зависит работа приложения Scala. Для создаваемого приложения можно удалить все файлы, кроме последнего (**SparkSimpleApp compile output** (Выходные данные компиляции SparkSimpleApp)). Выберите JAR-файлы, которые нужно удалить, и щелкните значок минус **-** .

        ![Удаление выходных данных со структурой проекта в IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Убедитесь, что установлен флажок **Include in project build** (Включить в сборку проекта), чтобы JAR-файл создавался при каждом создании и обновлении проекта. Нажмите кнопку **Apply** (Применить), а затем — **ОК**.

    7. Чтобы создать JAR-файл, выберите **Build** (Сборка) > **Build Artifacts** (Артефакты сборки)  > **Build** (Сборка). Компиляция проекта займет около 30 секунд.  Выходной JAR-файл будет создан в разделе **\out\artifacts**.

        ![Выходные данные артефакта проекта в IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Запуск приложения в кластере Apache Spark

Чтобы запустить приложение в кластере, можно использовать следующие методы:

* **Скопируйте приложение JAR в большой двоичный объект службы хранилища Azure**, связанный с кластером. Вы можете использовать для этого служебную программу командной строки [**AzCopy**](../../storage/common/storage-use-azcopy.md). Кроме того, для отправки данных можно использовать множество других клиентов. Дополнительные сведения о них см. в статье [Отправка данных для заданий Apache Hadoop в HDInsight](../hdinsight-upload-data.md).

* **Используйте Apache Livy для удаленной отправки задания приложения** в кластер Spark. В кластерах HDInsight Spark есть сервер Livy, который использует конечные точки REST для удаленной отправки заданий Spark. Дополнительные сведения см. в статье об [удаленной отправке заданий Apache Spark с помощью Apache Livy и кластеров Spark в HDInsight ](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите созданный кластер, сделав следующее:

1. Войдите на [портале Azure](https://portal.azure.com/).

1. В поле **Поиск** в верхней части страницы введите **HDInsight**.

1. Выберите **Кластеры HDInsight** в разделе **Службы**.

1. В списке кластеров HDInsight, который отобразится, выберите **...** рядом с кластером, созданным при работе с этим учебником.

1. Нажмите кнопку **Удалить**. Выберите **Да**.

![Удаление кластера HDInsight на портале Azure](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Удаление кластера HDInsight")

## <a name="next-step"></a>Дальнейшие действия

Из этой статьи вы узнали, как создать приложение Apache Spark на языке Scala. Из следующей статьи вы узнаете, как запустить это приложение на кластере HDInsight Spark, используя Livy.

> [!div class="nextstepaction"]
>[Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](./apache-spark-livy-rest-interface.md)
