---
title: Установка сторонних приложений в Azure HDInsight
description: Узнайте, как установить сторонние приложения Apache Hadoop на Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366348"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Установка сторонних приложений Apache Hadoop в Azure HDInsight

Узнайте, как установить стороннее приложение [Apache Hadoop](https://hadoop.apache.org/) в Azure HDInsight. Инструкции по установке собственного приложения см. в статье [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md).

Пользователи могут устанавливать приложения HDInsight в кластере HDInsight. Разработчиками этих приложений могут быть корпорация Майкрософт, независимые поставщики программного обеспечения или вы сами.  

Опубликованные приложения представлены в следующем списке:

|Приложение |Тип кластера (ы) | Описание |
|---|---|---|
|[Платформа AtScale Intelligence](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale превращает ваш кластер HDInsight в масштабируемый сервер OLAP, что позволяет задавать вопрос о миллиардах строк данных интерактивно, используя уже знающий, владеющие и любящие инструменты BI — от Microsoft Excel, Power BI, программного обеспечения Tableau до «likView». |
|[CDAP для HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP является первой единой интеграционной платформой для больших данных, которая ускоряет время для оценки Hadoop и позволяет ИТ-специалистам предоставлять данные самообслуживания. CDAP с открытым исходным кодом и расширяемым, устраняет барьеры на пути инноваций. Требования: 4 региональные узлы, мин D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Масштабируемая платформа самообслуживания Datameer для подготовки, изучения и управления данными для аналитики ускоряет превращение сложных многоресурсных данных в ценную информацию, готовую к работе, обеспечивая более быструю и интеллектуальную информацию в корпоративном масштабе. |
|[Dataiku DSS на HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS в корпоративной платформе для получения данных, которая позволяет ученым и аналитикам данных сотрудничать в разработке и более эффективном проектировании и запуске новых продуктов и услуг передачи данных, превращая необработанные данные в впечатляющие прогнозы. |
|[WANdisco Fusion HDI App](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Хадуп, Спарк,Хбейс, Шторм, Кафка |Поддержание согласованности данных в распределенной среде является огромной задачей операций с данными. WANdisco Fusion, программная платформа корпоративного класса, решает эту проблему, обеспечивая неструктурированную согласованность данных в любой среде. |
|[H2O игристаявода для HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water поддерживает следующие распределенные алгоритмы: GLM, Na've Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection, Autoencoders. |
|[Striim для интеграции данных в режиме реального времени в HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Хадуп,HBase,Шторм, Искра, Кафка |Striim — это комплексная платформа для интеграции и аналитики потоковой передачи данных, обеспечивающая непрерывный прием, обработку и анализ разрозненных потоков данных. |
|[Jumbune Предприятие-Ускорение BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |На высоком уровне, Jumbune помогает предприятиям, 1. Ускорение Tez, MapReduce & Spark двигатель на основе Hive, Java, Scala производительности рабочей нагрузки. 2. Проактивный мониторинг кластера Хадуп, 3. Создание управления качеством данных в распределенной файловой системе. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Хадуп,HBase,Spark |Работает на Apache Kylin, Kyligence Enterprise позволяет BI на больших данных. Как двигатель OLAP на предприятии, Kyligence Enterprise предоставляет бизнес-аналитику возможность архитектора BI на Hadoop с отраслевым стандартом хранилища данных и методологией BI. |
|[Starburst Presto для Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto — это быстрый и масштабируемый распределенный движок запросов S'L. Разработанная для разделения хранилища и вычислений Presto идеально подходит для выполнения запросов к данным в Azure Data Lake Storage, хранилищах BLOB-объектов Azure, базах данных SQL и NoSQL и других источниках данных. |
|[Сборщик данных StreamSets для облака HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Хадуп,Хбасе, Спарк,Кафка |StreamSets Data Collector — это легкий и мощный движок, который передает данные в режиме реального времени. Используйте collector для маршрутизации и обработки данных в потоках данных. Он поставляется с 30-дневной лицензией суда. |
|[Трифама Вранглер Энтерпрайз](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Хадуп, Искра,HBase |Trifacta Wrangler Enterprise для HDInsight поддерживает общекорпоративные данные, споры о любом масштабе данных. Затраты на запуск Trifacta на Azure — это сочетание затрат на подписку Trifacta плюс затраты на инфраструктуру Azure для виртуальных машин. |
|[Платформа данных Unifi](https://unifisoftware.com/platform/) |Хадуп,HBase,Шторм, Искра |Платформа unifi Data Platform представляет собой комплекс инструментов самообслуживания данных, предназначенный для расширения возможностей бизнес-пользователей для решения проблем данных, которые приводят к дополнительным доходам, снижению затрат или сложности эксплуатации. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Разгадать данные приложение для кластера HDInsight Spark. |
|[Каталог данных, управляемых ИИ, Waterline AI](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline каталогизирует, организует и регулирует данные с помощью ИИ для автоматического тегов данных с бизнес-условиями. Бизнес-грамотный каталог Waterline является критическиважным компонентом успеха для аналитики самообслуживания, соответствия и управления, а также инициатив по управлению ИТ. |

В этой статье используется портал Azure. Вы можете экспортировать шаблон Azure Resource Manager с портала или получить его копию у поставщиков, а затем развернуть его с помощью Azure PowerShell и классического интерфейса командной строки Azure.  Ознакомьтесь со статьей [Создание кластеров Apache Hadoop в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Предварительные требования
Если вы хотите установить приложение HDInsight в существующем кластере HDInsight, вы должны создать кластер HDInsight. Инструкции по созданию кластера см. в [этом разделе](hadoop/apache-hadoop-linux-tutorial-get-started.md). Вы также можете установить приложения HDInsight во время создания кластера HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Установка приложений в имеющиеся кластеры
Здесь приведена процедура установки приложения HDInsight в имеющийся кластер HDInsight.

**Установка приложения HDInsight**

1. Войдите на [портал Azure](https://portal.azure.com).
2. Из левого меню перейдите на **все службы** > **Analytics** > **HDInsight кластеров**.
3. Выберите кластер HDInsight из списка.  Если у вас нет кластера, сначала его необходимо создать.  См. [этот раздел](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. В категории **«Настройки»** выберите **приложения.** Список установленных приложений можно увидеть в главном окне. 
   
    ![Меню портала для приложений HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Выберите **«Добавить»** из меню. Вы можете увидеть список доступных приложений.  Если **«Добавить»** поседает серый цвет, это означает, что для этой версии кластера HDInsight нет приложений.
   
    ![Доступные приложения HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Выберите одно из доступных приложений, а затем следуйте инструкциям, чтобы принять юридические условия.

Статус установки можно увидеть из уведомлений портала (выберите значок колокола в верхней части портала). После установки приложение отобразится в списке "Установленные приложения".

## <a name="install-applications-during-cluster-creation"></a>Установка приложения во время создания кластера

Вы также можете установить приложения HDInsight во время создания кластера. Их установка осуществляется после создания и запуска кластера. Для установки приложений во время создания кластера с помощью портала Azure, со вкладки **«Конфигурация и цены»** выберите **приложение «Добавить».**

![Приложения конфигурации кластеров порталов Azure](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Отображение списка установленных приложений HDInsight и их свойств
На портале содержится список установленных приложений HDInsight для кластера и их свойств.

**Список приложений HDInsight и их свойств**

1. Войдите на [портал Azure](https://portal.azure.com).
2. Из левого меню перейдите на **все службы** > **Analytics** > **HDInsight кластеров**.
3. Выберите кластер HDInsight из списка.
4. В категории **«Настройки»** выберите **приложения.** Список установленных приложений можно увидеть в главном окне. 
   
    ![Установленные приложения HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Выберите одно из установленных приложений для отобрагиваемого свойства. В свойствах указано следующее:

    |Свойство | Описание |
    |---|---|
    |Имя приложения. |Имя приложения. |
    |Состояние |Статус приложения. |
    |Веб-страница |URL-адрес веб-приложения, развернутого на граничном узле. В качестве учетных данных используются учетные данные пользователя HTTP, настроенные для кластера. |
    |Конечная точка SSH |К граничному узлу можно подключиться с помощью SSH. В качестве учетных данных SSH используются учетные данные пользователя SSH, настроенные для кластера. См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Описание | Описание приложения. |

6. Чтобы удалить приложение, щелкните его правой кнопкой мыши и в контекстном меню выберите **Удалить**.

## <a name="connect-to-the-edge-node"></a>Подключение к граничному узлу
Для подключения к граничному узлу можно использовать протокол HTTP и SSH. Дополнительные сведения о конечной точке см. на [портале](#list-installed-hdinsight-apps-and-properties). См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

В качестве учетных данных конечной точки HTTP используютсяучетные данные пользователя HTTP, настроенные для кластера HDInsight. Что же касается конечной точки SSH, используются учетные данные пользователя SSH, настроенные для кластера HDInsight.

## <a name="troubleshoot"></a>Устранение неполадок
См. раздел [Устранение неполадок, связанных с установкой](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Дальнейшие действия
* [Установите пользовательские приложения HDInsight:](hdinsight-apps-install-custom-applications.md)узнайте, как развернуть неопубликованное приложение HDInsight в HDInsight.
* [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md)— узнайте, как опубликовать пользовательские приложения HDInsight в Azure Marketplace.
* [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(Установка приложения HDInsight) — узнайте, как определить приложения HDInsight.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md)— узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Создание кластеров Apache Hadoop под управлением Linux в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) — узнайте, как вызывать шаблоны Resource Manager для создания кластеров HDInsight.
* [Использование пустых граничных узлов в HDInsight](hdinsight-apps-use-edge-node.md)— узнайте, как использовать пустой граничный узел для доступа к кластеру HDInsight, а также тестирования и размещения приложений HDInsight.

