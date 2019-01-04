---
title: Создание кластеров Apache Hadoop с помощью веб-браузера в Azure HDInsight
description: Узнайте, как создавать кластеры Apache Hadoop, Apache HBase, Apache Storm или Apache Spark на платформе Linux в HDInsight с помощью браузера и портала предварительной версии Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 7ff69d3b79662cff1fc28b0b59777225d0cc439f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189036"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Создание кластеров под управлением Linux в HDInsight с помощью портала Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Портал Azure — это веб-инструмент управления службами и ресурсами, размещенными в облаке Microsoft Azure. Из этой статьи вы узнаете, как создавать кластеры HDInsight под управлением Linux с помощью портала Azure.

## <a name="prerequisites"></a>Предварительные требования
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Современный браузер**. Портал Azure использует HTML5 и JavaScript и может работать неправильно в старых веб-браузерах.

## <a name="create-clusters"></a>Создание кластеров
Портал Azure предоставляет большую часть свойств кластера. С помощью шаблона Azure Resource Manager можно скрыть много сведений. Дополнительные сведения см. в статье [Create Apache Hadoop clusters in HDInsight by using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Создание кластеров Apache Hadoop в HDInsight с помощью шаблонов Resource Manager).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Войдите на [портал Azure](https://portal.azure.com).
2. Последовательно выберите **+ Создать ресурс**, **Аналитика** и **HDInsight**.
   
    ![Создание кластера на портале Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "Creating a new cluster in the Azure portal")

3. В колонке **HDInsight** щелкните **Custom (size, settings, apps)** (Пользовательские (размер, параметры, приложения)), **Основы** и введите следующие сведения.

    ![Создание кластера на портале Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "Creating a new cluster in the Azure portal")

    * Введите **имя кластера**. Оно должно быть глобально уникальным.

    * В раскрывающемся списке **Подписка** выберите подписку Azure, которая используется для кластера.

    * Щелкните **Тип кластера**, а затем выберите тип кластера (Hadoop, Spark и т. д.), который нужно создать. Для параметра **Операционная система** щелкните **Linux**, а затем выберите версию. Используйте версию по умолчанию, если не уверены, что именно нужно выбрать. Дополнительные сведения см. в статье [Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)
     
        > [!IMPORTANT]
        > Кластеры HDInsight бывают разных типов, которые соответствуют рабочей нагрузке или технологии, для которой настроен кластер. Создать кластер, в котором бы объединились несколько типов, например Storm и HBase, нельзя. 
        > 
        > 
        
    * В полях **Имя пользователя для входа в кластер** и **Пароль для входа в кластер** введите имя пользователя администратора и пароль для кластера.

    * Введите **имя пользователя SSH**. Если требуется, чтобы пароль SSH был аналогичен указанному ранее паролю администратора, установите флажок **Использовать тот же пароль в учетных данных кластера**. В противном случае введите **пароль** или **открытый ключ**, которые будут использоваться для аутентификации пользователя SSH. Мы рекомендуем использовать открытый ключ. Нажмите кнопку **Выбрать** внизу страницы, чтобы сохранить учетные данные конфигурации.
   
        См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Для параметра **Группа ресурсов** укажите, хотите ли вы создать новую группу ресурсов или использовать существующую.

    * Укажите **расположение** центра обработки данных, в котором создан кластер.

    * Щелкните **Далее**.

4. В разделе **безопасности и сетей** вы можете подключить кластер к виртуальной сети с помощью раскрывающегося списка. Выберите виртуальную сеть Azure и подсеть для размещения кластера в виртуальной сети. Сведения об использовании HDInsight в виртуальной сети, включая требования к конфигурации виртуальной сети, см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md). Если нужно использовать **Корпоративный пакет безопасности**, можно выполнить приведенные здесь инструкции: [Настройка кластера HDInsight с Корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

5. В колонке **Хранилище** укажите нужное хранилище по умолчанию: службу хранилища Azure (WASB) или Data Lake Storage. Дополнительные сведения см. в таблице ниже.

     ![Создание кластера на портале Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "Creating a new cluster in the Azure portal")

     | Хранилище                                      | Описание |
     |----------------------------------------------|-------------|
     | **Большие двоичные объекты службы хранилища Azure как хранилище по умолчанию**   | <ul><li>В разделе **Тип первичного хранилища** выберите **Служба хранилища Azure**. После этого в разделе **Метод выбора** выберите вариант **Мои подписки**, если хотите указать учетную запись хранения, которая входит в вашу подписку Azure, а затем выберите учетную запись хранения. Или щелкните **Ключ доступа** и укажите сведения об учетной записи хранения, которая не входит в вашу подписку Azure.</li><li>В поле **Контейнер по умолчанию** можно оставить имя контейнера по умолчанию, предлагаемое порталом, или указать собственное.</li><li>Если в качестве хранилища по умолчанию используется WASB, вы можете щелкнуть **Дополнительные учетные записи хранения**, чтобы указать дополнительные учетные записи хранения, которые нужно связать с кластером. В области **Ключи хранилища Azure** щелкните **Добавить ключ хранилища** и выберите учетную запись хранения из подписки Azure или из других подписок (указав ключ доступа к учетной записи хранения).</li><li>При использовании WASB в качестве хранилища по умолчанию вы можете щелкнуть **Доступ к Data Lake Store** и указать Azure Data Lake Storage в качестве дополнительного хранилища. Дополнительные сведения см. в [кратком руководстве по настройке кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage как хранилище по умолчанию** | Для параметра **Тип первичного хранилища** выберите **Azure Data Lake Storage 1-го поколения** или **Azure Data Lake Storage 2-го поколения (предварительная версия)**, а затем ознакомьтесь с инструкциями в статье [Краткое руководство по настройке кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md). |
     | **Внешние хранилища метаданных**                      | При необходимости укажите базу данных SQL Azure, в которой будут храниться связанные с кластером метаданные Hive и Oozie. Для параметра **Select a SQL database for Hive** (Выбрать базу данных SQL для Hive) укажите базу данных SQL, а затем введите имя пользователя и пароль для базы данных. Повторите эти действия для метаданных Oozie.<br><br>Важные особенности при использовании базы данных Azure SQL для хранилищ метаданных. <ul><li>База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем — **Службы Azure**, **Да** и **Сохранить**.</li><li>Создавая метаданные, не используйте для базы данных имя, которое содержит тире и дефисы. Это может привести к сбою при создании кластера.</li></ul> |

     Щелкните **Далее**. 

     > [!WARNING]
     > Использование дополнительной учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

6. При необходимости щелкните **Приложения**, чтобы установить приложения, работающие с кластерами HDInsight. Разработчиками этих приложений могут быть корпорация Майкрософт, независимые поставщики программного обеспечения или вы сами. Дополнительные сведения см. в разделе [Установка приложения во время создания кластера](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Щелкните **Размер кластера**, чтобы отобразить сведения об узлах, которые используются для этого кластера. Задайте количество рабочих узлов, необходимых для кластера. Также показана предполагаемая стоимость использования кластера.
   
    ![Ценовые категории узлов](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "Указание количества узлов кластера")
   
   > [!IMPORTANT]
   > Если вы планируете использовать более 32 рабочих узлов (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
   > 
   > Дополнительные сведения о размерах узлов и их стоимости см. на странице с [ценами на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Нажмите кнопку **Далее**, чтобы сохранить конфигурацию цен узла.

8. В разделе **Действия скрипта** вы можете настроить кластер для установки пользовательских компонентов.  Используйте этот параметр, если хотите использовать настраиваемый скрипт для настройки кластера во время его создания. Дополнительную информацию о действиях скриптов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](hdinsight-hadoop-customize-cluster-linux.md).
Щелкните **Далее**.

9. В разделе **Сводка** проверьте ранее введенные сведения и нажмите кнопку **Создать**.

     ![Ценовые категории узлов](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "Указание количества узлов кластера")
    
    > [!NOTE]
    > Обычно создание кластера занимает около 15 минут. Отслеживать процесс подготовки можно с помощью плитки на начальной панели или записи **Уведомления** в левой части страницы.
    > 
    > 
10. После завершения процесса создания щелкните плитку кластера на начальной панели. В окне кластера содержатся следующие сведения.
    
    ![Интерфейс кластера](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "Свойства кластера")
    
    Ниже приведено описание значков в верхней части колонки.
    
    * На вкладке **Обзор** указаны все важные сведения о кластере: имя, группа ресурсов, к которой он принадлежит, расположение, операционная система, URL-адрес панели мониторинга кластера и т. д.
    * Элемент **Панель мониторинга** перенаправит вас на портал Ambari, связанный с кластером.
    * **Secure Shell**. Сведения, необходимые для доступа к кластеру по протоколу SSH.
    * Элемент **Изменить масштаб кластера** позволяет увеличить число рабочих узлов, связанных с кластером.
      * **Удалить**. Удаление кластера HDInsight.
    

## <a name="customize-clusters"></a>Настройка кластеров
* Ознакомьтесь с разделом [Настройка кластеров HDInsight с помощью начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md).
* См. статью [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Удаление кластера
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы успешно создали кластер HDInsight, обратитесь к указанным ниже статьям, чтобы научиться работать с кластером.

### <a name="apache-hadoop-clusters"></a>Кластеры Apache Hadoop
* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hadoop/hdinsight-use-hive.md)
* [Использование Apache Pig с HDInsight](hadoop/hdinsight-use-pig.md)
* [Использование Apache Hadoop MapReduce в HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Кластеры Apache HBase
* [Начало работы с Apache HBase в HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для Apache HBase в HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Кластеры Apache Storm
* [Создание топологий для Apache Storm в HDInsight на языке Java](storm/apache-storm-develop-java-topology.md)
* [Использование компонентов Python в Apache Storm в HDInsight](storm/apache-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологии с помощью Apache Storm в HDInsight.](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Кластеры Apache Spark
* [Создание автономного приложения с использованием Scala](spark/apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](spark/apache-spark-livy-rest-interface.md)
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](spark/apache-spark-use-bi-tools.md)
* [Использование Apache Spark MLlib для создания приложения машинного обучения и анализа набора данных](spark/apache-spark-machine-learning-mllib-ipython.md)

