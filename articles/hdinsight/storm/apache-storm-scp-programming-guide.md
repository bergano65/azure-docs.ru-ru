---
title: Руководство по программированию с использованием SCP.NET для Storm в HDInsight Azure
description: Узнайте, как использовать SCP.NET для создания топологий Storm на основе .NET для использования со Storm под управлением Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: f462fd88acf04fc8dced3db739a555c371c184ab
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154488"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Инструкции по программированию SCP для Apache Storm в Azure HDInsight

SPC — это платформа для создания надежного, согласованного и высокопроизводительного приложения для обработки данных в реальном времени. Он основан на [Apache Storm](https://storm.incubator.apache.org/) --системе обработки потоков, разработанной сообществом OSS. Оно спроектировано с помощью (Nathan Marz и было открыто с помощью Twitter. В платформе используется [Apache ZooKeeper](https://zookeeper.apache.org/)— еще один проект Apache для обеспечения высоконадежной распределенной координации и управления состоянием.

С помощью проекта SPC удалось не только перенести Storm в Windows, но и добавить расширения и адаптировать его для работы в экосистеме Windows. Дополнения включают в себя возможности разработки .NET и библиотеки; адаптация заключается в возможности развертывания на основе Windows.

Расширение и настройка выполняются таким образом, что нам не нужно развилкировать проекты OSS, и мы можем использовать производные экосистемы, основанные на высшем уровне.

## <a name="processing-model"></a>Модель обработки данных

Данные в SCP оформляются в виде постоянных потоков кортежей данных. Обычно кортежи включаются в очередь на первом этапе, затем бизнес-логика приложения, находящегося внутри топологии Storm, собирает и преобразовывает их. Наконец, данные на выходе могут подаваться в виде кортежей в другую систему SCP или отправляться на хранение в системы распределенных файлов или базы данных, например SQL Server.

![Схема очереди, передающей данные для обработки, которая наполняет хранилище данных](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

В Storm вычислительные графы определяются топологией приложения. Каждый узел в топологии содержит логическую схему обработки информации, а по связям между узлами можно определить движение потока данных. Узлы, которые "вливают" входные данные в топологию, называются объектами _spout_ (воронками) и могут использоваться для создания последовательности данных. Входные данные могут находиться в журналах файлов, базах данных транзакций, системных счетчиках производительности и т. д. Узлы для потоков входных и выходных данных называются объектами _bolt_ (ситами), они осуществляют фактическую фильтрацию, выборку и агрегацию данных.

SCP поддерживает лучшие действия, как минимум один раз и только после обработки данных. В приложении для обработки распределенной потоковой передачи в процессе обработки данных могут возникнуть различные ошибки, такие как сбой сети, сбой компьютера или Ошибка пользовательского кода и т. д. Обработка по принципу "минимум однажды" гарантирует, что все данные будут обрабатываться по крайней мере один раз благодаря автоматической повторной обработке тех же данных в случае ошибки. Обработка по крайней мере один раз является простой и надежной и подходит для многих приложений. Но когда приложение требует точного подсчета данных, модель обработки "минимум однажды" не подходит, так как одни и те же данные могут проходить через топологию приложения несколько раз. В этом случае, только когда обработка будет разработана, чтобы убедиться, что результат правильный, даже если данные могут воспроизводиться и обрабатываться несколько раз.

SCP позволяет разработчикам .NET разрабатывать приложения обработки данных в режиме реального времени, одновременно используя виртуальная машина Java (ВИРТУАЛЬНОЙ машины Java) с высокой назначением. .NET и виртуальные машины Java обмениваются данными через локальные TCP-сокеты. По сути, каждый spout — это пара процессов .NET/Java, где пользовательская логика выполняется в процессе .NET в качестве подключаемого модуля.

Для создания приложения для обработки данных поверх SCP необходимо выполнить следующее:

* Создать и активировать "воронки" для извлечения данных из очереди.
* Создать и активировать элементы bolt для обработки входящих данных и сохранения данных после обработки во внешнем хранилище, например в базе данных.
* Создать топологию процесса, загрузить и запустить ее. Топология определяет вершины и поток данных между вершинами. SCP получает спецификацию топологии и размещает из в кластере Storm, где каждая вершина работает в отдельном логическом узле. Отработка отказа и масштабирование будет выполняться планировщиком заданий.

В этой статье мы разберем несколько простых примеров создания приложения для обработки данных с помощью SCP.

## <a name="scp-plugin-interface"></a>Интерфейс подключаемого модуля SCP

Дополнительные модули (или приложения) SCP — это автономные файлы типа EXE, которые можно запускать в Visual Studio во время фазы разработки, а также можно подключать к конвейеру Storm после завершения разработки. Процесс написания кода дополнительного модуля SCP ничем не отличается от процессов написания кода для стандартных приложений в консоли Windows. Платформа SCP.NET объявляет интерфейс для "воронки" и "сита", которые пользователю нужно заполнить программным кодом для модуля. Это делается для того, чтобы пользователь мог сосредоточиться на своей задаче и предоставил платформе SCP.NET позаботиться обо всех остальных аспектах.

Код дополнительного модуля нужно внести в один из следующих интерфейсов, в зависимости от того, является ли топология транзакционной или нетранзакционной и является ли компонент объектом spout или bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin

ISCPPlugin — это общий интерфейс для различных дополнительных модулей. В настоящее время это фиктивный интерфейс.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout

ISCPSpout — это интерфейс для нетранзакционных "воронок".

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Когда вы вызываете `NextTuple()` , пользовательский код C# может отправить один или несколько кортежей. Если ничего не выдается, этот метод должен возвращать значение без каких-либо порождений. Следует отметить, что `NextTuple()`, `Ack()`и `Fail()` вызываются в строгом цикле в одном потоке C# . Если нет кортежей для выпуска, учтивым NextTuple в течение короткого промежутка времени (например, 10 миллисекунд), так что не тратит слишком много ресурсов ЦП.

Методы `Ack()` и `Fail()` вызываются только в случае активации механизма подтверждения в файле спецификации. `seqId` используется для того, чтобы задать кортеж, который является подтвержденным или неудачным. Если подтверждение включено в нетранзакционной топологии, в "воронке" должны использоваться следующие функции отправки:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Если подтверждение не поддерживается в нетранзакционной топологии, `Ack()` и `Fail()` можно оставить как пустую функцию.

Входной параметр `parms` в этих функциях является пустым словарем, зарезервированным для будущего использования.

### <a name="iscpbolt"></a>ISCPBolt

ISCPBolt — это интерфейс для нетранзакционных "сит"

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

При наличии нового кортежа для его обработки будет вызвана функция `Execute()`.

### <a name="iscptxspout"></a>ISCPTxSpout

ISCPTxSpout — это интерфейс для транзакционных "воронок".

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Как и в случае нетранзакционных счетчиков, `NextTx()`, `Ack()`и `Fail()` все они вызываются в строгом цикле в одном потоке C# процесса. Если нет данных для выдачи, учтивым `NextTx` в течение короткого промежутка времени (10 миллисекунд), чтобы не тратить слишком много ресурсов ЦП.

`NextTx()` вызывается для того, чтобы начать новую передачу, для обозначения транзакции используется выходной параметр `seqId`, который также используется в командах `Ack()` и `Fail()`. В случае с `NextTx()`пользователь может отправлять данные в часть Java. Данные сохраняются в ZooKeeper для возможности провести повторную отсылку. Так как возможности хранения ZooKeeper ограничены, пользователи могут отправлять метаданные, но не могут отправлять данные большого объема в транзакционных объектах spout.

При сбое множество автоматически воспроизводит транзакцию, поэтому `Fail()` не следует вызывать в обычных случаях. Но так как платформа SCP может проверить метаданные от транзакционной "воронки", то она может вызвать `Fail()` , если метаданные неверны.

Входной параметр `parms` в этих функциях является пустым словарем, зарезервированным для будущего использования.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

ISCPBatchBolt — это интерфейс для транзакционных "сит".

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` вызывается при поступлении нового кортежа в молнию. `FinishBatch()` вызывается после завершения этой транзакции. Входной параметр `parms` зарезервирован для использованию в будущем.

Для транзакционной топологии существует важный принцип — `StormTxAttempt`. Она содержит два поля: `TxId` и `AttemptId`. `TxId` используется для определения специальной транзакции, и для заданной транзакции может предприниматься несколько попыток, если перед этим она завершилась ошибкой и была повторена. SCP.NET создает объект ISCPBatchBolt для обработки каждой попытки `StormTxAttempt` точно так же, как это делает Storm в Java. Это делается для поддержки параллельной обработки данных. Пользователю необходимо помнить, что после завершения попытки выполнения транзакции объект ISCPBatchBolt будет уничтожен и удален как мусор.

## <a name="object-model"></a>Модель объектов

SCP.NET предоставляет разработчикам простой набор ключевых объектов для программирования. Они находятся в **контексте**, **статесторе**и **SCPRuntime**. Они обсуждаются в оставшейся части этого раздела.

### <a name="context"></a>Контекст

Context обеспечивает рабочую среду для приложений. Каждому экземпляру дополнительного модуля ISCP (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) соответствует экземпляр объекта Context. Функциональные возможности, предоставляемые контекстом, можно разделить на две части: (1) статическую часть, доступную во всем C# процессе, (2) динамическую часть, которая доступна только для конкретного экземпляра контекста.

### <a name="static-part"></a>Статическая часть

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` отвечает за создание записей в журнале.

`pluginType` используется для указания типа подключаемого модуля в процессе C#. Если процесс C# запущен в локальном тестовом режиме (без Java), в качестве типа подключаемого модуля задается `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` предназначен для получения параметров конфигурации из части Java. Параметры передаются со стороны Java после запуска дополнительного модуля на C#. Параметры `Config` делятся на две части: `stormConf` и `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` — это параметры, определенные Storm, а `pluginConf` — это параметры, определенные SCP. Пример.

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` предоставлен для получения контекста топологии, он наиболее удобен для компонентов с несколькими параллелизмами. Например:

    //demo how to get TopologyContext info
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)                      
    {
        Context.Logger.Info("TopologyContext info:");
        TopologyContext topologyContext = Context.TopologyContext;                    
        Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());          
        taskIndex = topologyContext.GetThisTaskIndex();
        Context.Logger.Info("taskIndex: {0}", taskIndex);
        string componentId = topologyContext.GetThisComponentId();                    
        Context.Logger.Info("componentId: {0}", componentId);
        List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
        Context.Logger.Info("taskNum: {0}", componentTasks.Count);                    
    }

### <a name="dynamic-part"></a>Динамическая часть

Следующие интерфейсы привязаны к определенным экземплярам объекта Context. Экземпляр объекта Context создается платформой SCP.NET и передается в код пользователя.

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Для нетранзакционных "воронок" с поддержкой функции подтверждения обработки данных предусмотрен следующий метод:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Для нетранзакционных "сит" с поддержкой функции подтверждения получения данных после принятия кортежа возможны только две команды: `Ack()` и `Fail()`. При отправке нового кортежа данных "сита" также должны указывать привязки для него. Существуют следующие методы.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore

`StateStore` предоставляет службы метаданных, создание монотонных последовательностей и координацию без ожидания. Распределенные многозадачные абстракции высокого уровня могут создаваться на базе `StateStore`, включая создание распределенных блокировок, распределенных очередей, барьеров и служб транзакций.

Приложения SCP могут использовать объект `State` для сохранения определенной информации в [Apache ZooKeeper](https://zookeeper.apache.org/), особенно в транзакционной топологии. Если во время этого процесса в транзакционной "воронке" происходит сбой и перезагрузка, вся необходимая информация может быть получена из ZooKeeper, и конвейер данных будет восстановлен.

Объект `StateStore` в основном обладает следующими методами.

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

Объект `State` в основном обладает следующими методами.

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Для метода `Commit()`, когда simpleMode имеет значение true, соответствующий ZNode будет удален из ZooKeeper. В противном случае будет удален текущий ZNode и создан новый в COMMITTED\_PATH.

### <a name="scpruntime"></a>SCPRuntime

SCPRuntime предоставляет два метода:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` используется для инициализации среды выполнения SCP. В этом методе C# процесс подключается к стороне Java и получает параметры конфигурации и контекст топологии.

`LaunchPlugin()` используется для запуска цикла обработки сообщений. В этом цикле C# подключаемый модуль получает сообщения на стороне Java (включая кортежи и управляющие сигналы), а затем обрабатывает сообщения, возможно, вызов метода интерфейса, предоставляемого пользовательским кодом. Входной параметр для метода `LaunchPlugin()` является делегатом, который может возвращать объект, реализующий интерфейс ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Для ISCPBatchBolt можно получить `StormTxAttempt` от `parms`и использовать его, чтобы определить, была ли попытка воспроизведена. Проверка попыток воспроизведения часто выполняется в процессе фиксации, и она демонстрируется в `HelloWorldTx` примере.

Как правило, дополнительный модуль SCP в такой ситуации может работать в двух режимах:

1. Локальный тестовый режим. в этом режиме подключаемые модули SCP ( C# пользовательский код) запускаются в среде Visual Studio на этапе разработки. В этом режиме можно использовать параметр `LocalContext`, который указывает метод для сериализации отправленных кортежей в локальные файлы и выполняет обратное считывание в память.

        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }

2. Обычный режим. В этом режиме подключаемые модули SCP запускаются процессом Java Storm.

    Ниже дан пример запуска дополнительного модуля SCP:

        namespace Scp.App.HelloWorld
        {
        public class Generator : ISCPSpout
        {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
            return new Generator(ctx);
            }
        }
   
        class HelloWorld
        {
            static void Main(string[] args)
            {
            /* Setting the environment variable here can change the log file name */
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");
   
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
            }
        }
        }

## <a name="topology-specification-language"></a>Язык спецификации топологии

Спецификация топологии SCP представляет собой доменный язык для описания и настройки топологий SCP. Он основан на Clojure DSL (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) и расширяется точкой подключения службы.

Спецификации топологии можно отправить напрямую в кластер Storm для выполнения через команду ***runspec***.

В SCP.NET добавлены следующие функции для определения транзакционных топологий:

| Новые функции | Параметры | Description |
| --- | --- | --- |
| TX-тополопи |topology-name<br />spout-map<br />bolt-map |Определение транзакционной топологии по имени, &nbsp;карта для определения воронки и сита. |
| SCP-TX-spout |exec-name<br />args<br />fields |Определение транзакционной "воронки". Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />***fields*** — это поля вывода для воронки. |
| SCP-TX-Batch-молния |exec-name<br />args<br />fields |Определение транзакционного пакетного "сита". Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />fields — это поля вывода для "воронки". |
| SCP-TX-Commit-молния |exec-name<br />args<br />fields |Определение транзакционного подтверждающего объекта bolt. Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />***fields*** — это поля вывода для сита. |
| нонткс — тополопи |topology-name<br />spout-map<br />bolt-map |Определение нетранзакционной топологии по имени, &nbsp;карта для определения воронки и сита. |
| SCP-spout |exec-name<br />args<br />fields<br />параметры |Определение нетранзакционной "воронки". Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />***fields*** — это поля вывода для воронки.<br /><br />Параметр ***parameters*** необязательный и используется для указания некоторых параметров, например nontransactional.ack.enabled. |
| SCP-молния |exec-name<br />args<br />fields<br />параметры |Определение нетранзакционного "сита". Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />***fields*** — это поля вывода для сита.<br /><br />Параметр ***parameters*** необязательный и используется для указания некоторых параметров, например nontransactional.ack.enabled. |

Для SCP.NET определены следующие ключевые слова:

| Keywords | Description |
| --- | --- |
| : имя |Определение имени топологии |
| : топология |Определение топологии с использованием приведенных выше функций, а также встроенных функций. |
| :p |Определение подсказок по параллелизму для каждой из "воронок" и "сита". |
| : Конфигурация |Определение параметров конфигурации или обновление существующих параметров. |
| : схема |Определение Схемы потока данных |

Часто используемые параметры:

| Параметр | Description |
| --- | --- |
| "plugin.name" |Имя exe-файла для дополнительного модуля на C# |
| "plugin. args" |Аргументы параметров дополнительного модуля |
| "Output. Schema" |Схема вывода |
| "нетранзакционный. ACK. Enabled" |Если подтверждение обработки для нетранзакционной топологии активно. |

Команда "runspec" запускается вместе с ключами и выглядит это так:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Параметр ***Resource-dir*** является необязательным. его необходимо указать, если требуется подключить C# приложение, а этот каталог содержит приложение, зависимости и конфигурации.

Параметр ***classpath*** также необязательный. Он используется для указания подкаталогов Java, если в файле спецификации содержится Java spout или молния.

## <a name="miscellaneous-features"></a>Прочие функции

### <a name="input-and-output-schema-declaration"></a>Объявление схемы ввода и вывода

Пользователи могут выдавать кортежи C# в процессах, платформа должна сериализовать кортеж в Byte [], передавать на сторону Java, а разрядность будет переносить этот кортеж в целевые объекты. В то время как в C# нижестоящих компонентах процессы получают кортежи со стороны Java и преобразуют их в исходные типы по платформам, все эти операции скрыты платформой.

Для поддержки процессов сериализации и десериализации код пользователя должен объявить схемы ввода и вывода.

Схема потока ввода-вывода — это словарь. Ключом является значение идентификатора потока, а значением — тип столбца. Можно объявить, что компонент поддерживает несколько потоков.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }


