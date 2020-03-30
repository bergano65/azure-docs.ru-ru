---
title: Библиотеки Apache Hive во время создания кластера - Azure HDInsight
description: Узнайте, как добавить библиотеки Apache Hive (файлы банки) в кластер HDInsight во время создания кластера.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471029"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Добавление пользовательских библиотек Apache Hive при создании кластера HDInsight

Узнайте, как предварительно загрузить [библиотеки Apache Hive](https://hive.apache.org/) на HDInsight. Этот документ содержит сведения об использовании действия скрипта по предварительной загрузке библиотек во время создания кластера. Библиотеки, добавленные с использованием шагов в этом документе, доступны во всем мире в Hive - нет необходимости использовать [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) для их загрузки.

## <a name="how-it-works"></a>Принцип работы

При создании кластера можно использовать действие скрипта для изменения кластерных узлов при их создании. Скрипт в этом документе принимает один параметр, определяющий расположение библиотек. Это расположение должно быть в пределах учетной записи хранилища Azure, а библиотеки должны быть в формате JAR.

Во время создания кластера сценарий перечисляет файлы, копирует их в каталог `/usr/lib/customhivelibs/` на головных и рабочих узлах, а затем добавляет их в свойство `hive.aux.jars.path` в файле `core-site.xml`. Для кластеров под управлением Linux он также обновляет файл `hive-env.sh` расположением файлов.

Использование действия скрипта в этой статье делает библиотеки доступными при использовании клиента Hive для **WebHCat**и **HiveServer2.**

## <a name="the-script"></a>Сценарий

**Расположение скрипта**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Требования

* Скрипты необходимо применять к **головным узлам** и **рабочим узлам**.

* JAR-файлы, которые нужно установить, должны храниться в хранилище BLOB-объектов Azure в **одном контейнере**.

* Учетная запись хранилища, содержащая библиотеку JAR-файлов, **должна** быть связана с кластером HDInsight во время создания. Это должна быть либо учетная запись хранения по умолчанию, либо учетная запись, добавленная через __настройки учетной записи хранения.__

* Путь WASB к контейнеру необходимо указать в виде параметра для действия сценария. Например, если JAR-файлы хранятся в контейнере **libs** в учетной записи хранения **mystorage**, то параметром будет `wasbs://libs@mystorage.blob.core.windows.net/`.

  > [!NOTE]  
  > В этом документе предполагается, что вы уже создали учетную запись хранения и контейнер BLOB-объектов, а также отправили в него файлы.
  >
  > Если учетная запись хранения не создана, создайте ее на [портале Azure](https://portal.azure.com). Затем с помощью служебной программы, например [Azure Storage Explorer](https://storageexplorer.com/), в учетной записи можно создать контейнер и передать в него файлы.

## <a name="create-a-cluster-using-the-script"></a>Создание кластера с помощью сценария

1. Начните подготовку кластера, используя шаги в [кластерах Provision HDInsight на Linux,](hdinsight-hadoop-provision-linux-clusters.md)но не завершите подготовку. Для создания кластера с помощью этого сценария также можно использовать Azure PowerShell или пакет SDK HDInsight для .NET. Дополнительные сведения об использовании этих методов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md). Для портала Azure из вкладки **«Конфигурация и цены»** выберите **действие скрипта**«

1. Для **хранения,** если учетная запись хранения, содержащая библиотеку файлов банки, будет отличаться от учетной записи, используемой для кластера, заполните **Дополнительные учетные записи хранения.**

1. Для **действий по сценарию**, предоставьте следующую информацию:

    |Свойство |Значение |
    |---|---|
    |Тип скрипта|- Custom|
    |name|Библиотеки |
    |URI bash-скрипта|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Типы узлов|Голова, Рабочий|
    |Параметры|введите адрес WASB к контейнеру и учетной записи хранения, содержащий JAR-файлы Например, `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Для Apache Spark 2.1 используйте `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`этот скрипт bash URI: .

1. Продолжите подготовку кластера, как описано в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Как только создание кластера завершается, вы сможете использовать банки, добавленные через `ADD JAR` этот скрипт от Hive без использования оператора.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о работе с Hive см. в статье [Обзор Apache Hive и HiveQL в Azure HDInsight](hadoop/hdinsight-use-hive.md).
