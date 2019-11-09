---
title: Создание кластеров Azure HDInsight с помощью Data Lake Storage 1-го поколения-Portal
description: Создание и использование кластеров HDInsight для работы с Azure Data Lake Storage 1-го поколения с помощью портала Azure
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838174"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Создание кластеров HDInsight, использующих Azure Data Lake Storage 1-го поколения, с помощью портала Azure

> [!div class="op_single_selector"]
> * [Использование портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Использование PowerShell (для хранилища по умолчанию)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Использование PowerShell (для дополнительного хранилища)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Использование Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Узнайте, как использовать портал Azure для создания кластера HDInsight с учетной записью Azure Data Lake Storage 1-го поколения в качестве хранилища по умолчанию или дополнительного хранилища. Несмотря на то, что дополнительное хранилище является необязательным для кластера HDInsight, рекомендуется хранить бизнес-данные в дополнительных учетных записях хранения.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать, убедитесь, что выполнены следующие требования:

* **Подписка Azure**. Перейдите на сайт [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Data Lake Storage 1-го поколения**. Следуйте инструкциям в статье [Начало работы с Azure Data Lake Storage 1-го поколения с помощью портала Azure](data-lake-store-get-started-portal.md). В учетной записи также необходимо создать корневую папку.  В этой статье используется корневая папка с именем __/Clusters__ .
* **Субъект-служба Azure Active Directory**. В этом пошаговом руководство приведены инструкции по созданию субъекта-службы в Azure Active Directory (Azure AD). Чтобы создать субъект-службу, необходимо быть администратором Azure AD. Если вы являетесь администратором, вы можете пропустить этот предварительный компонент и продолжить.

>[!NOTE]
>Вы можете создать субъект-службу только в том случае, если вы являетесь администратором Azure AD. Администратор Azure AD должен сначала создать субъект-службу, после чего вы сможете создать кластер HDInsight, использующий Data Lake Storage 1-го поколения. При создании субъекта-службы также необходимо использовать сертификат, как описано в разделе [Создание субъекта-службы с использованием сертификата](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Создание кластера HDInsight

В этом разделе вы создадите кластер HDInsight с учетными записями Data Lake Storage 1-го поколения в качестве хранилища по умолчанию или дополнительного хранилища. В этой статье основное внимание уделяется только части настройки учетных записей Data Lake Storage 1-го поколения. Общие сведения о создании кластеров и соответствующие процедуры см. в статье [Создание кластеров Hadoop в HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Создание кластера HDInsight, использующего Data Lake Storage 1-го поколения в качестве хранилища по умолчанию

Чтобы создать кластер HDInsight с учетной записью Data Lake Storage 1-го поколения в качестве учетной записи хранения по умолчанию:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Общие сведения о создании кластеров HDInsight см. в разделе [Создание кластеров](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters).
3. В колонке **Хранилище** в разделе **Тип первичного хранилища** выберите **Data Lake Storage 1-го поколения**, а затем введите указанные ниже сведения.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Добавление субъекта-службы в кластер HDInsight")

    * **Выбрать учетную запись Data Lake Store**: выберите существующую учетную запись Data Lake Storage 1-го поколения. Требуется существующая учетная запись Data Lake Storage 1-го поколения.  См. раздел [Предварительные требования](#prerequisites).
    * **Корневой путь**: введите путь к каталогу, в котором будут храниться файлы, связанные с кластером. На снимке экрана это путь __/clusters/myhdiadlcluster/__ , в котором должна существовать папка __/clusters__ и в котором портал создает папку *myhdicluster*.  *myhdicluster* — это имя кластера.
    * **Доступ к Data Lake Store**: настройте доступ между учетной записью Data Lake Storage 1-го поколения и кластером HDInsight. Инструкции см. в разделе [Настройка доступа к Data Lake Storage 1-го поколения](#configure-data-lake-storage-gen1-access).
    * **Дополнительные учетные записи хранения**: добавьте учетные записи хранения Azure в качестве дополнительных учетных записей хранения для кластера. Добавление дополнительных хранилищ Data Lake Storage 1-го поколения осуществляется путем предоставления разрешений на доступ к данным в кластере дополнительным учетным записям Data Lake Storage 1-го поколения. При этом учетная запись Data Lake Storage 1-го поколения настраивается в качестве основного типа хранилища. Инструкции см. в разделе [Настройка доступа к Data Lake Storage 1-го поколения](#configure-data-lake-storage-gen1-access).

4. В колонке **Доступ к Data Lake Store** нажмите кнопку **Выбрать** и продолжайте создание кластера, как описано в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Создание кластера HDInsight, использующего Data Lake Storage 1-го поколения в качестве дополнительного хранилища

Ниже приведены инструкции по созданию кластера HDInsight, использующего учетную запись хранения Azure в качестве хранилища по умолчанию и учетную запись Data Lake Storage 1-го поколения в качестве дополнительного хранилища.

Чтобы создать кластер HDInsight с учетной записью Data Lake Storage 1-го поколения в качестве дополнительной учетной записи хранения, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Общие сведения о создании кластеров HDInsight см. в разделе [Создание кластеров](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters).
3. В колонке **Хранилище** в разделе **Тип первичного хранилища** выберите **Хранилище Azure**, а затем введите указанные ниже сведения.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Добавление субъекта-службы в кластер HDInsight")

    * **Способ выбора** . чтобы указать учетную запись хранения, которая является частью подписки Azure, выберите **Мои подписки**, а затем выберите учетную запись хранения. Чтобы указать учетную запись хранения, не входящую в вашу подписку Azure, выберите **Ключ доступа**, а затем введите данные внешней учетной записи хранения.

    * **Контейнер по умолчанию** — используйте либо значение по умолчанию, либо укажите собственное имя.
    * **Дополнительные учетные записи хранения** — добавьте дополнительные учетные записи хранения Azure в качестве дополнительного хранилища.
    * **Data Lake Store доступ** . Настройка доступа между учетной записью Data Lake Storage 1-го поколения и кластером HDInsight. Инструкции см. в разделе [Настройка доступа к Data Lake Storage 1-го поколения](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Настройка доступа к Data Lake Storage 1-го поколения

В этом разделе вы настроите доступ к Data Lake Storage 1-го поколения из кластеров HDInsight с помощью субъекта-службы Azure Active Directory.

### <a name="specify-a-service-principal"></a>Указание субъекта-службы

На портале Azure можно использовать существующий субъект-службу или создать новый.

Чтобы создать субъект-службу из портал Azure:

1. В колонке хранилища выберите **Data Lake Store доступ** .
1. В колонке **Data Lake Storage 1-го поколения доступ** выберите **создать**.
1. Выберите **субъект-служба**, а затем следуйте инструкциям по созданию участника службы.
1. Скачайте сертификат, если его потребуется использовать в будущем. Скачать сертификат удобно, если вы холите использовать этот же субъект-службу для создания дополнительных кластеров HDInsight.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Добавление субъекта-службы в кластер HDInsight")

1. Выберите **доступ** , чтобы настроить доступ к папке.  См. раздел [Настройка разрешений для файлов](#configure-file-permissions).

Чтобы использовать существующий субъект-службу из портал Azure:

1. Выберите **Data Lake Store доступ**.
1. В колонке **Data Lake Storage 1-го поколения доступ** выберите **использовать существующий**.
1. Выберите **субъект-служба**, а затем выберите субъект службы.
1. Отправьте связанный с выбранным субъектом-службой сертификат (PFX-файл) и введите пароль этого сертификата.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Добавление субъекта-службы в кластер HDInsight")

1. Выберите **доступ** , чтобы настроить доступ к папке.  См. раздел [Настройка разрешений для файлов](#configure-file-permissions).

### <a name="configure-file-permissions"></a>Настройка разрешений для файлов

Конфигурация отличается в зависимости от того, используется ли учетная запись в качестве хранилища по умолчанию или дополнительной учетной записи хранения.

* В качестве хранилища по умолчанию:

  * разрешение на корневом уровне учетной записи Data Lake Storage 1-го поколения;
  * разрешение на корневом уровне хранилища кластера HDInsight. Например, в этом учебнике это папка __/clusters__.

* В качестве дополнительного хранилища:

  * разрешение на доступ к папкам, в которых находятся требуемые файлы.

Чтобы назначить разрешение на корневом уровне учетной записи Data Lake Storage 1-го поколения:

1. В колонке **Data Lake Storage 1-го поколения доступ** выберите **доступ**. Откроется колонка **Выбор разрешений для файла**. В ней отображены все учетные записи Data Lake Storage 1-го поколения в вашей подписке.
1. Наведите указатель мыши (но не щелкайте) на имя учетной записи Data Lake Storage 1-го поколения, чтобы отобразился флажок, а затем установите этот флажок.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Добавление субъекта-службы в кластер HDInsight")

   По умолчанию выбраны разрешения __Чтение__, __Запись__ и __Выполнение__.

1. В нижней части страницы щелкните **Выбрать**.
1. Выберите **выполнить** , чтобы назначить разрешение.
1. Нажмите кнопку **Готово**.

Чтобы назначить разрешение на корневом уровне кластера HDInsight, сделайте следующее:

1. В колонке **Data Lake Storage 1-го поколения доступ** выберите **доступ**. Откроется колонка **Выбор разрешений для файла**. В ней отображены все учетные записи Data Lake Storage 1-го поколения в вашей подписке.
1. В колонке **Выбор разрешений для файла** выберите имя учетной записи Data Lake Storage 1-го поколения, чтобы отобразить ее содержимое.
1. Выберите корень хранилища кластера HDInsight, установив флажок слева от папки. В соответствии с предыдущим снимком экрана корнем хранилища кластера является папка __/clusters__, указанная при выборе Data Lake Storage 1-го поколения в качестве хранилища по умолчанию.
1. Задайте разрешения для папки.  По умолчанию выбраны разрешения на чтение, запись и выполнение.
1. В нижней части страницы щелкните **Выбрать**.
1. Выберите **Запуск**.
1. Нажмите кнопку **Готово**.

При использовании Data Lake Storage 1-го поколения в качестве дополнительного хранилища необходимо назначить разрешение только для папок, к которым нужен доступ из кластера HDInsight. Например, на снимке экрана ниже предоставляется доступ только к папке **mynewfolder** в учетной записи Data Lake Storage 1-го поколения.

![Назначение разрешений субъекта-службы кластеру HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Назначение разрешений субъекта-службы кластеру HDInsight")

## <a name="verify-cluster-set-up"></a>Проверка установки кластера

После завершения настройки кластера проверьте результат в колонке кластера, выполнив одно или оба указанных ниже действия.

* Чтобы убедиться, что связанное хранилище кластера является указанной учетной записью Data Lake Storage 1-го поколения, выберите **учетные записи хранения** в левой области.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Добавление субъекта-службы в кластер HDInsight")

* Чтобы убедиться, что субъект-служба правильно связан с кластером HDInsight, на левой панели выберите **Data Lake Storage 1-го поколения доступ** .

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Добавление субъекта-службы в кластер HDInsight")

## <a name="examples"></a>Примеры

После настройки кластера с Data Lake Storage 1-го поколения в качестве хранилища см. примеры использования кластера HDInsight для анализа данных, хранящихся в Data Lake Storage 1-го поколения.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Выполнение запроса Hive к данным, хранящимся в Data Lake Storage 1-го поколения (основное хранилище)

Чтобы выполнить запрос Hive, используйте интерфейс представлений Hive, доступный на портале Ambari. Инструкции по использованию представлений Ambari Hive см. в статье [Использование представления Hive с Hadoop в HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

При работе с данными в Data Lake Storage 1-го поколения изменить можно лишь несколько строк.

Например, если вы используете созданный кластер, в котором основным хранилищем является Data Lake Storage 1-го поколения, то путь к данным будет следующим: *adl://<имя_учетной_записи_data_lake_storage_gen1>/azuredatalakestore.net/path/to/file*. Запрос Hive для создания таблицы на основе демонстрационных данных, хранящихся в учетной записи Data Lake Storage 1-го поколения, выглядит следующим образом:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Описание

* `adl://hdiadlsg1storage.azuredatalakestore.net/` — корневой элемент учетной записи Data Lake Storage 1-го поколения;
* `/clusters/myhdiadlcluster` — корень данных кластера, указанный при создании кластера;
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` — расположение примера файла, который используется в запросе.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Выполнение запроса Hive к данным, хранящимся в Data Lake Storage 1-го поколения (дополнительное хранилище)

Если созданный кластер в качестве хранилища по умолчанию использует хранилище BLOB-объектов, то демонстрационные данные не будут находиться в учетной записи Data Lake Storage 1-го поколения, используемой в качестве дополнительного хранилища. В этом случае сначала передайте данные из хранилища BLOB-объектов в Data Lake Storage 1-го поколения, а затем выполните запросы, как показано в предыдущем примере.

Сведения о том, как скопировать данные из хранилища BLOB-объектов в Data Lake Storage 1-го поколения, см. в следующих статьях:

* [Использование Distcp для копирования данных между BLOB-объектами и Data Lake Storage 1-го поколения](data-lake-store-copy-data-wasb-distcp.md)
* [Использование AdlCopy для копирования данных из BLOB-объектов в Data Lake Storage 1-го поколения](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Использование Data Lake Storage 1-го поколения с кластером Spark

Кластер Spark можно использовать для выполнения заданий Spark с данными, хранящимися в Data Lake Storage 1-го поколения. Дополнительные сведения см. в статье [Использование кластера HDInsight Spark для анализа данных в Data Lake Storage 1-го поколения](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Использование Data Lake Storage 1-го поколения в топологии Storm

Data Lake Storage 1-го поколения можно использовать для записи данных из топологии Storm. Инструкции по реализации этого сценария см. в статье [Использование Azure Data Lake Storage 1-го поколения с Apache Storm в HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Дополнительные материалы

* [Использование Data Lake Storage 1-го поколения с кластерами Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Создание кластеров HDInsight, использующих Data Lake Storage 1-го поколения, с помощью PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