В объект Context у нас будут добавлены следующие прикладные программные интерфейсы (API):

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Разработчики должны убедиться, что отправленные кортежи данных подчиняются схеме, заданной для этого потока. В противном случае система создаст исключение среды выполнения.

### <a name="multi-stream-support"></a>Поддержка многопоточности

SCP предоставляет пользовательскому коду возможность получения и отправки данных с помощью множества различных потоков одновременно. Эта возможность отражается в объекте Context, так как метод отправки получает оптимальный параметр идентификатора потока.

В объект SCP.NET Context были добавлены два метода. Они используются для выдачи кортежа или кортежей для указания StreamId. Идентификатор потока (StreamId) — это строка, которая должна одновременно соответствовать C# и Спецификации определений топологии.

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

    /* for non-transactional Spout only */
    public abstract void Emit(string streamId, List<object> values, long seqId);

Отправка в несуществующий поток приведет к возникновению исключения во время выполнения.

### <a name="fields-grouping"></a>Группирование полей

Встроенные поля, сгруппированные по, не работают должным образом в SCP.NET. На стороне прокси-сервера Java все поля представлены потоком байтов [], в то время как функция объединения полей использует для объединения хэш-код объекта в виде потока байтов[]. Хэш-код байтового объекта — это адрес этого объекта в памяти. Поэтому группирование будет неправильным для двух байтовых объектов с одинаковым содержимым, но не с одним и тем же адресом.

