---
title: Использование C# с MapReduce в Hadoop в HDInsight в Azure
description: Узнайте, как использовать C# для создания решений MapReduce, использующих Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 7a299ce16f6e9c7292cebf198c9c3077f8e05fcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417606"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Использование C# для потоковой передачи MapReduce в Apache Hadoop в HDInsight

Узнайте, как использовать C# для создания решения MapReduce в HDInsight.

Потоковая передача Apache Hadoop позволяет выполнять задания MapReduce с помощью сценария или исполняемой. Здесь .NET используется для реализации картографа и редуктора для решения подсчета слов.

## <a name="net-on-hdinsight"></a>.NET в HDInsight

Кластеры HDInsight используют [Mono (дляhttps://mono-project.com) ](https://mono-project.com) запуска приложений .NET. Mono версии 4.2.1 входит в состав HDInsight версии 3.6. Для получения дополнительной информации о версии Mono включены с HDInsight, [см.](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

Дополнительные сведения о совместимости Mono с различными версиями платформы .NET Framework см. в разделе [Compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Совместимость).

## <a name="how-hadoop-streaming-works"></a>Как работает потоковая передача Hadoop

Базовый процесс потоковой передачи в данном документе выглядит следующим образом.

1. Hadoop передает данные на картограф *(mapper.exe* в данном примере) на STDIN.
2. Модуль сопоставления обрабатывает эти данные и передает пары "ключ-значение", разделенные знаками табуляции, в канал STDOUT.
3. Выход читается Hadoop, а затем передается редуктору *(reducer.exe* в данном примере) на STDIN.
4. Модуль редукции считывает пары "ключ-значение", разделенные знаками табуляции, обрабатывает данные и передает результат в виде пар "ключ-значение", разделенные знаками табуляции, в канал STDOUT.
5. Выходные данные считываются Hadoop и записываются в выходной каталог.

Дополнительные сведения см. в разделе [Потоковая передача Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Предварительные требования

* приведенному.

* Опыт написания и выполнения сборки кода C#, предназначенного для платформы .NET Framework 4.5.

* Способ передачи EXE-файлов в кластер. В этом документе для передачи файлов в основное хранилище кластера используются средства Data Lake для Visual Studio.

* При использовании PowerShell вам понадобится [модуль Az.](https://docs.microsoft.com/powershell/azure/overview)

* Кластер Apache Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* [Схема универсального кода ресурса (URI)](../hdinsight-hadoop-linux-information.md#URI-and-scheme) для основного хранилища кластеров. Эта схема `wasb://` будет для хранения `abfs://` Azure, для хранения `adl://` данных Azure Data Lake Gen2 или для хранилища озер Azure Gen1. Если для службы хранилища Azure или Azure Data Lake Storage 2-го поколения включена безопасная передача, URI будет `wasbs://` или `abfss://` соответственно (см. также сведения о [безопасной передаче](../../storage/common/storage-require-secure-transfer.md)).

## <a name="create-the-mapper"></a>Создание модуля сопоставления

В Visual Studio создайте новое приложение консоли .NET Framework под названием *mapper.* Используйте для него следующий код.

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

После создания приложения создайте его для создания файла */bin/Debug/mapper.exe* в каталоге проекта.

## <a name="create-the-reducer"></a>Создание модуля редукции

В Visual Studio создайте новое консольное приложение .NET Framework под названием *reducer.* Используйте для него следующий код.

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

После создания приложения создайте его для создания файла */bin/Debug/reducer.exe* в каталоге проекта.

## <a name="upload-to-storage"></a>Отправка в хранилище

Далее необходимо загрузить *приложения для картографа* и *редуктора* в хранилище HDInsight.

1. В Visual Studio выберите **View** > **Server Explorer**.

1. Нажмите **кнопку Azure,** выберите **Подключитесь к подписке Microsoft Azure...**

1. Разверните кластер HDInsight, в который нужно развернуть это приложение. Отобразится запись с текстом **(Учетная запись хранения по умолчанию)**.

   ![Учетная запись хранения, кластер HDInsight, Исследователь серверов, Визуальная студия](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Если запись **(учетная запись хранилища по умолчанию)** может быть расширена, вы используете **учетную запись хранения Azure** в качестве хранилища по умолчанию для кластера. Чтобы просмотреть файлы на хранилище по умолчанию для кластера, расширьте вход, а затем дважды щелкните **(контейнер по умолчанию)**.

   * Если запись **(учетная запись хранилища по умолчанию)** не может быть расширена, вы используете **хранилище озер данных Azure data** в качестве хранилища по умолчанию для кластера. Чтобы просмотреть файлы в хранилище по умолчанию кластера, дважды щелкните запись **(Контейнер по умолчанию)**.

1. Чтобы передать EXE-файлы, используйте один из следующих методов.

    * Если вы используете **учетную запись хранения Azure,** выберите значок **Upload Blob.**

        ![HDInsight загрузить значок для картографа, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        В поле диалога **Upload New File** под **именем файла**выберите **Browse**. В диалоговом окне **Upload Blob** перейдите в папку *с бин-облагом* для проекта *картографа,* а затем выберите файл *mapper.exe.* Наконец, выберите **Открытый,** а затем **OK** для завершения загрузки.

    * Для **хранения озер azure Data,** справа щелкните пустую область в списке файлов, а затем выберите **Upload.** Наконец, выберите файл *mapper.exe,* а затем выберите **Open**.

    После завершения передачи *mapper.exe* повторите этот процесс для передачи файла *reducer.exe*.

## <a name="run-a-job-using-an-ssh-session"></a>Выполнение задания с помощью сеанса SSH

Следующая процедура описывает, как выполнить задание MapReduce с помощью сеанса SSH:

1. Используйте [команду ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Отоверьте приведенную ниже команду, заменив CLUSTERNAME на имя кластера, а затем введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Используйте одну из приведенных команд для запуска задания MapReduce.

   * Если хранилище по умолчанию является **хранилищем Azure:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Если хранилищем по умолчанию является **хранилище Data Lake Storage Gen1:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Если хранилищем по умолчанию является **хранилище Data Lake Storage Gen2:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   В следующем списке описывается, что представляет собой каждый параметр и опцион:

   |Параметр | Описание |
   |---|---|
   |hadoop-streaming.jar|Определяет файл банки, содержащий функцию потоковой передачи MapReduce.|
   |-файлы|Специфика *mapper.exe* и *reducer.exe* файлов для этой работы. Объявление, `wasbs:///`или `abfs:///` объявление протокола перед каждым файлом — это путь к корню хранилища по умолчанию для кластера. `adl:///`|
   |-картограф|Определяет файл, реализуемый картографом.|
   |-Редуктор|Определяет файл, реализуемый в редукторе.|
   |-Ввод|Определяет входные данные.|
   |-выход|Определяет каталог вывода.|

1. Как только задание MapReduce завершится, используйте следующую команду для просмотра результатов:

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

Этот сценарий предлагает ввести имя учетной записи для входа и пароль для кластера, а также имя кластера HDInsight. Как только задание завершается, вывод загружается в файл под названием *output.txt.* Ниже приведен пример выходных данных в файле `output.txt`.

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

Для получения дополнительной информации об использовании MapReduce с HDInsight, [см.](hdinsight-use-mapreduce.md)

Сведения об использовании языка C# с Hive и Pig см. в статье [Использование определяемых пользователем функций C# при потоковой передаче Apache Hive и Apache Pig в Apache Hadoop в HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Сведения об использовании языка C# со Storm в HDInsight см. в разделе [Разработка топологий для Apache Storm на C# с помощью средств Data Lake для Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
