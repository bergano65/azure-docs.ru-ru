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
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186857"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Руководство по программированию SCP для Apache Storm в Azure HDInsight

SCP — это платформа для создания приложений для обработки данных в режиме реального времени, надежных, последовательных и высокопроизводительных приложений для обработки данных. Он построен на вершине [Apache Storm](https://storm.incubator.apache.org/), который представляет собой систему обработки потоков, разработанную сообществами программного обеспечения с открытым исходным кодом. Натан Марц создал Шторм. Он был опубликован в качестве открытого источника в Twitter. Storm использует [Apache зоозащитник](https://zookeeper.apache.org/), который является еще одним проектом Apache, который позволяет высоконадежную распределенную координацию и управление состоянием.

Проект SCP портировал не только Storm на Windows, но и расширение и настройку для среды Windows. Расширения включают опыт разработчиков .NET и библиотеки .NET. Настройка включает развертывание на базе Windows.

С расширением и настройкой вам не нужно раскливать проекты программного обеспечения с открытым исходным кодом. Вы можете использовать производные среды, которые построены на вершине Storm.

## <a name="processing-model"></a>Модель обработки данных

Данные в SCP оформляются в виде постоянных потоков кортежей данных. Как правило, заумные лысые:

1. Поток в очередь.
1. Подбираются и преобразуются бизнес-логики, размещенные внутри топологии Storm.
1. Либо их выход по себе, как tuples к другой системе SCP или привержены магазинов, таких как распределенные файловые системы и базы данных, как S'L Server.

![Диаграмма данных о подаче данных очереди для обработки, которая, в свою очередь, питает хранилище данных](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

В Storm топология приложений определяет график вычислений. Каждый узлы в топологии содержит логику обработки. Ссылки между узлами указывают на поток данных.

Узлы, вводящие входные данные в топологию, называются _носиками._ Вы можете использовать их для последовательности данных. Данные ввода могут поступать из источника, например, из журналов файлов, транзакционной базы данных или счетчика производительности системы.

Узлы, имеющие как входные, так и выходные потоки данных, называются _болтами._ Они делают фактическую фильтрацию данных, выбор и агрегацию.

SCP поддерживает наилучшие усилия, по крайней мере один раз, и точно один раз обработки данных.

В распределенном приложении для обработки потоков могут возникнуть ошибки при обработке данных. К таким ошибкам относятся сбой в работе сети, сбой машины или ошибка в коде. Обработка по крайней мере один раз гарантирует, что все данные обрабатываются по крайней мере один раз путем автоматического воспроизведения одних и тех же данных при ошибке.

По крайней мере один раз обработка проста и надежна, и она подходит для многих приложений. Но когда приложение требует точного подсчета, обработка по крайней мере один раз недостаточна, потому что одни и те же данные могут быть воспроизведены в топологии приложения. В этом случае точно после обработки гарантируется, что результат правильный, даже если данные воспроизводится и обрабатываются несколько раз.

SCP позволяет разработчикам .NET создавать приложения для обработки данных в режиме реального времени при использовании Виртуальной машины Java (JVM) с Storm. JVM и .NET общаются через локальные розетки TCP. Каждый носик/болт представляет собой процессную пару .NET/Java, где пользовательская логика выполняется в процессе .NET в виде плагина.

Чтобы создать приложение для обработки данных поверх SCP, выполните следующие действия:

1. Проектирование и реализация носиков для вытягивания данных из очередей.
1. Проектирование и реализация болтов, обрабатываемых входными данными и сохраняеих их в внешних хранилищах, таких как база данных.
1. Дизайн топологии, а затем представить и запустить его.

Топология определяет вершины и данные, которые текут между ними. SCP принимает топологические спецификации и развертывает его на кластере Storm, где каждый вершина работает на одном логическом узлах. Планировщик задач шторма заботится о сбое и масштабировании.

В этой статье используются несколько простых примеров, чтобы пройти через как построить приложения для обработки данных с SCP.

## <a name="scp-plug-in-interface"></a>Интерфейс плагина SCP

Плагины SCP являются автономными приложениями. Они могут работать внутри Visual Studio во время разработки и быть подключены к конвейеру Storm после развертывания производства.

Написание плагина SCP - это то же самое, что писать любое другое приложение консоли Windows. Платформа SCP.NET объявляет некоторые интерфейсы для носика/болта. Ваш код плагина реализует эти интерфейсы. Основная цель этой конструкции заключается в том, чтобы позволить вам сосредоточиться на вашей бизнес-логики, позволяя SCP.NET платформы обрабатывать другие вещи.

Ваш код плагина реализует один из следующих интерфейсов. Какой интерфейс зависит от того, является ли топология транзакционной или нетранзакционной и является ли компонент носиком или болтом.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** является общим интерфейсом для многих плагинов. В настоящее время, это манекен интерфейс.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** — это интерфейс для нетранзакционного носика.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

При вызове **NextTuple** ваш код C-кода может излучать один или несколько подготовителей. Если нет ничего, чтобы излучать, этот метод должен вернуться, не испуская ничего.

**Методы NextTuple,** **Ack**и **Fail** называются в узком цикле в одном потоке процесса C. Когда Есть нет tuples излучать, есть **NextTuple** сна в течение короткого периода времени, как 10 миллисекунд. Этот сон помогает избежать расточнистых возможностей процессора.

Методы **Ack** и **Fail** называются только тогда, когда файл спецификации позволяет механизм подтверждения. Параметр *seqId* определяет тупл, который признан или не удался. Если подтверждение включено в топологии нетранзакционных, следующая функция **Эмита** должна быть использована в носике:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Если топология нетранзакционных платежей не поддерживает признание, **Ack** и **Fail** могут быть оставлены пустыми функциями.

Параметр ввода *параметра в* этих функциях определяет пустой словарь и зарезервирован для использования в будущем.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** является интерфейсом для нетранзакционного болт.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

При наличии нового тупла функция **"Выполнение"** вызывается для его обработки.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** — это интерфейс для транзакционного носика.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Так же, как их нетранзакционные аналоги, **NextTx**, **Ack**, и **Fail** все называются в жесткой петле в одном потоке процесса C. Когда нет tuples излучать, есть **NextTx** спать в течение короткого периода времени, как 10 миллисекунд. Этот сон помогает избежать расточнистых возможностей процессора.

Когда **NextTx** вызывается для запуска новой транзакции, параметр вывода *seqId* определяет транзакцию. Транзакция также используется в **Ack** и **Fail**. Ваш метод **NextTx** может излучать данные на стороне Java. Данные сохраняются в ZooKeeper для возможности провести повторную отсылку. Поскольку у зоозащитника ограниченная емкость, ваш код должен излучать только метаданные, а не объемные данные в транзакционном носике.

Поскольку Storm автоматически воспроизводит неудачную транзакцию, **Fail** обычно не вызывается. Но если SCP может проверить метаданные, испускаемые транзакционным носиком, он может вызвать **Fail,** когда метаданные недействительны.

Параметр ввода *параметра в* этих функциях определяет пустой словарь и зарезервирован для использования в будущем.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** является интерфейсом для транзакционного болта.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Метод **выполнения** вызывается, когда новый tuple прибывает в болт. Метод **FinishBatch** вызывается по окончании транзакции. Параметр ввода *параметра параметра параметра* для использования в будущем.

Для транзакционной топологии **StormTxAttempt** является важным классом. Он имеет два члена: **TxId** и **AttemptId**. Участник **TxId** определяет конкретную транзакцию. Транзакция может быть предпринята несколько раз, если она не удается и воспроизводится.

SCP.NET создает новый объект **ISCPBatchBolt** для обработки каждого объекта **StormTxAttempt,** точно так же, как то, что Storm делает на Java. Целью этой конструкции является поддержка параллельной обработки транзакций. После завершения попытки транзакции соответствующий объект **ISCPBatchBolt** уничтожается и собирается мусор.

## <a name="object-model"></a>Объектная модель

SCP.NET предоставляет разработчикам простой набор ключевых объектов для программирования. Объектами являются **Context,** **StateStore**и **SCPRuntime.** Они обсуждаются в этом разделе.

### <a name="context"></a>Контекст

Объект **Контекста** обеспечивает среду работы приложения. Каждый экземпляр **ISCPPlugin** **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout**или **ISCPBatchBolt** имеет соответствующий экземпляр **Context.** Функциональность, предоставляемая **Context,** разделена на следующие две части:

* Статическая часть, которая доступна во всем процессе C
* Динамическая часть, которая доступна только для конкретного экземпляра **Context**

### <a name="static-part"></a>Статическая часть

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

Объект **Logger** предназначен для целей ведения журнала.

Объект **pluginType** указывает на тип плагина процесса C. Если процесс запущен в локальном тестовом режиме без Java, тип плагина **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

Свойство **Config** получает параметры конфигурации со стороны Java, которая передает их при инициализации плагина C'. Параметры **Config** разделены на две части: **stormConf** и **pluginConf.**

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

Часть **stormConf** является параметрами, определенными Storm, а часть **pluginConf** — параметрами, определенными SCP. Ниже приведен пример:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

Тип **TopologyContext** получает контекст топологии. Это наиболее полезно для нескольких параллельных компонентов. Ниже приведен пример:

```csharp
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
```

### <a name="dynamic-part"></a>Динамическая часть

Следующие интерфейсы относятся к определенному экземпляру **Context,** который создается платформой SCP.NET и передается на код:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Для носика, поддерживающего подтверждение, нетранзакционный носик:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Нетранзакционный болт, поддерживающий подтверждение, должен явно вызывать **Ack** или **Fail** с полученным tuple. При излучении нового тупла, болт должен также указать якоря tuple'. Предоставляются следующие методы:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

Объект **StateStore** предоставляет услуги метаданных, монотоновое генерирующее последовательность и беспереданную координацию. Вы можете построить более высокий уровень распределенных параллелизмов абстракций на **StateStore**. Эти абстракции включают распределенные блокировки, распределенные очереди, барьеры и службы транзакций.

Приложения SCP могут использовать **государственный** объект для сериализации информации в [Apache зоопарке.](https://zookeeper.apache.org/) Эта способность особенно ценна для транзакционной топологии. Если транзакционный носик перестает отвечать и перезапускается, **state** может получить необходимую информацию от зоозащитника и перезапустить конвейер.

Объект **StateStore** имеет следующие основные методы:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**Государственный** объект имеет следующие основные методы:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Когда **simpleMode** настроен на **истину,** метод **Commit** удаляет соответствующий «Node» в зоопарке. В противном случае метод удаляет текущий «Node» и\_добавляет новый узел в COMMITTED PATH.

### <a name="scpruntime"></a>SCPRuntime

Класс **SCPRuntime** предоставляет следующие два метода:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Метод **Инициализации** инициализирует среду времени выполнения SCP. В этом методе процесс C-обработки подключается к стороне Java, чтобы получить параметры конфигурации и контекст топологии.

Метод **LaunchPlugin** запускает цикл обработки сообщений. В этом цикле плагин C's получает сообщения со стороны Java. Эти сообщения включают в себя tuples и контрольные сигналы. Затем плагин обрабатывает сообщения, возможно, вызывая метод интерфейса, предусмотренный вашим кодом.

Параметр ввода для **LaunchPlugin** является делегатом. Метод может вернуть объект, реализуемый **интерфейсis ISCPSpout,** **ISCPBolt,** **ISCPTxSpout**или **интерфейс ISCPBatchBolt.**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Для **ISCPBatchBolt**вы можете получить объект **StormTxAttempt** от параметра *parms* и использовать его, чтобы судить, является ли попытка воспроизведенной попыткой. Проверка попытки воспроизведения часто выполняется при болте коммита. Пример HelloWorldTx, показанный позднее в этой статье, демонстрирует эту проверку.

Плагины SCP обычно могут работать в двух режимах: локальном тестовом режиме и обычном режиме.

#### <a name="local-test-mode"></a>Режим локального тестирования

В этом режиме плагины SCP в коде C's работают внутри Visual Studio на этапе разработки. Интерфейс **ILocalContext** можно использовать в этом режиме. Интерфейс предоставляет методы для сериализации испускаемых подделок в локальных файлах и чтения их обратно в оперативную память.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Регулярный режим

В этом режиме процесс Storm Java запускает плагины SCP. Вот пример:

```csharp
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
```

## <a name="topology-specification-language"></a>Язык спецификации топологии

Спецификация SCP Topology — это язык для описания и настройки топологий SCP. Он основан на [Кложуре DSL шторма](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) и расширен SCP.

Вы можете отправлять спецификации топологии непосредственно в кластер Storm для выполнения через команду **runSpec.**

SCP.NET добавил следующие функции для определения транзакционных топологий:

| Новая функция | Параметры | Описание |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |Определяет топологию транзакций с именем топологии, картой определения носиков и картой определения болтов. |
| **scp-tx-spout** |*exec-name*<br />*Args*<br />*поля* |Определяет транзакционный носик. Функция выполняет приложение, указанное *exec-name* и использует *args.*<br /><br />Параметр *полей* определяет поля выхода для носика. |
| **scp-tx-batch-bolt** |*exec-name*<br />*Args*<br />*поля* |Определяет транзакционный пакетный болт. Функция выполняет приложение, указанное *exec-name* и использует *args.*<br /><br />Параметр *полей* определяет поля выхода для болта. |
| **scp-tx-commit-bolt** |*exec-name*<br />*Args*<br />*поля* |Определяет болт транзакционного коммита. Функция выполняет приложение, указанное *exec-name* и использует *args.*<br /><br />Параметр *полей* определяет поля выхода для болта. |
| **nontx-топология** |*topology-name*<br />*spout-map*<br />*bolt-map* |Определяет нетранзакционную топологию с именем топологии, картой определения носиков и картой определения болтов. |
| **scp-spout** |*exec-name*<br />*Args*<br />*поля*<br />*Параметры* |Определяет нетранзакционный носик. Функция выполняет приложение, указанное *exec-name* и использует *args.*<br /><br />Параметр *полей* определяет поля выхода для носика.<br /><br />Параметр *параметров* не является обязательным. Используйте его для определения параметров, таких как "nontransactional.ack.enabled". |
| **scp-bolt** |*exec-name*<br />*Args*<br />*поля*<br />*Параметры* |Определяет нетранзакционный болт. Функция выполняет приложение, указанное *exec-name* и использует *args.*<br /><br />Параметр *полей* определяет поля выхода для болта<br /><br />Параметр *параметров* не является обязательным. Используйте его для определения параметров, таких как "nontransactional.ack.enabled". |

SCP.NET определяет следующие ключевые слова:

| Ключевое слово | Описание |
| --- | --- |
| **:имя** |Название топологии |
| **:topology** |Топология с использованием функций в предыдущей таблице и встроенных функций |
| **:p** |Параллелизм намек на каждый носик или болт |
| **:конфиг** |Настраиваете ли параметры или обновляют существующие |
| **:схема** |Схема потока |

SCP.NET также определяет эти часто используемые параметры:

| Параметр | Описание |
| --- | --- |
| "plugin.name" |Имя файла .exe плагина C |
| "plugin.args" |Аргументы плагина |
| "output.schema" |Схема вывода |
| "nontransactional.ack.enabled" |Включено ли подтверждение для топологии нетранзакционных операций |

Команда **runSpec** развертывается вместе с битами. Вот использование команды:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Параметр *ресурса-дир* является необязательным. Укажите его, когда вы хотите подключить приложение C'. Указанный каталог содержит приложения, зависимости и конфигурации.

Параметр *classpath* также необязательный. Он определяет класс Java, если файл спецификации содержит носик Java или болт.

## <a name="miscellaneous-features"></a>Различные функции

### <a name="input-and-output-schema-declarations"></a>Заявления о входных данных и схемах вывода

Ваши процессы C' могут излучать tuples. Для этого платформа сериализирует tuples в объекты **байт** и передает объекты на сторону Java. Шторм после этого передает эти tuples к целям.

В компонентах ниже по течению процессы C-версии получают займы обратно со стороны Java и преобразуют их в исходные типы платформы. Все эти операции скрыты платформой.

Для поддержки сериализации и десериализации код должен декларировать схему ввода и вывода. Схема определяется как словарь. Идентификатор потока — это ключ словаря. Ключевым значением являются типы столбцов. Компонент может декларировать несколько потоков.

```csharp
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
```

Следующая функция добавляется к объекту **Контекста:**

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Разработчики должны убедиться, что испускаемые подмены подмены схемы, определенной для потока. В противном случае система будет забрасывать время выполнения.

### <a name="multistream-support"></a>Поддержка мультипотоков

SCP позволяет вашему коду излучать или получать из нескольких различных потоков одновременно. Объект **Context** отражает эту поддержку как дополнительный параметр идентификатора потока метода **Эмита.**

Добавлены два метода в объекте SCP.NET **контекста.** Они излучают один или несколько заумных веществ для конкретных потоков. Параметр *streamId* является строкой. Его значение должно быть одинаковым как в коде СЗ, так и в спецификации топологии-определения.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Излучение несуществующего потока приводит к исключениям из времени выполнения.

### <a name="fields-grouping"></a>Группировка полей

Встроенная группировка полей в Storm не работает должным образом в SCP.NET. На стороне прокси Java, тип данных всех полей на самом деле **байт .** Для группировки поля используется хэш-код объекта **байта.** Хэш-код является адресом этого объекта в оперативной памяти. Таким образом, группировка будет неправильной для мультибайтных объектов, которые имеют один и тот же контент, но не тот же адрес.

SCP.NET добавляет индивидуальный метод группировки, и он использует содержимое объекта **байта** для создания группы. В файле спецификации синтаксис выглядит следующим образом:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

В предыдущем файле спецификации:

* `scp-field-group`указывает, что группировка представляет собой настраиваемую группировку на местах, реализуемую SCP.
* `:tx`или `:non-tx` уточняет, является ли топология транзакционной. Эта информация нужна, так как начальный индекс отличается между транзакционными и нетранзакционными топологиями.
* `[0,1]`определяет набор идотов поля, который начинается с нуля.

### <a name="hybrid-topology"></a>Гибридная топология

Код Native Storm написан на Java. SCP.NET расширила Storm, чтобы вы могли писать код C,s для обработки бизнес-логики. Но SCP.NET также поддерживает гибридные топологии, которые содержат не только носики/болты Найла, но и носики/болты Java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Указать носик Java/болт в файле спецификации

Вы можете использовать **scp-носик** и **scp-bolt** в файле спецификации, чтобы указать носики и болты Java. Ниже приведен пример:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Вот `microsoft.scp.example.HybridTopology.Generator` название класса носик Java.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Укажите класс Java в команде runSpec

Если вы хотите отправить топологию, содержащую носики или болты Java, сначала составить их для создания файлов JAR. Затем укажите java classpath, который содержит файлы JAR при отправке топологии. Ниже приведен пример:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Вот `examples\HybridTopology\java\target\` папка, содержащая файл Java носик/болт JAR.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Сериализация и десериализация между Java и C #

Компонент SCP включает в себя сторону Java и сторону СЗ. Для взаимодействия с родными носиками/болтами Java, сериализация и десериализация должны происходить между java-стороной и стороной СЗ, как показано на следующем графике:

![Диаграмма отправки компонента Java в компонент SCP, который затем отправляется в другой компонент Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Сериализация в Java-стороне и десериализация в сторону СЗ

Сначала предоставьте реализацию по умолчанию для сериализации в java-стороне и десериализации в сторону СЗ.

Укажите метод сериализации стороны Java в файле спецификации.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Укажите метод десериализации стороны в коде C'.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Если тип данных не слишком сложен, эта реализация по умолчанию должна обрабатываться в большинстве случаев. Вот случаи, когда вы можете подключить свою собственную реализацию:

* Ваш тип данных слишком сложен для реализации по умолчанию.
* Производительность реализации по умолчанию не соответствует вашим требованиям.

Интерфейс сериализации в стороне Java определяется как:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

Интерфейс десериализации в сторону СЗ определяется как:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Сериализация в сторону СЗ и десериализация на стороне Java

Укажите метод сериализации стороны в коде C'.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Укажите метод десериализации Java-стороны в файле спецификации.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Здесь `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` находится название десериализатора, `"microsoft.scp.example.HybridTopology.Person"` и это целевой класс, к которому десериализованы данные.

Вы также можете подключить собственную реализацию серизатора и Java deserializer.

Этот код является интерфейсом для серийного сервера C':

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Этот код является интерфейсом для Java deserializer:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Режим хоста SCP

В этом режиме вы можете компилировать свой код в качестве DLL и использовать SCPHost.exe в соответствии с sCP для отправки топологии. Файл спецификации выглядит следующим образом:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Здесь, `"plugin.name"` указано, как `"SCPHost.exe"`, который предоставляется SCP SDK. SCPHost.exe принимает три параметра в следующем порядке:

1. Имя DLL, которое находится в этом примере. `"HelloWorld.dll"`
1. Название класса, которое находится в этом примере. `"Scp.App.HelloWorld.Generator"`
1. Название общестатического метода, на который можно ссылаться, чтобы получить экземпляр **ISCPPlugin**.

В режиме хоста компилируйте свой код в качестве DLL для вызова платформой SCP. Поскольку платформа может получить полный контроль над всей логикой обработки, мы рекомендуем вам представить топологию в режиме sCP host. Это упрощает опыт разработки. Это также приносит вам больше гибкости и лучше обратной совместимости для последующих релизов.

## <a name="scp-programming-examples"></a>Примеры программирования SCP

### <a name="helloworld"></a>HelloWorld

Следующий простой пример HelloWorld показывает вкус SCP.NET. Он использует нетранзакционную топологию с носик называется **генератор** и два болта называется **сплиттер** и **счетчик**. **Генератор** носик случайно генерирует предложения и излучает эти предложения **сплиттер.** Болт **сплиттера** разбивает предложения на слова и излучает эти слова **в счетчик** болт. **Счетчик** болт использует словарь для записи возникновения каждого слова.

Этот пример имеет два файла спецификации:\_HelloWorld.spec и HelloWorld EnableAck.spec. Код C's может узнать, включен ли `pluginConf` подтверждение, получив объект со стороны Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Если подтверждение включено в носике, словарь кэширует tuples, которые не были признаны. Если `Fail` называется, неудавшийся tuple воспроизводится.

```csharp
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
```

### <a name="helloworldtx"></a>HelloWorldTx

Следующий пример HelloWorldTx демонстрирует, как реализовать транзакционную топологию. Пример имеет один носик называется **генератор,** пакет болт называется **частичный счет,** и болт коммита называется **кол-сум**. В примере также есть три существующих текстовых файла: DataSource0.txt, DataSource1.txt и DataSource2.txt.

В каждой транзакции носик **генератора** случайным образом выбирает два файла из существующих трех файлов и излучает два имени файлов в болт **частичного подсчета.** Болт **частичного счета:**

1. Получает имя файла от полученного tuple.
1. Открывает соответствующий файл.
1. Подсчитывает количество слов в файле.
1. Излучает количество слов на болт **подсчета.**

Объект bolt с именем **count-sum** вычисляет итоговое количество.

Чтобы достичь точно один раз семантики, **счет-сумма** коммит болт должен судить, является ли это воспроизведена сделки. В этом примере он имеет следующую статическую переменную члена:

```csharp
public static long lastCommittedTxId = -1; 
```

При создании экземпляра **ISCPBatchBolt** значение `txAttempt` объекта становится от входных параметров.

```csharp
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
```

Когда `FinishBatch` вызывается, обновляется, `lastCommittedTxId` если это не восвоспроизведенная транзакция.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>Гибридная топология

Эта топология содержит носик Java и болт на C-болт. Он использует реализацию сериализации и десериализации по умолчанию, предоставляемую платформой SCP. Смотрите файл HybridTopology.spec в\\папке HybridTopology для деталей файла спецификации. Также смотрите SubmitTopology.bat, как указать класс Java.

### <a name="scphostdemo"></a>SCPHostDemo

Этот пример, по сути, такой же, как HelloWorld. Разница лишь в том, что ваш код компилируется как DLL и топология представляется с помощью SCPHost.exe. Более подробного объяснения можно узнать в разделе sCP host mode.

## <a name="next-steps"></a>Дальнейшие действия

Для примеров топологий Apache Storm, созданных с помощью SCP, см.

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Обработка событий из Центров событий Azure с помощью Apache Storm в HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Обработка данных с датчиков автомобилей из Центров событий с помощью Apache Storm в HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Извлечение, преобразование и загрузка (ETL) из концентраторов событий Azure в Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