В SCP.NET добавлен настраиваемый метод объединения, который для объединения полей в группы использует содержимое byte[]. Синтаксис файла **SPEC** выглядит следующим образом:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )

В данном случае

1. "scp-field-group" означает "адаптированный метод группирования полей с помощью SCP".
2. ":tx" или "non-tx" означает транзакционную или нетранзакционную топологию. Эта информация полезна для нас, поскольку начальных индекс в топологиях ":tx" и "non-tx" отличается.
3. [0,1] означает набор хэш-функций идентификаторов полей (начальное значение — 0).

### <a name="hybrid-topology"></a>Гибридная топология

Изначально система Storm написана на языке Java. И SCP.NET улучшены, чтобы позволить C# разработчикам писать C# код для обработки своей бизнес-логики. Но он также поддерживает гибридные топологии, которые содержат не только C# изменяющие или винты, но и Java spout/«винты».

### <a name="specify-java-spoutbolt-in-spec-file"></a>Задание "воронок" и "сит" на Java в файле спецификаций.

В файле спецификаций команды "scp-spout" и "scp-bolt" также можно использовать для указания "воронок " и "сит" на Java. Пример:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Здесь `microsoft.scp.example.HybridTopology.Generator` является именем класса "воронки" Java.

### <a name="specify-java-classpath-in-runspec-command"></a>Указание classpath Java в команде runSpec

