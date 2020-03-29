---
title: C, Apache Hive & Apache Pig на Apache Hadoop - Azure HDInsight
description: Узнайте, как использовать определяемые пользователем функции C# при потоковой передаче Apache Hive и Apache Pig в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74949395"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Используйте функции, определяемые пользователем с Apache Hive и Apache Pig на Apache Hadoop в HDInsight

Узнайте, как использовать функции, определяемые пользователями (UDF) с [Apache Hive](https://hive.apache.org) и [Apache Pig](https://pig.apache.org) на HDInsight.

> [!IMPORTANT]
> Шаги в этом документе работают с кластерами HDInsight на основе Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)

Hive и Pig могут передавать данные во внешние приложения для обработки. Этот процесс называется _потоковой передачей_. При использовании приложения .NET данные передаются в него через поток STDIN, после чего приложение возвращает результаты через поток STDOUT. Для чтения и записи с помощью потоков STDIN и STDOUT можно использовать `Console.ReadLine()` и `Console.WriteLine()` из консольного приложения.

## <a name="prerequisites"></a>Предварительные требования

* Опыт написания и выполнения сборки кода C#, предназначенного для платформы .NET Framework 4.5.

    Используйте любую интегрированную среду разработки. Мы рекомендуем [Visual Studio](https://www.visualstudio.com/vs) или Visual [Studio Code.](https://code.visualstudio.com/) Шаги в этом документе используют Visual Studio 2019.

* Способ передачи EXE-файлов в кластер и выполнения заданий Pig и Hive. Мы рекомендуем [Data Lake Tools для визуальной студии,](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md) [Azure PowerShell](/powershell/azure)и [Azure CLI.](/cli/azure/install-azure-cli?view=azure-cli-latest) В этом документе для передачи файлов и выполнения примера запроса Hive используются средства Data Lake для Visual Studio.

    Для получения информации о других способах запуска запросов Hive, [см.](hdinsight-use-hive.md)

* Hadoop в кластере HDInsight. Для получения дополнительной [информации](../hdinsight-hadoop-provision-linux-clusters.md)о создании кластера см.

## <a name="net-on-hdinsight"></a>.NET в HDInsight

В кластерах *HDInsight под управлением Linux* для запуска приложений .NET используется [Mono (https://mono-project.com)](https://mono-project.com). Mono версии 4.2.1 входит в состав HDInsight версии 3.6.

Дополнительные сведения о совместимости Mono с различными версиями платформы .NET Framework см. в разделе [Compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Совместимость).

Для получения дополнительной информации о версии .NET Framework и [HDInsight component versions](../hdinsight-component-versioning.md)Mono, включенных в версии HDInsight, см.

## <a name="create-the-c-projects"></a>Создание проектов C\#

В следующих разделах описывается, как создать проект c- в Visual Studio для UDF Apache Hive и UDF Apache Pig.

### <a name="apache-hive-udf"></a>Определяемая пользователем функция Apache Hive

Для создания проекта на C-е для UDF Apache Hive:

1. Запустите Visual Studio.

2. Выберите **Создать новый проект**.

3. В новом окне **проекта** выберите шаблон **Console App (.NET Framework)** (версия C'). Затем выберите **Следующий**.

4. В новом окне **проекта** введите **название проекта** *HiveCSharp*и перейдите к **местоположению** или создайте для сохранения нового проекта. Затем выберите **Создать**.

5. В Visual Studio IDE замените содержимое *Program.cs* следующим кодом:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. Из панели меню выберите решение **Build** > **Build для** построения проекта.

7. Закройте решение.

### <a name="apache-pig-udf"></a>Определяемая пользователем функция Apache Pig

Для создания проекта на C-е для UDF Apache Hive:

1. Запустите Visual Studio.

2. В окне **Начало работы** выберите **Создать проект**.

3. В новом окне **проекта** выберите шаблон **Console App (.NET Framework)** (версия C'). Затем выберите **Следующий**.

4. В новом окне **проекта** введите **название проекта** *PigUDF*и перейдите на **место,** чтобы сохранить новый проект. Затем выберите **Создать**.

5. В Visual Studio IDE замените содержимое *Program.cs* следующим кодом:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Этот код анализирует строки, отправленные из Pig, и переформатирует строки, которые начинаются с `java.lang.Exception`.

6. Из панели меню выберите решение **Build** > **Build для** построения проекта.

7. Оставьте решение открытым.

## <a name="upload-to-storage"></a>Отправка в хранилище

Затем загрузите приложения Hive и Pig UDF для хранения в кластере HDInsight.

1. В Visual Studio перейдите к **View** > **Server Explorer**.

1. От **Server Explorer**, нажмите правой кнопкой мыши **Azure**, выберите **Подключитесь к подписке Microsoft Azure**и завершите процесс вхинга.

1. Разверните кластер HDInsight, в который нужно развернуть это приложение. Отобразится запись с текстом **(Учетная запись хранения по умолчанию)**.

    ![Учетная запись хранения по умолчанию, кластер HDInsight, Исследователь серверов](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Если эта запись может быть расширена, вы используете **учетную запись хранения Azure** в качестве хранилища по умолчанию для кластера. Чтобы просмотреть файлы в хранилище по умолчанию кластера, разверните эту запись, а затем дважды щелкните запись **(Контейнер по умолчанию)**.

    * Если эта запись не может быть расширена, вы используете **хранилище озер данных Azure** в качестве хранилища по умолчанию для кластера. Чтобы просмотреть файлы в хранилище по умолчанию кластера, дважды щелкните запись **(Контейнер по умолчанию)**.

1. Чтобы передать EXE-файлы, используйте один из следующих методов.

    * Если вы используете **учетную запись хранения Azure,** выберите значок **Upload Blob.**

        ![HDInsight загрузить значок для нового проекта](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        В поле диалога **Upload New File** под **именем файла**выберите **Browse**. В диалоговом окне **Upload Blob** перейдите в папку *для* проекта *HiveCSharp,* а затем выберите файл *HiveCSharp.exe.* Наконец, выберите **Открытый,** а затем **OK** для завершения загрузки.

    * Если вы используете **Хранилище озер данных Azure,** нажмите вправо на пустую область в объявлении, а затем выберите **Upload.** Наконец, выберите файл *HiveCSharp.exe* и выберите **Open.**

    После завершения передачи *HiveCSharp.exe* повторите этот процесс для передачи файла *PigUDF.exe*.

## <a name="run-an-apache-hive-query"></a>Выполнение запроса Apache Hive

Теперь вы можете запустить запрос Hive, который использует ваше приложение Hive UDF.

1. В Visual Studio перейдите к **View** > **Server Explorer**.

2. Разверните пункт **Azure**, а затем — **HDInsight**.

3. Щелкните правой кнопкой мыши кластер, в котором развернуто приложение *HiveCSharp*, а затем выберите **Написать запрос Hive**.

4. В качестве запроса Hive используйте следующий текст:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Раскомментируйте инструкцию `add file`, которая соответствует типу хранилища по умолчанию для кластера.

    Этот запрос выбирает `clientid`, `devicemake`и `devicemodel` поля `hivesampletable`из , а затем он передает поля в приложение *HiveCSharp.exe.* В результате запроса приложение должно возвратить три поля, которые хранятся под именами `clientid`, `phoneLabel` и `phoneHash`. Запрос также предполагает найти *HiveCSharp.exe* в корне контейнера для хранения данных по умолчанию.

5. Переключите **интерактивный** по умолчанию в **пакет,** а затем выберите **отправить** задание в кластер HDInsight. Откроется окно **Сводка по заданию Hive**.

6. Выберите **обновление** резюме до тех пор, пока **статус работы** не изменится в **Завершено.** Для просмотра вывода задания выберите **выход задания.**

## <a name="run-an-apache-pig-job"></a>Выполнение задания Apache Pig

Вы также можете запустить работу Свинья, которая использует ваш Свинья UDF приложения.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH. (Например, запустить `ssh sshuser@<clustername>-ssh.azurehdinsight.net`команду .) Для получения дополнительной информации [см.](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Используйте следующую команду для запуска командной строки Свиньи:

    ```shell
    pig
    ```

    Откроется командная строка `grunt>`.

3. Чтобы запустить задание Pig, использующее приложение .NET Framework, введите следующую команду.

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    Заявление `DEFINE` создает псевдоним `streamer` приложения *PigUDF.exe* и `CACHE` загружает его из хранилища по умолчанию для кластера. Позже `streamer` используется с `STREAM` оператором для обработки `LOG` отдельных строк, содержащихся в и возвращения данных в виде ряда столбцов.

    > [!NOTE]
    > Имя приложения, используемое для потоковой передачи, должно быть окружено \` символом (backtick) при псевдониме, и символом ' (одна цитата) при использовании с `SHIP`.

4. После ввода последней строки запустится задание. Появится результат, аналогичный приведенному ниже тексту.

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Используйте `exit` для выхода свиньи.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как использовать приложение .NET Framework от Hive and Pig на HDInsight. Чтобы узнать об использовании Python с Hive и Pig, ознакомьтесь со статьей [Использование определяемых пользователем функций Python с Apache Hive и Apache Pig в HDInsight](python-udf-hdinsight.md).

Для других способов использования Hive, а также узнать об использовании MapReduce, см.

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md)
* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)
* [Свинья Латинская Основы](https://pig.apache.org/docs/latest/basic.html)