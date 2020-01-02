---
title: Использование C# с MapReduce в Hadoop в HDInsight в Azure
description: Узнайте, как использовать C# для создания решений MapReduce, использующих Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 025b5c5c1e3b8543111e112202906ef6f1fdb482
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561801"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Использование C# для потоковой передачи MapReduce в Apache Hadoop в HDInsight

Узнайте, как использовать C# для создания решения MapReduce в HDInsight.

Потоковая передача Apache Hadoop выполняется служебной программой, которая позволяет запускать задания MapReduce с помощью скрипта или исполняемого файла. В этом примере .NET используется для реализации модулей сопоставления и редукции для решения для подсчета слов.

## <a name="net-on-hdinsight"></a>.NET в HDInsight

Кластеры HDInsight используют [Mono (https://mono-project.com)](https://mono-project.com) для запуска приложений .NET. Mono версии 4.2.1 входит в состав HDInsight версии 3.6. Дополнительные сведения о версии Mono, включенной в HDInsight, см. в разделе [Apache Hadoop компоненты, доступные в разных версиях hdinsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).

Дополнительные сведения о совместимости Mono с различными версиями платформы .NET Framework см. в разделе [Compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Совместимость).

## <a name="how-hadoop-streaming-works"></a>Как работает потоковая передача Hadoop

Базовый процесс потоковой передачи в данном документе выглядит следующим образом.

1. Hadoop передает данные в средство сопоставления (в этом примере это средство*сопоставления. exe* ) на stdin.
2. Модуль сопоставления обрабатывает эти данные и передает пары "ключ-значение", разделенные знаками табуляции, в канал STDOUT.
3. Выходные данные считываются Hadoop, а затем передаются в reduce (в этом примере это*reduce. exe* ) на stdin.
4. Модуль редукции считывает пары "ключ-значение", разделенные знаками табуляции, обрабатывает данные и передает результат в виде пар "ключ-значение", разделенные знаками табуляции, в канал STDOUT.
5. Выходные данные считываются Hadoop и записываются в выходной каталог.

Дополнительные сведения см. в разделе [Потоковая передача Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Технические условия

* приведенному.

* Опыт написания и выполнения сборки кода C#, предназначенного для платформы .NET Framework 4.5.

* Способ передачи EXE-файлов в кластер. В этом документе для передачи файлов в основное хранилище кластера используются средства Data Lake для Visual Studio.

* При использовании PowerShell вам потребуется [модуль AZ](https://docs.microsoft.com/powershell/azure/overview).

* SSH-клиент (необязательно). Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Кластер Apache Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* [Схема универсального кода ресурса (URI)](../hdinsight-hadoop-linux-information.md#URI-and-scheme) для основного хранилища кластеров. Для службы хранилища Azure это будет `wasb://`, для Azure Data Lake Storage 2-го поколения — `abfs://` или `adl://` для Azure Data Lake Storage 1-го поколения. Если для службы хранилища Azure или Azure Data Lake Storage 2-го поколения включена безопасная передача, URI будет `wasbs://` или `abfss://` соответственно (см. также сведения о [безопасной передаче](../../storage/common/storage-require-secure-transfer.md)).


## <a name="create-the-mapper"></a>Создание модуля сопоставления

В Visual Studio создайте новое .NET Framework консольное приложение с именем *сопоставитель*. Используйте для него следующий код.

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

После создания приложения создайте его для создания файла */бин/дебуг/маппер.ЕКСЕ* в каталоге проекта.

## <a name="create-the-reducer"></a>Создание модуля редукции

В Visual Studio создайте новое .NET Framework консольное приложение с именем *reduce*. Используйте для него следующий код.

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

После создания приложения создайте его для создания файла */бин/дебуг/редуцер.ЕКСЕ* в каталоге проекта.

## <a name="upload-to-storage"></a>Отправка в хранилище

Далее необходимо передать приложения модуля *сопоставления* и программы *сокращения* в хранилище HDInsight.

1. В Visual Studio выберите **View** > **Обозреватель сервера**.

1. Щелкните правой кнопкой мыши **Azure**, выберите **подключиться к Microsoft Azure подписке...** и завершите вход.

1. Разверните кластер HDInsight, в который нужно развернуть это приложение. Отобразится запись с текстом **(Учетная запись хранения по умолчанию)** .

   ![Учетная запись хранения, кластер HDInsight, обозреватель сервера, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Если вы можете расширить запись **(учетную запись хранения по умолчанию)** , вы используете **учетную запись хранения Azure** в качестве хранилища по умолчанию для кластера. Чтобы просмотреть файлы в хранилище по умолчанию для кластера, разверните запись и дважды щелкните элемент **(контейнер по умолчанию)** .

   * Если запись **(учетная запись хранения по умолчанию)** не может быть расширена, вы используете **Azure Data Lake Storage** в качестве хранилища по умолчанию для кластера. Чтобы просмотреть файлы в хранилище по умолчанию кластера, дважды щелкните запись **(Контейнер по умолчанию)** .

1. Чтобы передать EXE-файлы, используйте один из следующих методов.

    * Если вы используете **учетную запись хранения Azure**, щелкните значок **Отправить BLOB-объект** .

        ![Значок отправки HDInsight для модуля сопоставления, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        В диалоговом окне **Отправка нового файла** в поле **имя файла**выберите **Обзор**. В диалоговом окне **Отправка большого двоичного объекта** перейдите в папку *bin\Debug* проекта модуля *сопоставления* , а затем выберите файл *сопоставителя. exe* . Наконец, выберите **Открыть** , а затем нажмите **кнопку ОК** , чтобы завершить отправку.

    * Для **Azure Data Lake Storage**щелкните правой кнопкой мыши пустую область в списке файлов и выберите **Отправить**. Наконец, выберите файл модуля *сопоставления. exe* и нажмите кнопку **Открыть**.

    После завершения передачи *mapper.exe* повторите этот процесс для передачи файла *reducer.exe*.

## <a name="run-a-job-using-an-ssh-session"></a>Выполнение задания с помощью сеанса SSH

Следующая процедура описывает выполнение задания MapReduce с помощью сеанса SSH.

1. Используйте [команду SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Измените приведенную ниже команду, заменив ИМЯ_КЛАСТЕРА именем кластера, а затем введите следующую команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Используйте одну из приведенных команд для запуска задания MapReduce.

   * Если хранилищем по умолчанию является служба **хранилища Azure**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Если хранилищем по умолчанию является **Data Lake Storage 1-го поколения**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Если хранилищем по умолчанию является **Data Lake Storage 2-го поколения**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   В следующем списке описывается, что представляет каждый параметр и параметр.

   * *хадуп-стреаминг. jar*— указывает JAR-файл, содержащий функциональность потоковой передачи MapReduce.
   * `-files`: указывает файлы модуля *сопоставления. exe* и средства *сокращения. exe* для этого задания. `wasbs:///`, `adl:///`или `abfs:///` объявления протокола перед каждым файлом — это путь к корню хранилища по умолчанию для кластера.
   * `-mapper`: указывает файл, который реализует модуль сопоставления.
   * `-reducer`: указывает файл, который реализует класс reduce.
   * `-input`: задает входные данные.
   * `-output`: Указывает выходной каталог.

3. После завершения задания MapReduce используйте следующую команду, чтобы просмотреть результаты.

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Ниже приведен пример данных, возвращаемых этой командой.

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Выполнение задания с помощью PowerShell

Используйте приведенный ниже сценарий PowerShell для запуска задания MapReduce и скачивания результатов.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Этот сценарий предлагает ввести имя учетной записи для входа и пароль для кластера, а также имя кластера HDInsight. После завершения задания выходные данные загружаются в файл с именем *Output. txt*. Ниже приведен пример выходных данных в файле `output.txt`.

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании MapReduce с HDInsight см. [в статье Использование MapReduce в Apache Hadoop в hdinsight](hdinsight-use-mapreduce.md).

Сведения об использовании языка C# с Hive и Pig см. в статье [Использование определяемых пользователем функций C# при потоковой передаче Apache Hive и Apache Pig в Apache Hadoop в HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Сведения об использовании языка C# со Storm в HDInsight см. в разделе [Разработка топологий для Apache Storm на C# с помощью средств Data Lake для Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
