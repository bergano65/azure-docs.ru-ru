---
title: Разработка топологий Apache Storm с помощью Visual Studio и C# в Azure HDInsight
description: Сведения о создании топологий Storm в C#. Создайте топологию подсчета слов в Visual Studio с помощью инструментов Hadoop для Visual Studio.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/31/2019
ms.openlocfilehash: 1903c2faab865152d1f3666f3c9dadd745058b56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612297"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Разработка топологий для Apache Storm на C# с помощью средств Data Lake для Visual Studio

Сведения о создании топологии Apache Storm на языке C# с помощью средств Azure Data Lake (Apache Hadoop) для Visual Studio. В этом документе приведены пошаговые инструкции по созданию проекта Storm в Visual Studio, его локальному тестированию и развертыванию в Apache Storm в кластере Azure HDInsight.

Вы также узнаете о том, как создавать гибридные топологии, использующие компоненты C# и Java.

Топологии спомощью .NET 4.5 и моно для запуска в кластере HDInsight. Для получения информации о потенциальных несовместимости [см.](https://www.mono-project.com/docs/about-mono/compatibility/) Чтобы использовать топологию C', `Microsoft.SCP.Net.SDK` необходимо обновить пакет NuGet, используемый проектом, до версии 0.10.0.6 или позже. Версия пакета также должна соответствовать основной версии Storm, установленной на HDInsight.

| Версия HDInsight | Версия Apache Storm | Версия SCP.NET | Версия Mono по умолчанию |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Предварительные требования

Кластер Apache Storm в HDInsight. Ознакомьтесь со статьей [Create Linux-based clusters in HDInsight by using the Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) (Создание кластеров под управлением Linux в HDInsight с помощью портала Azure) и выберите **Storm** для параметра **Тип кластера**.

## <a name="install-visual-studio"></a>Установка Visual Studio

Вы можете разрабатывать топологии с помощью SCP.NET с помощью [Visual Studio.](https://visualstudio.microsoft.com/downloads/) Инструкции здесь использовать Visual Studio 2019, но вы также можете использовать более ранние версии Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Установка средств Data Lake для Visual Studio

Инструкции по установке средств Data Lake для Visual Studio см. в статье [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Установка Java

При отправке топологии Storm из Visual Studio SCP.NET создает ZIP-файл, содержащий топологию и зависимости. Эти ZIP-файлы создаются с помощью Java, так как при этом используется формат, который более совместим с кластерами под управлением Linux.

1. Установите Java Developer Kit (JDK) 7 или более поздней версии в среде разработки. Получить пакет Oracle JDK можно на сайте [Oracle](https://openjdk.java.net/). Вы также можете использовать [другие дистрибутивы Java](/java/azure/jdk/).

2. Установите `JAVA_HOME` переменную среды в каталог, содержащий Java.

3. Установите `PATH` переменную среды, чтобы включить `%JAVA_HOME%\bin` каталог.

Можно создать и запустить следующее приложение консоли СЗ, чтобы убедиться, что Java и JDK правильно установлены:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Шаблоны Apache Storm

Средства Data Lake для Visual Studio предоставляют следующие шаблоны:

| Тип проекта | Что демонстрирует |
| --- | --- |
| Приложение Storm |Пустой проект топологии Storm |
| Пример модуля записи Storm Azure SQL |Запись в базу данных SQL Azure. |
| Пример модуля чтения Storm Azure Cosmos DB |Чтение из базы данных Azure Cosmos DB. |
| Пример модуля записи Storm Azure Cosmos DB |Запись в базу данных Azure Cosmos DB. |
| Пример модуля чтения концентратора событий Storm |Чтение из Центров событий Azure. |
| Пример модуля записи концентратора событий Storm |Запись в Центры событий Azure. |
| Пример модуля чтения Storm HBase |Чтение из HBase в кластерах HDInsight. |
| Пример модуля записи Storm HBase |Запись в HBase в кластерах HDInsight. |
| Пример Storm Hybrid |Использование компонентов Java. |
| Пример Storm |Базовая топология подсчета слов. |

> [!WARNING]  
> Не все шаблоны работают с HDInsight под управлением Linux. Пакеты NuGet, используемые в шаблонах, могут оказаться несовместимыми с Mono. Чтобы выявить потенциальные проблемы, см. [Mono совместимости](https://www.mono-project.com/docs/about-mono/compatibility/) и использовать [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

На данном этапе в этом документе используется базовый тип проекта приложения Storm для создания топологии.

### <a name="apache-hbase-templates"></a>Шаблоны Apache HBase

Для взаимодействия с HBase в кластере HDInsight шаблоны модулей чтения и записи HBase используют REST API HBase, а не API Java HBase.

### <a name="eventhub-templates"></a>Шаблоны EventHub

> [!IMPORTANT]  
> Компонент spout EventHub на основе Java, входящий в состав шаблона EventHub Reader, не работает со Storm в HDInsight версии 3.5 или более поздней. Обновленная версия этого компонента доступна в [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Пример топологии, которая использует этот компонент и работает со Storm в HDInsight 3.5, см. в [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Создание топологии на C#

Для создания топологического проекта в Visual Studio:

1. Запустите Visual Studio.

1. В окне **Начало работы** выберите **Создать проект**.

1. В **создайте новое** окно проекта, прокрутите и выберите **Приложение Storm,** затем выберите **Следующее.**

1. В новом окне **проекта введите** **название проекта** *WordCount,* перейдите или создайте путь каталога **местоположения** для проекта, а затем выберите **«Создать».**

    ![Storm приложение, Наконкажируйте свой новый диалоговой ящик проекта, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

После создания проекта следует иметь следующие файлы:

* *Program.cs*: Определение топологии для вашего проекта. Обратите внимание, что по умолчанию создается топология, состоящая из одного объекта spout и одного объекта bolt.

* *Spout.cs*— пример воронки, выдающей случайные числа.

* *Bolt.cs*— пример сита, которое подсчитывает числа, созданные воронкой.

При создании проекта NuGet скачивает последнюю версию [пакета SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

### <a name="implement-the-spout"></a>Реализация воронки

Затем добавьте код для носика, который используется для чтения данных в топологии из внешнего источника. Эта воронка случайным образом отправляет предложения в топологию.

1. Откройте файл *Spout.cs*. Ниже перечислены основные компоненты воронки.

   * `NextTuple`: Вызывается Storm, когда носик разрешено излучать новые tuples.

   * `Ack`(только транзакционная топология): Обработки подтверждений, начатых другими компонентами в топологии для tuples, отправленных из носика. Подтверждение кортежа позволяет воронке определить, что он был успешно обработан нижестоящими компонентами.

   * `Fail`(только транзакционная топология): Ручки подделок, которые не обрабатывают другие компоненты в топологии. Реализация метода `Fail` позволяет повторно излучать тулуп, чтобы его можно было обработать заново.

2. Замените содержимое `Spout` класса следующим текстом:

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Реализация сит

Теперь создайте два болта Storm в этом примере:

1. Удалите существующий файл *Bolt.cs* из проекта.

2. В **Solution Explorer**, правой кнопкой мыши проекта, и выберите **Добавить** > **новый элемент**. Выберите в списке **Storm Bolt** (Сито воронки) и введите имя *Splitter.cs*. В коде нового файла измените имя `WordCount`пространства имен на . Затем повторите этот процесс, чтобы создать второй болт под названием *Counter.cs.*

   * *Splitter.cs*: Реализует болт, который разделяет предложения на отдельные слова, и излучает новый поток слов.

   * *Counter.cs*: Реализует болт, который считает каждое слово, и излучает новый поток слов и подсчет для каждого слова.

     > [!NOTE]  
     > Хотя эти сита считывают данные и записывают их в потоки, их можно также использовать для взаимодействия с базой данных, службой и т. д.

3. Откройте файл *Splitter.cs*. Он имеет только один `Execute`метод по умолчанию: . Метод `Execute` вызывается, когда болт получает тулдля для обработки. Здесь можно считывать и обрабатывать входящие кортежи и создавать исходящие кортежи.

4. Замените содержимое класса `Splitter` следующим кодом:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Откройте файл *Counter.cs* и замените содержимое класса следующим кодом:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Определение топологии

Потоки данных между компонентами могут быть представлены в виде диаграммы воронок и сит. Граф для этой топологии выглядит следующим образом.

![Диаграмма расположения компонента носика и болта, топология шторма](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Носик излучает предложения, которые распространяются в экземплярах болтА Сплиттера. Сито Splitter разбивает предложения на слова, которые распределяются среди экземпляров сита Counter.

Поскольку экземпляр Counter содержит локальное количество слов, необходимо убедиться, что определенные слова перетекают в тот же экземпляр Counter bolt. Каждое конкретное слово отслеживается только одним экземпляром. Так как сито Splitter не поддерживает состояние, на самом деле неважно, какое предложение получает то или иное сито.

Открыто *Program.cs*. Важным методом `GetTopologyBuilder`является , который используется для определения топологии, которая передается Storm. Замените содержимое `GetTopologyBuilder` следующим кодом для реализации описанной ранее топологии:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Отправка топологии

Теперь вы готовы представить топологию в кластер HDInsight.

1. Перейдите к **просмотру** > **исследователь сервера**.

1. Нажмите **кнопку Azure,** выберите **Подключитесь к подписке Microsoft Azure...**

1. В **Solution Explorer**, правой кнопкой мыши проекта, и выберите Отправить в Storm на **HDInsight**.

1. В диалоговом поле **Submit Topology** под списком выпадающих кластеров **Storm,** выберите Storm в кластере HDInsight, а затем выберите **Отправить.** Вы можете проверить, является ли отправка успешной, просмотрев панель **вывода.**

    Когда топология была успешно представлена, должно появиться окно **Storm Topologies View** для кластера. Выберите топологию **WordCount** из списка для просмотра информации о запущенной топологии.

    ![Окно зрения топологии шторма, кластер HDInsight, Визуальная студия](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > **Топологии Storm** можно также просмотреть в **обозревателе сервера**. Расширьте **Azure** > **HDInsight**, нажмите правой кнопкой мыши Storm на кластере HDInsight, а затем выберите **Просмотр Шторма Topologies.**

    Для просмотра информации о компонентах в топологии выберите компонент в диаграмме.

1. В разделе **Topology Summary** выберите **Kill,** чтобы остановить топологию.

    > [!NOTE]  
    > Топологии Storm выполняются, пока не будут удалены или пока не будет удален кластер.

## <a name="transactional-topology"></a>Транзакционная топология

Предыдущая топология является нетранзакционной. Компоненты топологии не реализуют функциональность для воспроизведения сообщений. Чтобы получить пример транзакционной топологии, создайте проект и выберите в качестве типа проекта **Пример Storm**.

Транзакционные топологии реализуют следующие возможности, обеспечивающие воспроизведение данных:

* **Кэширование метаданных**: Носик должен хранить метаданные о испускаемых данных, так что данные могут быть извлечены и излмиссии снова, если сбой происходит. Так как в этом примере отправляются данные небольшого размера, необработанные данные каждого кортежа хранятся в словаре для воспроизведения.

* **Подтверждение** — каждый элемент bolt в топологии может вызвать `this.ctx.Ack(tuple)`, чтобы подтвердить успешную обработку кортежа. Когда все болты признали tuple, `Ack` метод носик вызывается. Метод `Ack` позволяет воронке удалить кэшированные данные для воспроизведения.

* **Fail** (Сбой) — каждое сито может вызвать `this.ctx.Fail(tuple)`, указав тем самым на сбой при обработке кортежа. Информация о сбое передается в метод `Fail` воронки, в которой кортеж может быть воспроизведен с помощью кэшированных метаданных.

* **Sequence ID**(Идентификатор последовательности) — при отправке кортежа может быть указан уникальный идентификатор последовательности. Это значение определяет tuple для`Ack` воспроизведения (и) `Fail`обработки. Например, носик в проекте **Storm Sample** использует следующий вызов метода при испускании данных:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Этот код излучает tuple, который содержит предложение к потоку `lastSeqId`по умолчанию, с значением идентификатора последовательности, содержащимся в . В этом `lastSeqId` примере, приращены для каждого tuple испускаемого.

Как показано в проекте **Пример Storm**, транзакционность компонента можно задать во время выполнения в соответствии с конфигурацией.

## <a name="hybrid-topology-with-c-and-java"></a>Гибридная топология с помощью C# и Java

Средства Data Lake для Visual Studio можно также использовать для создания гибридных топологий, в которых одни компоненты написаны на C#, а другие — на Java.

Чтобы получить пример гибридной топологии, создайте проект и выберите **Пример гибридного решения Storm**. В примере демонстрируются следующие понятия:

* **Носик Java** и болт `HybridTopology_javaSpout_csharpBolt` **Сз**: Определяется в классе.

  В `HybridTopologyTx_javaSpout_csharpBolt` классе определена транзакционная версия.

* **Носик и** **Java болт** `HybridTopology_csharpSpout_javaBolt` : Определяется в классе.

  В `HybridTopologyTx_csharpSpout_javaBolt` классе определена транзакционная версия.

  > [!NOTE]  
  > В этой версии также демонстрируется использование кода Clojure из текстового файла в качестве компонента Java.

Чтобы изменить топологию, используемую при отправке проекта, перед отправкой в кластер переместите оператор `[Active(true)]` в топологию, которую необходимо использовать.

> [!NOTE]  
> Все необходимые файлы Java предоставляются в составе этого проекта и находятся в папке *JavaDependency* .

Рассмотрим следующее при создании и представлении гибридной топологии:

* Используйте `JavaComponentConstructor` для создания экземпляра класса Java для носика или болта.

* Используйте `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` для сериализации данных в компоненты Java или из нее от java-объектов до JSON.

* Отправляя топологию на сервер, необходимо использовать параметр **Дополнительные конфигурации**, чтобы задать **пути к файлам Java**. Указанный путь должен быть каталогом, в котором есть файлы JAR, содержащие классы Java.

### <a name="azure-event-hubs"></a>Центры событий Azure

В SCP.NET версии 0.9.4.203 появились новые класс и метод, предназначенные специально для работы с компонентом spout концентратора событий (компонентом spout Java, который считывает данные из Центров событий). При создании топологии, используюейой носик концентратора событий (например, с помощью шаблона **Storm EventHub Reader Sample),** используйте следующие AAP:

* `EventHubSpoutConfig`класс: Создает объект, содержащий конфигурацию для компонента носика.

* `TopologyBuilder.SetEventHubSpout`метод: Добавляет компонент носика события концентрологии в топологию.

> [!NOTE]  
> Вы все равно `CustomizedInteropJSONSerializer` должны использовать для сериализации данных, производимых носик.

## <a name="use-configurationmanager"></a>Использование ConfigurationManager

Не используйте **ConfigurationManager** для получения значений конфигурации из компонентов болтови и носика. Это вызовет исключение пустого указателя. Вместо этого передайте конфигурацию для вашего проекта в топологию Storm в качестве ключевой пары и стоимости в контексте топологии. Каждому компоненту, зависящему от значений конфигурации, необходимо получить их из контекста во время инициализации.

В приведенном ниже коде показано, как получить эти значения.

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Если используется метод `Get`, чтобы вернуть экземпляр компонента, он должен передавать конструктору параметры `Context` и `Dictionary<string, Object>`. В примере ниже показан базовый метод `Get`, передающий значения должным образом.

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Обновление SCP.NET

Последние версии SCP.NET поддерживают обновление пакета через NuGet. Если доступно новое обновление, вы получите уведомление об обновлении. Чтобы вручную проверить обновления, выполните указанные ниже действия.

1. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.

2. Из диспетчера пакетов выберите **Обновления**. Если доступно обновление пакета поддержки SCP.NET, оно включено в список. Выберите **обновление** для пакета, а затем в диалоговом поле **Preview Changes,** выберите **OK,** чтобы установить его.

> [!IMPORTANT]  
> Если проект был создан с помощью предыдущей версии SCP.NET, в которой не использовалось средство NuGet, выполните следующие действия для обновления до новой версии.
>
> 1. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.
> 2. Используя поле **поиска,** ищите, `Microsoft.SCP.Net.SDK` а затем добавляйте в проект.

## <a name="troubleshoot-common-issues-with-topologies"></a>Устранение распространенных неполадок с топологиями

### <a name="null-pointer-exceptions"></a>Исключение пустого указателя

При использовании топологии C's с кластером HDInsight на основе Linux, болт и носик компонентов, которые используют **ConfigurationManager** для чтения параметров конфигурации во время выполнения может вернуть исключения нулевой указатель.

Конфигурация проекта передается в топологию Storm как пара "ключ —значение" в контексте топологии. Его можно извлечь из объекта словаря, который передается компонентам при инициализании.

Для получения дополнительной информации смотрите раздел [«Использование ConfigurationManager»](#use-configurationmanager) в этом документе.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Когда вы используете топологию C's с кластером HDInsight на основе Linux, вы можете столкнуться со следующей ошибкой:

`System.TypeLoadException: Failure has occurred while loading a type.`

Эта ошибка возникает, когда вы используете двоичный файл, который не совместим с версией .NET, которую поддерживает Mono.

Для кластеров HDInsight под управлением Linux в проекте должны использоваться двоичные файлы, скомпилированные для .NET 4.5.

### <a name="test-a-topology-locally"></a>Локальная проверка топологии

Хотя в кластере легко развернуть топологию, в некоторых случаях может потребоваться проверить топологию локально. Используйте следующие шаги для запуска и тестирования топологии примера в этой статье локально в среде разработки.

> [!WARNING]  
> Локальное тестирование работает только для базовых топологий на C#. Нельзя использовать локальное тестирование для гибридных топологий или топологий, использующих несколько потоков.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Свойства**. Свойства проекта. Затем измените **тип вывода** на **консольное приложение.**

   ![Приложение HDInsight Storm, свойства проекта, тип вывода](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Перед развертыванием топологии в кластере не забудьте вернуть для параметра **Тип выходных данных** значение **Библиотеки классов**.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект, а затем выберите **Добавить** > **Новый элемент**. Выберите **класс**и введите *LocalTest.cs* как имя класса. Наконец, выберите **Добавить**.

1. Откройте *LocalTest.cs*и `using` добавьте следующее заявление в верхней части:

    ```csharp
    using Microsoft.SCP;
    ```

1. Используйте следующий код в `LocalTest` качестве содержимого класса:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Ознакомьтесь с комментариями в коде. Этот код `LocalContext` используется для запуска компонентов в среде разработки. Он сохраняет поток данных между компонентами и текстовыми файлами на локальном диске.

1. Откройте *Program.cs*и добавьте `Main` следующий код к методу:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. Сохранить изменения, а затем выберите **F5** или выбрать > **отладка Start Debugging** для запуска проекта. **Debug** Должно появится окно консоли, а также данные о состоянии журнала в ходе выполнения тестов. При `Tests finished` появляются, выберите любой ключ, чтобы закрыть окно.

1. Используйте **проводник** для поиска каталога, который содержит проект, (Например: *\\C: Пользователи\\\\\\\\\<your_user_name>\\исходного репо WordCount WordCount*.) Затем в этом каталоге, открыть *Бин*, а затем выбрать *Debug*. Вы должны увидеть текстовые файлы, которые были произведены во время тестов: *sentences.txt*, *counter.txt*и *splitter.txt*. Откройте каждый текстовый файл и проверьте данные.

   > [!NOTE]  
   > Строковые данные сохраняются в этих файлах как массив значений типа decimal. Например, `[[97,103,111]]` в файле **splitter.txt** слово *назад*.

> [!NOTE]  
> Не забудьте вернуть **тип проекта** в **библиотеку класса** в свойствах проекта перед развертыванием в Storm в кластере HDInsight.

### <a name="log-information"></a>Сведения о журнале

С помощью `Context.Logger`можно легко записывать информацию из компонентов топологии в журнал. Например, приведенная ниже команда создает информационную запись журнала:

`Context.Logger.Info("Component started");`

Записанную информацию можно просмотреть в **журнале службы Hadoop**, который можно найти в **обозревателе сервера**. Разверните запись для Storm в кластере HDInsight, а затем — **Журнал службы Hadoop**. Выберите файла журнала для просмотра.

> [!NOTE]  
> Журналы хранятся в используемой в кластере учетной записи хранения Azure. Для просмотра журналов в Visual Studio необходимо войти в подписку Azure, содержащую эту учетную запись хранения.

### <a name="view-error-information"></a>Просмотр информации об ошибке

Чтобы просмотреть ошибки, произошедшие в работающей топологии, сделайте следующее.

1. С **Server Explorer**, правой кнопкой Storm на HDInsight кластера, и выберите Просмотр Storm **Topologies**.

   Для элементов **spout** и **bolt** столбец **Последняя ошибка** содержит сведения о последней ошибке.

2. Выберите **идентификатор Spout** ID или **Идентификатор Болта** для компонента, в который указана ошибка. Страница детали отображает дополнительную информацию об ошибках в разделе **Ошибки** в нижней части страницы.

3. Чтобы получить дополнительную информацию, выберите **Порт** из раздела **«Исполнители»** страницы, чтобы просмотреть журнал работника Шторма за последние несколько минут.

### <a name="errors-submitting-topologies"></a>Ошибки при отправке топологии

Если вы столкнетесь с ошибками, отправляющими топологию в HDInsight, вы можете найти журналы компонентов сервера, которые обрабатывают представление топологии в кластере HDInsight. Чтобы загрузить эти журналы, используйте следующую команду из командной строки:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Замените *sshuser* именем учетной записи пользователя SSH для кластера. Замените *название кластера* названием кластера HDInsight. Дополнительные сведения об использовании `scp` и `ssh` с HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Отправка может завершиться ошибкой по нескольким причинам:

* JDK не установлен или не в пути.
* Требуемые зависимости Java не включены в отправку.
* Зависимости несовместимы.
* Имена топологии дублируются.

Если файл журнала *hdinsight-scpwebapi.out* содержит a, `FileNotFoundException`исключение может быть вызвано следующими условиями:

* JDK не в пути на среде разработки. Убедитесь в том, что пакет JDK установлен в среде разработки, а `%JAVA_HOME%/bin` находится по требуемому пути.
* Вы упускаете зависимость Java. Убедитесь, что вы включите все необходимые файлы .jar как часть отправки.

## <a name="next-steps"></a>Дальнейшие действия

Пример обработки данных из Центров событий см. в статье [Обработка событий из Центров событий Azure с помощью Storm в HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Пример топологии C#, которая разбивает поток данных на несколько потоков, см. на странице с [примером C# Storm](https://github.com/Blackmist/csharp-storm-example).

Дополнительные сведения о создании топологий C# см. в [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Другие методы работы с HDInsight и дополнительные примеры Storm в HDInsight см. в следующих статьях.

**Microsoft SCP.NET**

* [Руководство по программированию SCP для Apache Storm в Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm в HDInsight**

* [Deploy and manage Apache Storm topologies on Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md) (Развертывание топологий Apache Storm и управление ими в Azure HDInsight)
* [Примеры топологий Apache Storm в Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop в HDInsight**

* [Обзор Apache Hive и HiveQL в Azure HDInsight](../hadoop/hdinsight-use-hive.md)
* [Использование MapReduce в Apache Hadoop в HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase в HDInsight**

* [использованию Apache HBase в Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
