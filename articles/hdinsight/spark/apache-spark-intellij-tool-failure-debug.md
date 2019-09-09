---
title: 'Сбой отладки задания Spark с Azure Toolkit for IntelliJ (Предварительная версия) '
description: Руководство по использованию средств HDInsight в Azure Toolkit for IntelliJ для отладки приложений
keywords: удаленная отладка intellij, ssh, intellij, hdinsight, отладка intellij, отладка
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: a07dcd58263674aa6fd360e138c0b9c999ea644e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814146"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Сбой отладки задания Spark с Azure Toolkit for IntelliJ (Предварительная версия)

В этой статье содержатся пошаговые инструкции по использованию средств HDInsight в [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) для запуска приложений **отладки Spark с ошибкой** . 

## <a name="prerequisites"></a>Предварительные требования
* [Комплект разработчика Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). В этом руководстве используется Java версии 8.0.202.
  
* IntelliJ IDEA. В этой статье используется среда [IntelliJ IDEA версии 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Подключитесь к кластеру HDInsight. См. раздел [Подключение к кластеру HDInsight](apache-spark-intellij-tool-plugin.md).

* Обозреватель службы хранилища Microsoft Azure. См. раздел [Download обозреватель службы хранилища Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Создание проекта с шаблоном отладки 

Создайте проект Spark 2.3.2, чтобы продолжить отладку сбоя, выполните в этом документе пример файла отладки задачи, который не удалось выполнить.

1. Откройте IntelliJ IDEA. Откройте окно **Новый проект** .

   1\. На левой панели щелкните **Azure Spark/HDInsight**. 

   2\. В главном окне выберите **проект Spark с неудачной отладкой задачи (Предварительная версия) (Scala)** .

     ![Создание проекта отладки](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   В. Щелкните **Далее**.     
 
2. В окне **Новый проект** выполните следующие действия.

   ![Выбор пакета SDK для Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   1\. Введите имя и расположение проекта.

   2\. В раскрывающемся списке **пакет SDK для проекта** выберите **Java 1,8** для кластера **Spark 2.3.2** .

   В. В раскрывающемся списке **версия Spark** выберите **Spark 2.3.2 (Scala 2.11.8)** .

   Г. Выберите **Готово**.

3. Выберите **src** > **main** > **scala**, чтобы открыть код в проекте. В этом примере используется скрипт **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Запуск приложения Spark Scala/Java в кластере HDInsight

Создайте приложение Spark Scala/Java, а затем запустите приложение в кластере Spark, выполнив следующие действия.

1. Щелкните **Добавить конфигурацию** , чтобы открыть окно **конфигурации запуска/отладки** .

   ![Изменить конфигурации](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png) 

2. В диалоговом окне **Run/Debug Configurations** (Конфигурации выполнения и отладки) щелкните знак "плюс" ( **+** ). Затем выберите параметр **Apache Spark в HDInsight** .

   ![Добавление новой конфигурации](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Откройте вкладку **Remotely Run in Cluster** (Удаленный запуск в кластере). Введите сведения в полях **Name** (Имя), **Spark cluster** (Кластер Spark) и **Main class name** (Имя основного класса). Наши средства поддерживают отладку с **исполнителями**. **Нумексекторс**, значение по умолчанию — 5, и лучше не задавать более 3. Чтобы сократить время выполнения, можно добавить **Spark. Yarn. максаппаттемптс** в **конфигурации задания** и установить значение 1. Нажмите кнопку " **ОК** ", чтобы сохранить конфигурацию.

   ![Запуск конфигураций отладки](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Теперь конфигурация сохранена под именем, которое вы указали. Чтобы просмотреть сведения о конфигурации, выберите имя конфигурации. Чтобы внести изменения, выберите **Edit Configurations** (Изменить конфигурации). 

5. После завершения настройки конфигурации можно запустить проект в удаленном кластере.
   
   ![Кнопка удаленного запуска](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Идентификатор приложения можно проверить в окне Вывод.
   
   ![Результат удаленного запуска](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)   

## <a name="download-failed-job-profile"></a>Скачивание профиля невыполненного задания

В случае сбоя отправки задания можно скачать профиль невыполненного задания на локальный компьютер для дальнейшей отладки.

1. Откройте **Обозреватель службы хранилища Microsoft Azure**, выберите учетную запись HDInsight кластера для невыполненного задания, скачайте невыполненные ресурсы заданий из соответствующего расположения: **\hdp\spark2-Events\\. Spark — сбои\\ Идентификатор\<приложения >** в локальную папку. В окне **действия** отобразится ход загрузки.

   ![Сбой загрузки файла file1](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![file2 сбоя загрузки](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)   

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Настройка локальной среды отладки и отладка при сбое

1. Откройте исходный проект или создайте новый проект и свяжите его с исходным исходным кодом. В настоящее время отладка сбоев поддерживается только для версии Spark 2.3.2.

2. В IntelliJ идея создайте файл конфигурации **отладки Spark** с ошибкой, выберите файл ФТД из ранее скачанных ресурсов невыполненных заданий для поля **расположение контекста сбоя задания Spark** .
   
   ![Конфигурация сбоя создавайте](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

4. Нажмите кнопку "локальный запуск" на панели инструментов. ошибка отобразится в окне "выполнение".
   
   ![Run-Failure-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Run-Failure-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

5. Установите точку останова, как указано в журнале, затем нажмите кнопку локальная отладка, чтобы выполнить локальную отладку так же, как обычные проекты Scala/Java в IntelliJ.

5. После отладки, если проект успешно завершает работу, можно повторно отправить невыполненное задание в Spark в кластере HDInsight.

## <a name="seealso"></a>Дальнейшие действия
* [Apache Отладка Apache Spark приложений](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [Создание приложений Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [Удаленная отладка приложений Apache Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Руководство. Интерактивный анализ данных с помощью Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Apache Spark MLlib для Создание приложения машинного обучения Apache Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Apache Spark MLlib для создания приложения машинного обучения и анализа набора данных](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Создание приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в Azure Toolkit for Eclipse для создания приложений Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Apache Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Apache Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