Если вы хотите отправить топологию с "воронками" и "ситами" Java, сначала необходимо скомпилировать "воронки" или "сита" Java и получить JAR-файлы. Затем при отправке топологии нужно указать classpath Java, который содержит JAR-файлы. Например:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

**examples\\HybridTopology\\java\\target\\** — папка с JAR-файлом воронки или сита Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Сериализация и десериализация между Java иC#

Компонент SCP включает в себя и C# сторону Java. Для взаимодействия с собственными "воронками" и "ситами" Java нужно выполнять сериализацию и десериализацию между частями Java и C#, как показано на следующей схеме.

![Схема компонента Java, отправляющего данные в компонент SCP, который отправляет данные в компонент Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. Сериализация на стороне Java и десериализация в C# сторону

   Для начала нам нужно обеспечить стандартное применение сериализации на стороне Java и десериализации на стороне C#. Метод сериализации на стороне Java можно задать через файл спецификаций:

       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })

   Метод десериализации на стороне C# можно задать через программный код пользователя:

       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            

   Эта реализация по умолчанию должна работать в большинстве случаев, если тип данных не слишком сложен. В некоторых случаях из-за того, что пользовательский тип данных слишком сложен или производительность нашей реализации по умолчанию не соответствует требованиям пользователя, пользователи могут подключить собственную реализацию.

   Интерфейс сериализации на стороне Java задается следующим образом:

       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }

   Интерфейс сериализации на стороне C# задается следующим образом:

   Общедоступный интерфейс ICustomizedInteropCSharpDeserializer.

       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. Сериализация в C# сторону и десериализации на стороне Java

   Метод десериализации в части C# следует указать в пользовательском коде C#.

       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 

   Метод десериализации в части Java следует указать в SPEC-файле.

    ```
    (scp-spout
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       }
    )
    ```

   Здесь microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer — имя десериализатора, а microsoft.scp.example.HybridTopology.Person — целевой класс, в который десериализуются данные.

   Пользователь может также подключить собственную реализацию C# сериализатора и десериализатора Java. Этот код является интерфейсом для C# сериализатора:

       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }

   Код ниже — это интерфейс для десериализатора Java.

       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Хост-режим SCP

