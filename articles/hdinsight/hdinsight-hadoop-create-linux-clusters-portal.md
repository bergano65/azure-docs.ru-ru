---
title: Create Apache Hadoop clusters using web browser, Azure HDInsight
description: Learn how to create Apache Hadoop, Apache HBase, Apache Storm, or Apache Spark clusters on Linux for HDInsight by using a web browser and the Azure portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215915"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Создание кластеров под управлением Linux в HDInsight с помощью портала Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Портал Azure — это веб-инструмент управления службами и ресурсами, размещенными в облаке Microsoft Azure. Из этой статьи вы узнаете, как создавать кластеры Azure HDInsight под управлением Linux с помощью портала Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. Просмотрите видео о том, [как получить бесплатную пробную версию Azure для тестирования Hadoop в HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Современный браузер**. Портал Azure использует HTML5 и JavaScript. Он может неправильно работать в старых браузерах.

## <a name="create-clusters"></a>Создание кластеров

Портал Azure предоставляет большую часть свойств кластера. С помощью шаблонов Azure Resource Manager можно скрыть множество сведений. Дополнительные сведения см. в статье [Создание кластеров Apache Hadoop в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Войдите на [портале Azure](https://portal.azure.com).

1. From the left menu, navigate to **+ Create a resource** >  **Analytics** > **Azure HDInsight**.

    ![Create a new cluster in the Azure portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Создание кластера на портале Azure")

1. From the **Create HDInsight cluster** page, select **Go to classic create experience**.

    ![Go to classic create experience](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. На странице **HDInsight** выберите параметр **Настраиваемое (размер, параметры, приложения)** .

1. Выберите раздел **1, "Основные сведения"** . Then enter the following information:

    |Свойство |Описание |
    |---|---|
    |Имя кластера|Оно должно быть глобально уникальным.|
    |Subscription|From the drop-down list, select the Azure subscription that's used for the cluster.|
    |Тип кластера|Select the type of cluster you want to create. Например, Hadoop и Apache Spark. В поле **Операционная система** нужно выбрать **Linux**. Далее выберите версию типа кластера. Используйте версию по умолчанию, если не уверены, что именно нужно выбрать. Дополнительные сведения см. в статье [Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)|
    |Имя пользователя для входа в кластер|Provide the username, default is **admin**.|
    |Пароль для входа в кластер|Provide the password.|
    |Имя пользователя для Secure Shell (SSH)|Default is **sshuser**. If you want the same SSH password as the admin password you specified earlier, select the **Use cluster login password for SSH** check box. В противном случае введите **пароль** или **открытый ключ**, которые будут использоваться для аутентификации пользователя SSH. Мы рекомендуем использовать открытый ключ. Нажмите кнопку **Выбрать** внизу страницы, чтобы сохранить учетные данные конфигурации.  Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |группа ресурсов.|Укажите, следует ли создать новую группу ресурсов или использовать имеющуюся.|
    |Location|Specify a datacenter where the cluster is created.|

    ![HDInsight create cluster basics](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Создание кластера на портале Azure")

    > [!IMPORTANT]  
    > Кластеры HDInsight бывают разных типов. Они соответствуют рабочей нагрузке или технологии, для которой предназначен кластер. Создать кластер, в котором бы объединились несколько типов, нельзя. Например, Storm и HBase в одном кластере.

    Чтобы перейти к следующему шагу, нажмите кнопку **Далее**.

1. В разделе **2, "Безопасность и сети",** можно подключить кластер к виртуальной сети с помощью раскрывающегося меню. Выберите виртуальную сеть Azure и подсеть для размещения кластера в виртуальной сети. For information on using HDInsight with a virtual network, see [Plan a virtual network deployment for Azure HDInsight clusters](hdinsight-plan-virtual-network-deployment.md). Статья содержит конкретные требования к конфигурации виртуальной сети.

    If you want to use the **Enterprise Security Package**, follow these instructions: [Configure a HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Чтобы перейти к следующему шагу, нажмите кнопку **Далее**.

1. From **3 Storage**, for **Storage Account Settings**, specify whether you want Azure Storage or Azure Data Lake Storage as your default storage. Дополнительные сведения приведены в таблице ниже.

    | Primary Storage type | Описание |
    |------------------|-------------|
    | Служба хранилища Azure   |  * For **Selection method**, choose **My subscriptions** if you want to specify a storage account that's part of your Azure subscription. Затем выберите свою учетную запись хранения. В противном случае щелкните **Ключ доступа**. Затем укажите сведения об учетной записи хранения, которая не входит в вашу подписку Azure.</br></br> * For **Default container**, choose the default container name suggested by the portal or specify your own.</br></br> * If Azure Blob storage is your default storage, you can also select **Additional Storage Accounts** to specify additional storage accounts to associate with the cluster. Для параметра **Ключи хранилища Azure** выберите **Добавить ключ хранилища**. Затем можно предоставить учетную запись хранения из подписок Azure или из других подписок. Укажите ключ доступа к учетной записи хранения.</br></br> * If Blob storage is your default storage, you can also select **Data Lake Storage access** to specify Azure Data Lake Storage as additional storage. Дополнительные сведения см. в статье [Краткое руководство по установке кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | Служба хранилища Azure Data Lake | Select **Azure Data Lake Storage Gen1** or **Azure Data Lake Storage Gen2**. Then refer to the article [Quickstart: Set up clusters in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) for instructions. |

    **Metastore Settings (optional)**

    При необходимости укажите базу данных SQL Azure, в которой будут храниться связанные с кластером метаданные Apache Hive и Apache Oozie. Для параметра **Выбрать базу данных SQL для Hive** выберите базу данных SQL. Затем введите имя пользователя и пароль для базы данных. Повторите эти действия для метаданных Oozie.

    Ниже приведены важные особенности при использовании базы данных Azure SQL для хранилищ метаданных:
    * База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. На этом сервере выполняется экземпляр базы данных SQL. В представлении сервера выберите **Настроить**. Затем для параметра **Службы Azure** выберите значение **Да**. Затем нажмите кнопку **Save** (Сохранить).
    * При создании хранилища метаданных не следует использовать в названиях базы данных тире или дефисы. Это может привести к сбою при создании кластера.

    ![HDInsight create cluster storage](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Создание кластера на портале Azure")

    > [!WARNING]  
    > Использование дополнительной учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

    Чтобы перейти к следующему шагу, нажмите кнопку **Далее**.

1. В разделе **4, "Приложения (необязательно)",** выберите любые необходимые приложения. Эти приложения могут разрабатывать корпорация Майкрософт, независимые поставщики программного обеспечения (ISV) или вы сами. Дополнительные сведения см. в разделе [Установка приложения во время создания кластера](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Чтобы перейти к следующему шагу, нажмите кнопку **Далее**.

1. В разделе **5, "Размер кластера",** отображаются сведения об узлах, которые используются для этого кластера. Задайте количество рабочих узлов, необходимых для кластера. Также показана предполагаемая стоимость использования кластера.

    ![HDInsight create cluster nodes](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Specify number of cluster nodes")

   > [!IMPORTANT]  
   > Если вы планируете использовать более 32 рабочих узлов, для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ. Планируйте количество узлов либо при создании кластера, либо путем масштабирования кластера после создания.
   >
   > Дополнительные сведения о размерах узлов и их стоимости см. на странице с [ценами на Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Чтобы перейти к следующему шагу, нажмите кнопку **Далее**.

1. В разделе **6, "Действия скрипта",** можно настроить кластер для установки пользовательских компонентов. Используйте этот параметр, если хотите использовать настраиваемый сценарий для настройки кластера во время его создания. Дополнительные сведения о действиях сценариев см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](hdinsight-hadoop-customize-cluster-linux.md).

   Чтобы перейти к следующему шагу, нажмите кнопку **Далее**.

1. В разделе **7, "Сводка",** проверьте введенные ранее сведения. Щелкните **Создать**.

    ![HDInsight create cluster summary](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Specify number of cluster nodes")

    > [!NOTE]  
    > Обычно создание кластера занимает около 20 минут. Проверьте **Уведомления**, чтобы узнать, как идет процесс подготовки.

1. Когда кластер будет создан, нажмите кнопку **Перейти к ресурсу** в уведомлении **Развертывание прошло успешно**. В окне кластера содержатся следующие сведения.

    ![HDI Azure portal cluster overview](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Свойства кластера")

    Some of the icons in the window are explained as follows:

    |Свойство | Описание |
    |---|---|
    |Краткое описание|Provides all the essential information about the cluster. Например, имя, группа ресурсов, к которой она принадлежит, расположение, операционная система и URL-адрес панели мониторинга кластера.|
    |Панели мониторинга кластера|Directs you to the Ambari portal associated with the cluster.|
    |SSH + Cluster login|Provides information needed to access the cluster by using SSH.|
    |Удаление|Удаление кластера HDInsight.|

## <a name="customize-clusters"></a>Настройка кластеров

* [Настройка кластеров HDInsight с помощью начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно создали кластер HDInsight. Теперь узнайте, как работать с кластером.

### <a name="apache-hadoop-clusters"></a>Кластеры Apache Hadoop

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hadoop/hdinsight-use-hive.md)
* [Использование MapReduce с HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Кластеры Apache HBase

* [Начало работы с Apache HBase в HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для Apache HBase в HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Кластеры Apache Storm

* [Создание топологий для Apache Storm в HDInsight на языке Java](storm/apache-storm-develop-java-topology.md)
* [Использование компонентов Python в Apache Storm в HDInsight](storm/apache-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологии с помощью Apache Storm в HDInsight.](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Кластеры Apache Spark

* [Руководство. Создание приложения Scala Maven для Apache Spark в HDInsight с помощью IntelliJ](spark/apache-spark-create-standalone-application.md)
* [Удаленная отправка заданий Spark в кластер Azure HDInsight с помощью Apache Spark REST API](spark/apache-spark-livy-rest-interface.md)
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](spark/apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)
