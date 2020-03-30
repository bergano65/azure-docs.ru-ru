---
title: Функция, определяемая пользователем Java (UDF) с Apache Hive Azure HDInsight
description: Узнайте, как создать определяемую пользователем функцию (UDF) на основе Java, которая работает с Apache Hive. В этом примере определяемая пользователем функция преобразует таблицу текстовых строк в нижний регистр.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327209"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Использование определяемых пользователем функций Java с Apache Hive в HDInsight

Узнайте, как создать определяемую пользователем функцию (UDF) на основе Java, которая работает с Apache Hive. Определяемая пользователем функция Java в этом примере преобразует таблицу текстовых строк в символы нижнего регистра.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Хадуп на HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) версии 8](https://aka.ms/azure-jdks)
* Средство [Apache Maven](https://maven.apache.org/download.cgi), [установленное](https://maven.apache.org/install.html) согласно инструкций Apache.  Maven — система сборки проектов Java.
* [Схема универсального кода ресурса (URI)](../hdinsight-hadoop-linux-information.md#URI-and-scheme) для основного хранилища кластеров. Это будет wasb:// для хранилища Azure, abfs:// для хранения данных Azure Data Lake Data Gen2 или adl:// для хранения azure Data Lake Data Gen1. Если для службы хранилища Azure включено безопасное перемещение, URI будет таким: `wasbs://`.  См. также сведения о [безопасной передаче](../../storage/common/storage-require-secure-transfer.md).

* Текстовый редактор или Java IDE.

    > [!IMPORTANT]  
    > Если вы создаете файлы Python в клиенте Windows, следует использовать редактор, который использует LF в качестве символа конца строки. Если вы не уверены, используется ли в редакторе символ LF или CRLF, см. раздел [Устранение неполадок](#troubleshooting), в котором описано, как удалять символы CR.

## <a name="test-environment"></a>Тестовая среда

Среда, используемая для этой статьи, была компьютером под управлением Windows 10.  Команды выполнялись в командном запросе, а различные файлы были отредактированы с помощью блокнота. Изменяйте соответственно для вашей среды.

Из запроса команд введите команды ниже, чтобы создать рабочую среду:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Создание примера определяемой пользователем функции Java

1. Создайте новый проект Maven, введя следующую команду:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Эта команда создает каталог `exampleudf`с именем , который содержит проект Maven.

2. Как только проект был создан, удалите `exampleudf/src/test` каталог, который был создан в рамках проекта, введя следующую команду:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Откройте, `pom.xml` введя команду ниже:

    ```cmd
    notepad pom.xml
    ```

    Затем замените `<dependencies>` существующую запись на следующую XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    В этих записях указана версия Hadoop и Hive, используемая в кластерах HDInsight 3.6. Сведения о версиях Hadoop и Hive, включенных в HDInsight, можно найти в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md) .

    В конце файла добавьте раздел `<build>` перед строкой `</project>`. Этот раздел должен содержать следующий код XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Эти записи определяют способ построения проекта. В частности, используемую в проекте версию Java и способ построения uberjar для развертывания в кластер.

    После внесения изменений сохраните файл.

4. Введите команду ниже, чтобы `ExampleUDF.java`создать и открыть новый файл:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Затем скопируйте и вставьте код Java ниже в новый файл. Затем закройте файл.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Этот код реализует определяемую пользователем функцию, которая принимает строковое значение и возвращает строку в нижнем регистре.

## <a name="build-and-install-the-udf"></a>Создание и установка определяемой пользователем функции

В приведенных ниже `sshuser` командах замените фактическое имя пользователя, если они отличаются друг от друга. Заменить `mycluster` фактическим названием кластера.

1. Составить и упаковать UDF, введя следующую команду:

    ```cmd
    mvn compile package
    ```

    Эта команда выполняет сборку определяемой пользователем функции и упаковывает ее в файл `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Используйте `scp` команду для копирования файла в кластер HDInsight, введя следующую команду:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Подключитесь к кластеру с помощью SSH, введя следующую команду:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. От открытой сессии SSH скопируйте файл банки на хранение HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Использование определяемой пользователем функции из Hive

1. Запустите клиент Beeline с сеанса SSH, введя следующую команду:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    В этой команде предполагается, что вы использовали для кластера учетную запись входа по умолчанию — **admin** .

2. После появления командной строки `jdbc:hive2://localhost:10001/>` введите следующую команду, чтобы добавить определяемую пользователем функцию в Hive и предоставить ее как функцию.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Используйте определяемую пользователем функцию, чтобы преобразовать значения, полученные из таблицы, в строки нижнего регистра.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Этот запрос выбирает состояние из таблицы, преобразует строку в нижний корпус, а затем отображает их вместе с неизмененным именем. Выходные данные выглядят следующим образом:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Устранение неполадок

При выполнении задания улья, вы можете встретить ошибку, похожую на следующий текст:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Эта проблема может быть вызвана символами окончания строк в файле Python. Многие редакторы Windows по умолчанию используют символы CRLF, но в приложениях Linux обычно ожидается использование символа LF.

Вы можете использовать следующие команды PowerShell для удаления символов CR перед передачей файла в HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Дальнейшие действия

Другие способы работы с Hive в HDInsight см. в [этой статье](hdinsight-use-hive.md).

Дополнительные сведения об определяемых пользователем функциях Hive см. в разделе [Hive Operators and User-Defined Functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Операторы Hive и определяемые пользователем функции) вики-сайта Hive на сайте apache.org.
