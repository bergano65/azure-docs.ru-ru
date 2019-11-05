---
title: Отладка задания Spark с помощью набора средств Azure IntelliJ (Предварительная версия) — HDInsight
description: Руководство по использованию средств HDInsight в Azure Toolkit for IntelliJ для отладки приложений
keywords: удаленная отладка intellij, ssh, intellij, hdinsight, отладка intellij, отладка
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494602"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Сбой отладки задания Spark с Azure Toolkit for IntelliJ (Предварительная версия)

В этой статье содержатся пошаговые инструкции по использованию средств HDInsight в [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) для запуска приложений **отладки Spark с ошибкой** .

## <a name="prerequisites"></a>Технические условия

* [Комплект разработчика Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). В этом руководстве используется Java версии 8.0.202.
  
* IntelliJ IDEA. В этой статье используется [INTELLIJ идея Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Подключитесь к кластеру HDInsight. См. раздел [Подключение к кластеру HDInsight](apache-spark-intellij-tool-plugin.md).

* Обозреватель службы хранилища Microsoft Azure. См. раздел [Download обозреватель службы хранилища Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Создание проекта с шаблоном отладки

Создайте проект Spark 2.3.2, чтобы продолжить отладку сбоя, выполните в этом документе пример файла отладки задачи, который не удалось выполнить.

1. Откройте IntelliJ IDEA. Откройте окно **Новый проект** .

   а. На левой панели щелкните **Azure Spark/HDInsight**.

   b. В главном окне выберите **проект Spark с неудачной отладкой задачи (Предварительная версия) (Scala)** .

     ![IntelliJ создание проекта отладки](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Щелкните **Далее**.

2. В окне **Новый проект** выполните следующие действия.

   ![IntelliJ новый проект выберите версию Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   а. Введите имя и расположение проекта.

   b. В раскрывающемся списке **пакет SDK для проекта** выберите **Java 1,8** для кластера **Spark 2.3.2** .

   c. В раскрывающемся списке **версия Spark** выберите **Spark 2.3.2 (Scala 2.11.8)** .

   г) Выберите **Готово**.

3. Выберите **src** > **main** > **scala**, чтобы открыть код в проекте. В этом примере используется скрипт **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Запуск приложения Spark Scala/Java в кластере HDInsight

Создайте приложение Spark Scala/Java, а затем запустите приложение в кластере Spark, выполнив следующие действия.

1. Щелкните **Добавить конфигурацию** , чтобы открыть окно **конфигурации запуска/отладки** .

   ![HDI IntelliJ добавить конфигурацию](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. В диалоговом окне **Run/Debug Configurations** (Конфигурации выполнения и отладки) щелкните знак "плюс" ( **+** ). Затем выберите параметр **Apache Spark в HDInsight** .

   ![IntelliJ добавить новую конфигурацию](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Перейдите в режим **удаленного запуска на вкладке кластер** . Введите данные для **имени**, **кластера Spark**и **имени класса Main**. Наши средства поддерживают отладку с **исполнителями**. **Нумексекторс**, значение по умолчанию — 5, и лучше не задавать более 3. Чтобы сократить время выполнения, можно добавить **Spark. Yarn. максаппаттемптс** в **конфигурации задания** и установить значение 1. Нажмите кнопку " **ОК** ", чтобы сохранить конфигурацию.

   ![IntelliJ запускать конфигурации отладки New](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Теперь конфигурация сохранена под именем, которое вы указали. Чтобы просмотреть сведения о конфигурации, выберите имя конфигурации. Чтобы внести изменения, выберите **Edit Configurations** (Изменить конфигурации).

5. После завершения настройки конфигурации можно запустить проект в удаленном кластере.

   ![Кнопка удаленного запуска IntelliJ отладки удаленного задания Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Идентификатор приложения можно проверить в окне Вывод.

   ![Результат удаленного запуска IntelliJ отладки удаленного задания Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Скачивание профиля невыполненного задания

В случае сбоя отправки задания можно скачать профиль невыполненного задания на локальный компьютер для дальнейшей отладки.

1. Откройте **Обозреватель службы хранилища Microsoft Azure**, выберите учетную запись HDInsight кластера для невыполненного задания, скачайте невыполненные ресурсы заданий из соответствующего расположения: **\hdp\spark2-Events\\. spark — сбои\\\<Идентификатор приложения >** в локальную папку. В окне **действия** отобразится ход загрузки.

   ![Сбой загрузки Обозреватель службы хранилища Azure](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Загрузка Обозреватель службы хранилища Azure успешно завершена](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Настройка локальной среды отладки и отладка при сбое

1. Откройте исходный проект или создайте новый проект и свяжите его с исходным исходным кодом. В настоящее время отладка сбоев поддерживается только для версии Spark 2.3.2.

1. В IntelliJ идея создайте файл конфигурации **отладки Spark** с ошибкой, выберите файл ФТД из ранее скачанных ресурсов невыполненных заданий для поля **расположение контекста сбоя задания Spark** .

   ![Конфигурация сбоя создавайте](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Нажмите кнопку "локальный запуск" на панели инструментов. ошибка отобразится в окне "выполнение".

   ![Run-Failure-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Run-Failure-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Установите точку останова, как указано в журнале, затем нажмите кнопку локальная отладка, чтобы выполнить локальную отладку так же, как обычные проекты Scala/Java в IntelliJ.

1. После отладки, если проект успешно завершает работу, можно повторно отправить невыполненное задание в Spark в кластере HDInsight.

## <a name="seealso"></a>Дальнейшие действия

* [Обзор: Отладка Apache Spark приложений](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Демонстрация

* Создание проекта Scala (видео): [создание приложений Scala Apache Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Удаленная отладка приложений Apache Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ)

### <a name="scenarios"></a>Сценарии

* [Apache Spark с бизнес-аналитикой: Интерактивный анализ данных с помощью Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Apache Spark в HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения

* [Создание приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в Azure Toolkit for Eclipse для создания приложений Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Apache Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
