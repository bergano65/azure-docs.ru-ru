---
title: Установка сторонних приложений в Azure HDInsight
description: Сведения об установке сторонних приложений Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 3e1f64aa00ea0d78eca8a994814a91600b91239f
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190693"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Установка сторонних приложений Apache Hadoop в Azure HDInsight

Узнайте, как установить стороннее приложение [Apache Hadoop](https://hadoop.apache.org/) в Azure HDInsight. Инструкции по установке собственного приложения см. в статье [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md).

Пользователи могут устанавливать приложения HDInsight в кластере HDInsight. Разработчиками этих приложений могут быть корпорация Майкрософт, независимые поставщики программного обеспечения или вы сами.  

Опубликованные приложения представлены в следующем списке:

|Приложение |Типы кластера | Описание |
|---|---|---|
|[AtScale Intelligence Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale включает кластера HDInsight в горизонтально масштабируемый сервер OLAP, позволяя запроса миллиарды строк данных в интерактивном режиме с помощью средств бизнес-Аналитики, вы уже знаете, владельцем и предпочитаете, — из Microsoft Excel, Power BI, Tableau Software для QlikView. |
|[CDAP для HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP является первая унифицированная Интеграционная платформа для больших объемов данных, ускорения значение времени для Hadoop и позволяет ИТ для предоставления данных самообслуживания. Открытым исходным кодом и расширяемой, CDAP удаляет препятствий для инноваций. Требования: 4 региона узлов, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Datameer масштабируемая платформа самообслуживания для подготовки, исследование и данные для аналитики, управляющие ускоряет превращение сложных несколькими источниками данных в ценные сведения корпоративного уровня, предоставления быстрее и эффективнее, insights в масштабе предприятия. |
|[Dataiku DSS в HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS в корпоративной платформы обработки и анализа данных, позволяющий данных по обработке и анализу данных совместно работать для разработки и выполнения новых данных продуктов и служб более эффективно, превратив необработанные данные в эффективные прогнозы. |
|[Приложение WANdisco Fusion HDI App](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Поддержание соответствия в распределенной среде данных является сложной задачей операции данных. Приложение WANdisco Fusion, это платформа корпоративного класса программного обеспечения, решает эту проблему, позволяя согласованности неструктурированных данных в любой среде. |
|[H2O-SparklingWater для HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water поддерживает следующие распределенные алгоритмы: GLM, упрощенный алгоритм Байеса, распределенный случайный лес, машины градиентного Бустинга, глубокие Нейронные сети, глубокое обучение, K-средних, PCA, обобщенные модели Низкорангового, обнаружение аномалий и Автокодировщики. |
|[Striim для интеграции данных в реальном времени для HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Storm, Spark, Kafka |Striim (произносится «поток») является end-to-end потоковую передачу, интеграции данных + платформы аналитики, включение непрерывного приема, обработки и анализа потоков данных. |
|[Ускорение Enterprise Jumbune BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |На высоком уровне Jumbune помогает предприятиям на 1. Ускорение Tez, MapReduce и Spark ядра на основе Hive, Java, Scala производительность рабочей нагрузки. 2. Мониторинг, 3 упреждающего кластера Hadoop. Установка управления качеством данных в распределенной файловой системе. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |На платформе Apache Kylin, Kyligence Enterprise позволяет бизнес-Аналитики на основе больших данных. Как ядро OLAP enterprise на Hadoop Kyligence Enterprise позволяет бизнес-аналитик проектирования бизнес-Аналитики в Hadoop с хранилищем данных стандартные для отрасли и методологии бизнес-Аналитики. |
|[Сервер заданий Spark для KNIME Spark Executor](https://azuremarketplace.microsoft.com/marketplace/apps/knime.spark-job-server-for-knime-spark-executor) |Spark |Сервер заданий Spark для KNIME Spark Executor используется для подключения платформы KNIME Analytics Platform к кластерам HDInsight. |
|[Звезда с Presto для Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto быстрых и масштабируемых распределенных обработчик SQL-запросов. Разработанная для разделения хранилища и вычислений Presto идеально подходит для выполнения запросов к данным в Azure Data Lake Storage, хранилищах BLOB-объектов Azure, базах данных SQL и NoSQL и других источниках данных. |
|[Сборщик данных StreamSets для облака HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |Сборщик данных StreamSets – это упрощенный, мощный механизм, выводит данные в режиме реального времени. Используйте сборщик данных для маршрута и обработка данных в потоки данных. Он поставляется с лицензией на пробную версию в течение 30 дней. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |Enterprise Wrangler Trifacta для HDInsight поддерживает структурирование для любого масштаба данных корпоративного уровня данных. Стоимость выполнения Trifacta в Azure представляет собой сочетание Trifacta затраты на подписку, а также затраты на инфраструктуру Azure для виртуальных машин. |
|[Платформа данных Unifi](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/unifi-software.unifi-data-platform) |Hadoop, HBase, Storm или Spark |Платформа данных Unifi — это набор интегрированных средств самостоятельного данных расширить возможности бизнес-пользователь к решению данных проблем, добавочное доходы, снижать затраты или сложность эксплуатации. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Обнаружить приложение данных для кластера HDInsight Spark. |
|[Каталог данных, управляемых искусственным Интеллектом waterline](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline каталоги, упорядочивает и управляет данных с помощью искусственного Интеллекта для данных автоматически тег с бизнес-терминологией. Каталог грамотного бизнеса Waterline —это чрезвычайно важный компонент успешного проведения самостоятельной аналитики, обеспечения соответствия требованиям и системы управления, а также инициатив в области управления ИТ. |

В этой статье используется портал Azure. Вы можете экспортировать шаблон Azure Resource Manager с портала или получить его копию у поставщиков, а затем развернуть его с помощью Azure PowerShell и классического интерфейса командной строки Azure.  Ознакомьтесь со статьей [Создание кластеров Apache Hadoop в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Технические условия
Если вы хотите установить приложение HDInsight в существующем кластере HDInsight, вы должны создать кластер HDInsight. Инструкции по созданию кластера см. в [этом разделе](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Вы также можете установить приложения HDInsight во время создания кластера HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Установка приложений в имеющиеся кластеры
Здесь приведена процедура установки приложения HDInsight в имеющийся кластер HDInsight.

**Установка приложения HDInsight**

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню слева, перейдите к **все службы** > **Analytics** > **кластеры HDInsight**.
3. Выберите кластер HDInsight в списке.  Если у вас нет кластера, сначала его необходимо создать.  См. [этот раздел](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. В разделе **параметры** категорию **приложений**. Отобразится список установленных приложений, в главном окне. 
   
    ![Меню портала для приложений HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Выберите **+ добавить** в меню. Отобразится список доступных приложений.  Если **+ добавить** неактивна, что означает, что являются приложения для этой версии кластера HDInsight отсутствуют.
   
    ![Доступные приложения HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Выберите один из доступных приложений и следуйте инструкциям, чтобы принять условия.

Вы увидите состояние установки в уведомлениях портала (выберите значок колокольчика в верхней части портала). После установки приложение отобразится в списке "Установленные приложения".

## <a name="install-applications-during-cluster-creation"></a>Установка приложения во время создания кластера
Вы также можете установить приложения HDInsight во время создания кластера. Их установка осуществляется после создания и запуска кластера. Чтобы установить приложения во время создания кластера с помощью портала Azure, используйте **Custom** параметр вместо значения по умолчанию **быстрое создание** параметр.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Отображение списка установленных приложений HDInsight и их свойств
На портале содержится список установленных приложений HDInsight для кластера и их свойств.

**Список приложений HDInsight и их свойств**

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню слева, перейдите к **все службы** > **Analytics** > **кластеры HDInsight**.
3. Выберите кластер HDInsight в списке.
4. В разделе **параметры** категорию **приложений**. Отобразится список установленных приложений, в главном окне. 
   
    ![Установленные приложения HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Выберите один из установленных приложений для отображения свойства. В свойствах указано следующее:

    |Свойство | Описание |
    |---|---|
    |Имя приложения. |Имя приложения. |
    |Status |Состояние приложения. |
    |Веб-страница |URL-адрес веб-приложения, развернутого на граничном узле. В качестве учетных данных используются учетные данные пользователя HTTP, настроенные для кластера. |
    |Конечная точка SSH |К граничному узлу можно подключиться с помощью SSH. В качестве учетных данных SSH используются учетные данные пользователя SSH, настроенные для кластера. См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Описание | Описание приложения. |

6. Чтобы удалить приложение, щелкните его правой кнопкой мыши и в контекстном меню выберите **Удалить**.

## <a name="connect-to-the-edge-node"></a>Подключение к граничному узлу
Для подключения к граничному узлу можно использовать протокол HTTP и SSH. Дополнительные сведения о конечной точке см. на [портале](#list-installed-hdinsight-apps-and-properties). См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

В качестве учетных данных конечной точки HTTP используются учетные данные пользователя HTTP, настроенные для кластера HDInsight. Что же касается конечной точки SSH, используются учетные данные пользователя SSH, настроенные для кластера HDInsight.

## <a name="troubleshoot"></a>Устранение неполадок
См. раздел [Устранение неполадок, связанных с установкой](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Дальнейшие действия
* [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md)— узнайте, как развернуть в HDInsight неопубликованное приложение HDInsight.
* [Публикация приложения HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md). Узнайте, как опубликовать пользовательские приложения HDInsight в Microsoft Azure Marketplace.
* [MSDN. Application](https://msdn.microsoft.com/library/mt706515.aspx) (Приложение). Узнайте, как определить приложения HDInsight.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md)— узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Создание кластеров Apache Hadoop под управлением Linux в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) — узнайте, как вызывать шаблоны Resource Manager для создания кластеров HDInsight.
* [Использование пустых граничных узлов в HDInsight](hdinsight-apps-use-edge-node.md)— узнайте, как использовать пустой граничный узел для доступа к кластеру HDInsight, а также тестирования и размещения приложений HDInsight.