В этом режиме пользователь может компилировать код в DLL-файл и использовать предоставляемый SCP файл SCPHost.exe, чтобы задать топологию. Соответствующий файл спецификации выглядит следующим образом:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Здесь в качестве `plugin.name` указан `SCPHost.exe` из пакета SDK для SCP. Файлу SCPHost.exe требуется три параметра:

1. Первый из них — это имя библиотеки DLL. В данном примере это `"HelloWorld.dll"`.
2. Второй — это имя класса. В данном примере это `"Scp.App.HelloWorld.Generator"`.
3. Третий — это общедоступный статический метод, который можно вызвать для того, чтобы получить экземпляр ISCPPlugin.

В хост-режиме код пользователя компилируется в DLL-файл и вызывается платформой SCP. Поэтому платформа SCP получает полный контроль над всей логической схемой обработки информации. Мы рекомендуем нашим клиентам задавать топологию через хост-режим SCP, поскольку это упрощает процесс программирования, способствует увеличению его гибкости, а также лучшей совместимости с предыдущими версиями.

## <a name="scp-programming-examples"></a>Примеры программирования для SCP

### <a name="helloworld"></a>HelloWorld

**HelloWorld** — это простой пример, демонстрирующий SCP.NET. Она использует нетранзакционную топологию, в которой воронка называется **generator**, а два сита — **splitter** и **counter**. Объект spout с именем **generator** случайным образом создает предложения и отправляет их в объект bolt c именем **splitter**. Разделитель «молния * *» разделяет предложения на слова и выдает эти слова в виде **счетчика** . "Сито" "counter" использует словарь для записи частоты употребления каждого слова.

