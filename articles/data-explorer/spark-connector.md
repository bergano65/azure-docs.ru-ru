---
title: Соединитель обозреватель данных Azure для Apache Spark можно используйте для перемещения данных между кластерами обозреватель данных Azure и Spark.
description: В этом разделе показано, как перемещать данные между кластерами обозреватель данных Azure и Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441348"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Соединитель обозреватель данных Azure для Apache Spark (Предварительная версия)

[Apache Spark](https://spark.apache.org/) является единой analytics обрабатывать большие объемы данных. Обозреватель данных Azure — это служба аналитики быстрое, полностью управляемое данных для анализа в режиме реального времени на больших объемов данных. 

Соединитель обозреватель данных Azure для Spark реализует данных источника и приемника данных для перемещения данных между кластерами обозреватель данных Azure и Spark используются оба представленных их возможности. При использовании Azure Data Explorer и Apache Spark вы можете выполнять сборку быстрых и масштабируемых приложений, ориентированных на управляемые данными сценарии, такие как машинное обучение, сценарий извлечения, преобразования и загрузки (ETL) и Log Analytics. Запись в обозреватель данных Azure может осуществляться в пакете и режим потоковой передачи.
Чтение с помощью обозревателя данных Azure поддерживает удаление столбцов и включение предиката, что позволяет сократить объем передаваемых данных путем фильтрации данных в обозревателе данных Azure.

Соединитель Spark обозреватель данных Azure — [проект с открытым кодом](https://github.com/Azure/azure-kusto-spark) , которые можно запустить в любом кластере Spark.

> [!NOTE]
> Несмотря на то, что некоторые из приведенных ниже примерах см. [Azure Databricks](https://docs.azuredatabricks.net/) кластера Spark, соединитель Spark обозреватель данных Azure не принимает прямой зависимости от Databricks или любой другой дистрибутив Spark.

## <a name="prerequisites"></a>Технические условия

* [Создайте кластер и базу данных Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal). 
* Создание кластера Spark
* Установить библиотеку соединителя обозреватель данных Azure и библиотек, перечисленных в [зависимости](https://github.com/Azure/azure-kusto-spark#dependencies) следующие [пакета SDK для Java Kusto](/azure/kusto/api/java/kusto-java-client-library) библиотеки:
    * [Клиент данных Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto приема клиента](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Встроенные библиотеки для [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Как создать соединитель Spark

Соединитель Spark может быть создан на основе [источников](https://github.com/Azure/azure-kusto-spark) как описано ниже.

> [!NOTE]
> Этот шаг не является обязательным. Если вы используете готовые библиотеки перейдите [настройки кластера Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Создание необходимых компонентов

* Установленный пакет SDK Java 1.8
* [Maven 3.x](https://maven.apache.org/download.cgi) установлен
* Apache Spark версии 2.4.0 или более поздней версии

> [!TIP]
> версии 2.3.x также поддерживаются, но может потребовать некоторых изменений в файл pom.xml зависимости.

Для приложений Scala и Java, с помощью определения проекта Maven, связать приложение с помощью следующего артефакта (последняя версия может отличаться):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Команды сборки

Для создания JAR-файла и выполнения всех тестов выполните команду:

```
mvn clean package
```

Чтобы создать JAR-файл, выполнить все тесты Чтобы установить JAR-файл в локальный репозиторий Maven.

```
mvn clean install
```

Дополнительные сведения см. в разделе [использования соединителя](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Настройка кластеров Spark

> [!NOTE]
> Рекомендуется использовать последний выпуск соединителя Spark обозреватель данных Azure, выполняя следующие действия:

1. Задайте параметры кластера, в зависимости от кластером Azure Databricks с помощью Spark 2.4 и Scala 2.11 следующие Spark: 

    ![Параметры кластера Databricks](media/spark-connector/databricks-cluster.png)

1. Импорт библиотеки соединитель обозреватель данных Azure:

    ![Библиотека импорта обозреватель данных Azure](media/spark-connector/db-create-library.png)

1. Добавьте дополнительные зависимости:

    ![Добавление зависимостей](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Найти правильный java версии выпуска для каждого выпуска Spark [здесь](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Убедитесь, что все необходимые библиотеки устанавливаются:

    ![Проверьте установленные библиотеки](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Соединитель Spark обозреватель данных Azure позволяет проверять подлинность с помощью Azure Active Directory (Azure AD) с помощью [приложения Azure AD](#azure-ad-application-authentication), [маркер доступа Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [проверки подлинности устройства ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (для нерабочих сценариев), или [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Пользователь должен установить пакет хранилища ключей azure и учетные данные приложения для доступа к ресурсу хранилища ключей.

### <a name="azure-ad-application-authentication"></a>Проверка подлинности приложения Azure AD

Большинство метод проверки подлинности простых и распространенных. Этот метод рекомендуется для использования соединителя Spark обозреватель данных Azure.

|properties  |Описание  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Идентификатор приложения (клиента) с Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Центр проверки подлинности с Azure AD. Идентификатор Azure AD каталог (клиент).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Ключ приложения Azure AD для клиента.     |

### <a name="azure-data-explorer-privileges"></a>Привилегии обозреватель данных Azure

Необходимо предоставить следующие права доступа в кластере обозреватель данных Azure:

* Для чтения (источник данных), Azure AD приложение должно иметь *средство просмотра* права в целевой базе данных, или *администратора* привилегии на целевую таблицу.
* Для записи (приемник данных), Azure AD приложение должно иметь *приема* права в целевой базе данных. Ей также необходимо предоставить *пользователя* права на создание новых таблиц в целевой базе данных. Если целевая таблица уже существует, *администратора* можно настроить права на целевую таблицу.
 
Дополнительные сведения о роли основного обозревателя данных Azure, см. в разделе [авторизации на основе ролей](/azure/kusto/management/access-control/role-based-authorization). Управление ролями безопасности, см. в разделе [управление ролями безопасности](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark в качестве приемника: Запись в обозревателе данных Azure

1. Настройка параметров в качестве приемника:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Запись данных Spark кластера обозреватель данных Azure в качестве пакета:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Записи данных потоковой передачи:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Источник SPARK: Чтение из обозревателя данных Azure

1. При чтении небольших объемов данных, определение запроса данных:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. При чтении больших объемов данных, необходимо указать хранилище временных больших двоичных объектов. Укажите ключ SAS контейнера хранилища, или имя учетной записи хранения, ключ учетной записи и имя контейнера. Это действие необходимо только необходимые для текущей предварительной версии соединителя Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    В приведенном выше примере мы не доступ к хранилищу ключей, с помощью интерфейса соединителя. Кроме того мы используем более простой метод, с помощью Databricks секреты.

1. Чтение из обозревателя данных Azure:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
