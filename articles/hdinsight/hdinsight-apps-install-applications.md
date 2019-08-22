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
ms.openlocfilehash: 9b96294362400891113812e308ff59bd0e438b90
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873366"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Установка сторонних приложений Apache Hadoop в Azure HDInsight

Узнайте, как установить стороннее приложение [Apache Hadoop](https://hadoop.apache.org/) в Azure HDInsight. Инструкции по установке собственного приложения см. в статье [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md).

Пользователи могут устанавливать приложения HDInsight в кластере HDInsight. Разработчиками этих приложений могут быть корпорация Майкрософт, независимые поставщики программного обеспечения или вы сами.  

Опубликованные приложения представлены в следующем списке:

|Приложение |Типы кластеров | Описание |
|---|---|---|
|[Платформа AtScale Intelligence](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale превращает ваш кластер HDInsight в масштабируемый OLAP-сервер, позволяя запрашивать миллиарды строк данных в интерактивном режиме с помощью средств бизнес-аналитики, которые уже знакомы, владеют и любовь — от Microsoft Excel, Power BI, Tableau Software to QlikView. |
|[CDAP для HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |Hbase |CDAP — это первая унифицированная платформа интеграции для больших данных, которая ускоряет время до значения Hadoop и позволяет ему предоставлять данные самообслуживания. С открытым кодом и расширяемым CDAP устраняет барьеры для инноваций. Требования: 4 узла регионов, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Datameerная платформа самообслуживания для подготовки, исследования и регулирования данных для аналитики ускоряет процесс преобразования сложных данных с несколькими источниками в ценные сведения, готовые для бизнеса, обеспечивая более быструю и эффективную аналитику в масштабах предприятия. |
|[Dataiku DSS в HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS в корпоративной платформе для обработки и анализа данных, которая позволяет специалистам по обработке и анализу данных совместно создавать и запускать новые продукты и службы данных, преобразуя необработанные данные в затронутые прогнозы. |
|[Приложение HDI Fusion Вандиско](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Kafka |Обеспечение единообразия данных в распределенной среде — задача массовых операций с данными. Вандиско Fusion, платформа программного обеспечения корпоративного класса, решает эту проблему, обеспечивая согласованность неструктурированных данных в любой среде. |
|[H2O Спарклингватер для HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |Вода H2O Sparkling поддерживает следующие распределенные алгоритмы: GLM, упрощенный алгоритм Байеса, распределенный случайный лес, высокопроизводительный компьютер с градиентом, глубокие нейронные сети, глубокое обучение, K-средние, PCA, обобщенные модели с низким рангом, обнаружение аномалий, автокодировщики. |
|[Стриим для интеграции данных в режиме реального времени с HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, г., Spark, Kafka |Стриим (произносится как "поток") — это комплексная платформа интеграции данных потоковой передачи, обеспечивающая непрерывное получение, обработку и анализ разнородных потоков данных. |
|[Жумбуне Enterprise-Accelerator Бигдата Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |На высоком уровне Жумбуне помогает предприятиям, 1. Ускорение Tez, MapReduce & Hive на основе ядра Spark, Java, Scala производительность рабочей нагрузки. 2. Упреждающий мониторинг кластера Hadoop, 3. Установка управления качествами данных в распределенной файловой системе. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |На платформе Apache Kylin Kyligence Enterprise позволяет бизнес-аналитику создавать большие данные. Как модуль OLAP Enterprise в Hadoop, Kyligence Enterprise предоставляет бизнес-аналитику архитектор BI в Hadoop с использованием стандартного хранилища данных и методологии бизнес-аналитики. |
|[Звезда Presto для Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto — это быстрый и масштабируемый механизм распределенных запросов SQL. Разработанная для разделения хранилища и вычислений Presto идеально подходит для выполнения запросов к данным в Azure Data Lake Storage, хранилищах BLOB-объектов Azure, базах данных SQL и NoSQL и других источниках данных. |
|[Сборщик данных StreamSets для HDInsight Cloud](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |Сборщик данных StreamSets — это упрощенная, мощная система, которая осуществляет потоковую передачу данных в режиме реального времени. Используйте сборщик данных для маршрутизации и обработки данных в потоках данных. Он поставляется с лицензией на 30 дней. |
|[Трифакта Вранглер Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |Трифакта Вранглер Enterprise для HDInsight поддерживает структурирование данных уровня предприятия для любого масштаба данных. Стоимость запуска Трифакта в Azure — это сочетание затрат на подписку Трифакта, а также затраты на инфраструктуру Azure для виртуальных машин. |
|[Платформа данных унифицированной платформы](https://unifisoftware.com/platform/) |Hadoop, HBase, в Spark |Платформа данных унифицированных структур — это простой интегрированный набор средств самостоятельного управления данными, позволяющий бизнес-пользователю решать проблемы с данными, которые повышают прибыль, сокращая затраты или эксплуатационную сложность. |
|[Унравелдата APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Приложение данных обнаружить для кластера HDInsight Spark. |
|[Каталог данных ватерлине на основе искусственного интеллекта](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Ватерлине каталоги, упорядочивает и управляет данными с помощью AI для автоматического тега данных с бизнес-терминами. Каталог грамотного бизнеса Waterline —это чрезвычайно важный компонент успешного проведения самостоятельной аналитики, обеспечения соответствия требованиям и системы управления, а также инициатив в области управления ИТ. |

В этой статье используется портал Azure. Вы можете экспортировать шаблон Azure Resource Manager с портала или получить его копию у поставщиков, а затем развернуть его с помощью Azure PowerShell и классического интерфейса командной строки Azure.  Ознакомьтесь со статьей [Создание кластеров Apache Hadoop в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Предварительные требования
Если вы хотите установить приложение HDInsight в существующем кластере HDInsight, вы должны создать кластер HDInsight. Инструкции по созданию кластера см. в [этом разделе](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Вы также можете установить приложения HDInsight во время создания кластера HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Установка приложений в имеющиеся кластеры
Здесь приведена процедура установки приложения HDInsight в имеющийся кластер HDInsight.

**Установка приложения HDInsight**

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню слева перейдите к разделу **все службы** > **аналитика** > **кластеры HDInsight**.
3. Выберите кластер HDInsight из списка.  Если у вас нет кластера, сначала его необходимо создать.  См. [этот раздел](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. В категории **Параметры** выберите **приложения**. Список установленных приложений можно просмотреть в главном окне. 
   
    ![Меню портала для приложений HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. В меню выберите пункт **+ Добавить** . Можно просмотреть список доступных приложений.  Если **+ Добавить** неактивно, это означает, что для этой версии кластера HDInsight нет приложений.
   
    ![Доступные приложения HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Выберите одно из доступных приложений, а затем следуйте инструкциям, чтобы принять условия лицензионного соглашения.

Состояние установки можно увидеть в уведомлениях портала (щелкните значок колокольчика в верхней части портала). После установки приложение отобразится в списке "Установленные приложения".

## <a name="install-applications-during-cluster-creation"></a>Установка приложения во время создания кластера
Вы также можете установить приложения HDInsight во время создания кластера. Их установка осуществляется после создания и запуска кластера. Чтобы установить приложения во время создания кластера с помощью портал Azure, используйте параметр **Custom** вместо параметра **Быстрое создание** по умолчанию.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Отображение списка установленных приложений HDInsight и их свойств
На портале содержится список установленных приложений HDInsight для кластера и их свойств.

**Список приложений HDInsight и их свойств**

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню слева перейдите к разделу **все службы** > **аналитика** > **кластеры HDInsight**.
3. Выберите кластер HDInsight из списка.
4. В категории **Параметры** выберите **приложения**. Список установленных приложений можно просмотреть в главном окне. 
   
    ![Установленные приложения HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Выберите одно из установленных приложений, чтобы отобразить свойство. В свойствах указано следующее:

    |Свойство | Описание |
    |---|---|
    |Имя приложения |Имя приложения. |
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

## <a name="next-steps"></a>Следующие шаги
* [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md)— узнайте, как развернуть в HDInsight неопубликованное приложение HDInsight.
* [Публикация приложения HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md). Узнайте, как опубликовать пользовательские приложения HDInsight в Microsoft Azure Marketplace.
* [MSDN. Application](https://msdn.microsoft.com/library/mt706515.aspx) (Приложение). Узнайте, как определить приложения HDInsight.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md)— узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Создание кластеров Apache Hadoop под управлением Linux в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) — узнайте, как вызывать шаблоны Resource Manager для создания кластеров HDInsight.
* [Использование пустых граничных узлов в HDInsight](hdinsight-apps-use-edge-node.md)— узнайте, как использовать пустой граничный узел для доступа к кластеру HDInsight, а также тестирования и размещения приложений HDInsight.

