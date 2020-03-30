---
title: Работа Debug Spark с Помощью Инструментов IntelliJ Azure (предварительный просмотр) - HDInsight
description: Руководство с использованием инструментов HDInsight в Azure Toolkit для IntelliJ для отладки приложений
keywords: удаленная отладка intellij, ssh, intellij, hdinsight, отладка intellij, отладка
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494602"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Неудача искры отладки работы с Azure Toolkit для IntelliJ (предварительный просмотр)

В этой статье содержатся пошаговые рекомендации по использованию инструментов HDInsight в [Azure Toolkit для IntelliJ для](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) запуска приложений **Spark Failure Debug.**

## <a name="prerequisites"></a>Предварительные требования

* [Комплект Oracle Java Development](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). В этом руководстве используется Java версии 8.0.202.
  
* Среда IntelliJ IDEA. Эта статья использует [IntelliJ IDEA сообщества ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Подключитесь к кластеру HDInsight. Смотрите [Подключите к кластеру HDInsight.](apache-spark-intellij-tool-plugin.md)

* Исследователь хранения данных Microsoft Azure. Смотрите [Скачать Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Создание проекта с шаблоном отладки

Создайте проект spark2.3.2 для продолжения отладки сбоев, примите отладку образца в этом документе задачи отказа.

1. Откройте IntelliJ IDEA. Откройте окно **нового проекта.**

   а. На левой панели щелкните **Azure Spark/HDInsight**.

   b. Выберите **проект Spark с пробой отказа Отображения (Предварительный просмотр) (Scala)** из основного окна.

     ![Intellij Создать отладку проекта](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Нажмите кнопку **Далее**.

2. В окне **New Project** (Новый проект) выполните указанные ниже действия.

   ![Intellij Новый проект выбрать версию Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   а. Введите имя и расположение проекта.

   b. В списке выпадающих проектов **SDK** выберите **Java 1.8** для кластера **Spark 2.3.2.**

   c. В списке **Spark Version** выпадвниз, выберите **Spark 2.3.2 (Scala 2.11.8)**.

   d. Нажмите кнопку **Готово**.

3. Выберите **src** > **main** > **scala,** чтобы открыть код в проекте. В этом примере используется **AgeMean_Div ()** скрипт.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Запуск приложения Spark Scala/Java в кластере HDInsight

Создайте искру приложения Scala/Java, а затем запустите приложение в кластере Spark, сделав следующие действия:

1. Нажмите **Добавить Конфигурация,** чтобы открыть **окно конфигураций Run/Debug.**

   ![Конфигурация HDI Intellij Add](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. В диалоговом окне **Run/Debug Configurations** (Конфигурации выполнения и отладки) щелкните знак "плюс" (**+**). Затем выберите **Apache Spark на hdInsight** вариант.

   ![Intellij Добавить новую конфигурацию](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Переключиться на **удаленное запуск в** вкладке кластера. Введите информацию для **имени,** **кластера Spark**и **имени основного класса.** Наши средства поддерживают отладку с **исполнителями**. **NumExectors**, значение по умолчанию составляет 5, и вам лучше не устанавливать выше, чем 3. Чтобы сократить время выполнения, вы можете добавить **spark.yarn.maxAppAttempts** в **конфигурации рабочих мест** и установить значение до 1. Нажмите **кнопку OK,** чтобы сохранить конфигурацию.

   ![Настройки отладки Intellij Run новые](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Теперь конфигурация сохранена под именем, которое вы указали. Чтобы просмотреть сведения о конфигурации, выберите имя конфигурации. Чтобы внести изменения, выберите **Edit Configurations** (Изменить конфигурации).

5. После завершения настроек конфигураций можно запустить проект против удаленного кластера.

   ![Кнопка удаленного запуска Intellij Debug](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Идентификатор приложения можно проверить из окна вывода.

   ![Результат удаленного запуска Intellij Debug Remote Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Скачать неудавшийся профиль работы

Если отправка задания не удается, можно загрузить неудавшийся профиль задания в локальную машину для дальнейшей отладки.

1. Откройте **Microsoft Azure Storage Explorer,** найдите учетную запись кластера HDInsight для неудавшейся работы, загрузите невыполненные задания из соответствующего местоположения: **идентификатор приложения\\.spark-failures.spark-failed\\\<>** в локальную папку. Окно **действий** покажет прогресс загрузки.

   ![Сбой загрузки Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Успех загрузки Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Настройка локальной среды отладки и отладка при сбое

1. Откройте исходный проект или создайте новый проект и свяжете его с исходным кодом. Только версия spark2.3.2 поддерживается для отладки сбоев в настоящее время.

1. В IntelliJ IDEA создайте файл конфигурации **Spark Failure Debug,** выберите файл FTD из ранее загруженных невыполненных ресурсов для поля **расположения контекста сбоя в работе Spark.**

   ![конфигурация сбоя crete](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Нажмите кнопку локального запуска в панели инструментов, ошибка будет отображаться в окне Run.

   ![конфигурация запуска-сбой1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![конфигурация запуска-сбой2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Установите точку разрыва, как показывает журнал, а затем нажмите кнопку локальной отладки, чтобы сделать локальную отладку так же, как обычные проекты Scala / Java в IntelliJ.

1. После отладки, если проект успешно завершен, можно повторно отправить сбой задания в искру в кластере HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Дальнейшие действия

* [Обзор: Приложения Debug Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Демонстрация

* Создание проекта Scala (видео): [создание приложений Scala Apache Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Удаленная отладка приложений Apache Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ)

### <a name="scenarios"></a>Сценарии

* [Apache Spark с BI: Делайте интерактивный анализ данных с помощью Spark в HDInsight с помощью инструментов BI](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

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
