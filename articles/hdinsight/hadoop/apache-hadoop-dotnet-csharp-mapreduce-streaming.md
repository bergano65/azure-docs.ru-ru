---
title: Использование C# с MapReduce в Hadoop в HDInsight в Azure
description: Узнайте, как использовать C# для создания решений MapReduce, использующих Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: hrasheed
ms.openlocfilehash: 1cdf029d296bd6ff11b6531cd47dc6a7fd3163c3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930264"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Использование C# для потоковой передачи MapReduce в Apache Hadoop в HDInsight

Узнайте, как использовать C# для создания решения MapReduce в HDInsight.

> [!IMPORTANT]
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. Дополнительные сведения см. [в статье Apache Hadoop компонентов в HDInsight](../hdinsight-component-versioning.md).

Потоковая передача Apache Hadoop выполняется служебной программой, которая позволяет запускать задания MapReduce с помощью скрипта или исполняемого файла. В этом примере .NET используется для реализации модулей сопоставления и редукции для решения для подсчета слов.

## <a name="net-on-hdinsight"></a>.NET в HDInsight

В кластерах *HDInsight под управлением Linux* для запуска приложений .NET используется [Mono (https://mono-project.com)](https://mono-project.com). Mono версии 4.2.1 входит в состав HDInsight версии 3.6. Дополнительные сведения о версии Mono, включенной в HDInsight, см. в разделе [Apache Hadoop компоненты, доступные в разных версиях hdinsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions). 

Дополнительные сведения о совместимости Mono с различными версиями платформы .NET Framework см. в разделе [Compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Совместимость).

## <a name="how-hadoop-streaming-works"></a>Как работает потоковая передача Hadoop

Базовый процесс потоковой передачи в данном документе выглядит следующим образом.

1. Hadoop передает данные в средство сопоставления (в этом примере это средство*сопоставления. exe* ) на stdin.
2. Модуль сопоставления обрабатывает эти данные и передает пары "ключ-значение", разделенные знаками табуляции, в канал STDOUT.
3. Выходные данные считываются Hadoop, а затем передаются в reduce (в этом примере это*reduce. exe* ) на stdin.
4. Модуль редукции считывает пары "ключ-значение", разделенные знаками табуляции, обрабатывает данные и передает результат в виде пар "ключ-значение", разделенные знаками табуляции, в канал STDOUT.
5. Выходные данные считываются Hadoop и записываются в выходной каталог.

Дополнительные сведения см. в разделе [Потоковая передача Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>предварительным требованиям

* приведенному.

* Опыт написания и выполнения сборки кода C#, предназначенного для платформы .NET Framework 4.5.

* Способ передачи EXE-файлов в кластер. В этом документе для передачи файлов в основное хранилище кластера используются средства Data Lake для Visual Studio.

* Azure PowerShell или Secure Shell (SSH).

* Hadoop в кластере HDInsight. Дополнительные сведения о создании кластера см. в статье [Создание кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

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

2. Разверните пункт **Azure**, а затем — **HDInsight**.

3. При появлении запроса введите учетные данные подписки Azure и нажмите кнопку **войти**.

4. Разверните кластер HDInsight, в который нужно развернуть это приложение. Отобразится запись с текстом **(Учетная запись хранения по умолчанию)** .

   ![Учетная запись хранения, кластер HDInsight, обозреватель сервера, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Если вы можете расширить запись **(учетную запись хранения по умолчанию)** , вы используете **учетную запись хранения Azure** в качестве хранилища по умолчанию для кластера. Чтобы просмотреть файлы в хранилище по умолчанию для кластера, разверните запись и дважды щелкните элемент **(контейнер по умолчанию)** .

   * Если запись **(учетная запись хранения по умолчанию)** не может быть расширена, вы используете **Azure Data Lake Storage** в качестве хранилища по умолчанию для кластера. Чтобы просмотреть файлы в хранилище по умолчанию кластера, дважды щелкните запись **(Контейнер по умолчанию)** .

5. Чтобы передать EXE-файлы, используйте один из следующих методов.

    * Если вы используете **учетную запись хранения Azure**, щелкните значок **Отправить BLOB-объект** . 

        ![Значок отправки HDInsight для модуля сопоставления, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        В диалоговом окне **Отправка нового файла** в поле **имя файла**выберите **Обзор**. В диалоговом окне **Отправка большого двоичного объекта** перейдите в папку *bin\Debug* проекта модуля *сопоставления* , а затем выберите файл *сопоставителя. exe* . Наконец, выберите **Открыть** , а затем нажмите **кнопку ОК** , чтобы завершить отправку. 

    * Для **Azure Data Lake Storage**щелкните правой кнопкой мыши пустую область в списке файлов и выберите **Отправить**. Наконец, выберите файл модуля *сопоставления. exe* и нажмите кнопку **Открыть**.

    После завершения передачи *mapper.exe* повторите этот процесс для передачи файла *reducer.exe*.

## <a name="run-a-job-using-an-ssh-session"></a>Выполнение задания с помощью сеанса SSH

Следующая процедура описывает выполнение задания MapReduce с помощью сеанса SSH.

1. Подключитесь к кластеру HDInsight с помощью SSH. (Например, выполните команду `ssh sshuser@<clustername>-ssh.azurehdinsight.net`.) Дополнительные сведения см. [в статье Использование SSH с HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Используйте одну из приведенных команд для запуска задания MapReduce.

   * Если хранилищем по умолчанию является служба **хранилища Azure**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasb:///mapper.exe,wasb:///reducer.exe \
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
   * `-files`: указывает файлы модуля *сопоставления. exe* и средства *сокращения. exe* для этого задания. `wasb:///`, `adl:///`или `abfs:///` объявления протокола перед каждым файлом — это путь к корню хранилища по умолчанию для кластера.
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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об использовании MapReduce с HDInsight см. [в статье Использование MapReduce в Apache Hadoop в hdinsight](hdinsight-use-mapreduce.md).

Сведения об использовании языка C# с Hive и Pig см. в статье [Использование определяемых пользователем функций C# при потоковой передаче Apache Hive и Apache Pig в Apache Hadoop в HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Сведения об использовании языка C# со Storm в HDInsight см. в разделе [Разработка топологий для Apache Storm на C# с помощью средств Data Lake для Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