Для этого примера существует два файла спецификации — **HelloWorld.spec** и **HelloWorld\_EnableAck.spec**. В программном коде C# можно выяснить, задействована ли функция подтверждения, получив pluginConf со стороны Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

В spout, если подтверждение включено, словарь используется для кэширования кортежей, которые не были подтверждены. Если вызывается Fail(), то отклоненный кортеж отсылается повторно.

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### <a name="helloworldtx"></a>HelloWorldTx

Пример **HelloWorldTx** демонстрирует, как реализовать транзакционную топологию. Он содержит объект spout с именем **generator**, пакетный объект bolt c именем **partial-count** и объект bolt подтверждения c именем **count-sum**. В примере содержатся три предварительно созданных текстовых файла: **DataSource0.txt**, **DataSource1.txt** и **DataSource2.txt**.

При каждой транзакции объект spout c именем **generator** случайным образом выбирает два из трех предварительно созданных файла и отправляет их имена в объект bolt c именем **partial-count**. Объект bolt с именем **partial-count** сначала извлекает имя файла из полученного кортежа, открывает этот файл и считает количество слов в нем, после чего отправляет полученное значение в объект bolt с именем **count-sum**. Объект bolt с именем **count-sum** вычисляет итоговое количество.

Чтобы добиться **ровно** такой семантики **, необходимо определить** , является ли эта транзакция воспроизводимой. В этом примере транзакция имеет статическую переменную-член.

    public static long lastCommittedTxId = -1; 

После создания экземпляра ISCPBatchBolt он извлекает `txAttempt` из входящих параметров.

    public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
    {
        /* for transactional topology, we can get txAttempt from the input parms */
        if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
        {
            StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
            return new CountSum(ctx, txAttempt);
        }
        else
        {
            throw new Exception("null txAttempt");
        }
    }

При вызове `FinishBatch()` `lastCommittedTxId` обновляется, если это не воспроизводимая транзакция.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }

### <a name="hybridtopology"></a>Гибридная топология

Эта топология содержит Java spout и C# молния. В ней используются стандартные для платформы SCP процессы сериализации и десериализации. Сведения о файле спецификаций см. в файле **HybridTopology.spec** в папке **examples\\HybridTopology**, а сведения о том, как задавать путь к классу Java, — в файле **SubmitTopology.bat**.

### <a name="scphostdemo"></a>SCPHostDemo

Этот пример по своей сути не отличается от примера с программой HelloWorld. Единственное отличие заключается в том, что пользовательский код компилируется в DLL-файл и топология задается через файл SCPHost.exe. Дополнительная информация содержится в разделе "Хост-режим SCP".

## <a name="next-steps"></a>Следующие шаги

Примеры топологий Apache Storm, созданных с помощью SCP, приведены в следующих документах:

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Обработка событий из Центров событий Azure с помощью Apache Storm в HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Обработка данных с датчиков автомобилей из Центров событий с помощью Apache Storm в HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Извлечение, преобразование и загрузка данных из Центров событий Azure в Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
