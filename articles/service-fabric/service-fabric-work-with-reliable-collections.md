---
title: Работа с Reliable Collections
description: Изучите лучшие практики работы с надежными коллекциями в приложении Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94836a37a62e3eeffb94d891980cc02694bd973e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409809"
---
# <a name="working-with-reliable-collections"></a>Работа с Reliable Collections
Платформа Service Fabric предлагает модель программирования с отслеживанием состояния, которая доступна разработчикам .NET через API-интерфейсы Reliable Collections. В частности, Service Fabric предоставляет такие классы, как Reliable Dictionaries (далее — надежные словари) и Reliable Queues (далее — надежные очереди). При использовании этих классов состояние секционируется (для масштабируемости), реплицируется (для доступности) и обрабатывается в ходе транзакции в секции (для семантики ACID). Давайте посмотрим на типичное использование надежного объекта словаря и посмотрим, что он на самом деле делает.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

Для всех операций с объектами надежных словарей (за исключением операции ClearAsync, которая является обратимой) требуется объект ITransaction. Этот объект связан с любыми изменениями, которые вы пытаетесь внести в любой надежный словарь и/или надежные объекты очереди в пределах одного раздела. Вы приобретаете объект ITransaction, позвонив в метод CreateTransaction менеджера раздела StateManager.

В приведенном выше коде объект ITransaction передается надежному методу AddAsync надежного словаря. Внутри методов словаря, которые принимают ключ, устанавливают блокировки на чтение или запись, связанные с ключом. Если метод изменяет значение ключа, метод принимает блокировку записи на ключе, и если метод читает только от значения ключа, то на ключе берется блокировка чтения. Поскольку AddAsync изменяет значение ключа в новое, пройденое значение, блокировка записи ключа берется. Таким образом, если два (или более) потока пытаются одновременно добавить значения с тем же ключом, один поток получит блокировку записи, а остальные потоки заблокируются. По умолчанию методы блокируются до четырех секунд для получения блокировки. Через четыре секунды методы создают исключение TimeoutException. Существуют перегрузки метода, позволяющие вам пройти явное значение тайм-аута, если вы предпочитаете.

Для этого нужно написать код, который будет реагировать на исключение TimeoutException, перехватывая его и повторяя всю операцию (как показано в приведенном выше коде). В моем простом коде я просто звоню Task.Delay, каждый раз проходящей 100 миллисекунд. Но на самом деле, возможно, будет лучше использовать экспоненциальную задержку.

После получения блокировки метод AddAsync добавляет ссылки на объект ключа и значения для внутренних временных словарей, связанных с объектом ITransaction. Это делается для обеспечения семантики, которая позволяет владельцу считывать собственные записи. То есть, если после вызова AddAsync вызвать метод TryGetValueAsync (с использованием того же объекта ITransaction), значение будет возвращено, даже если транзакция еще не зафиксирована. Затем метод AddAsync сериализует объекты ключа и значения в массивы байтов, а также добавляет эти массивы байтов в файл журнала на локальном узле. Наконец, метод AddAsync отправляет массивы байтов во все вторичные реплики, чтобы реплики имели ту же информацию о ключе и значении. Хотя информация о ключе и значении записана в файл журнала, эти сведения не считаются частью словаря, пока не будет зафиксирована связанная с ними транзакция.

В приведенном выше коде вызов CommitAsync фиксирует все операции транзакции. В частности, этот вызов добавляет в файл журнала на локальном узле сведения о фиксации, а также отправляет запись о фиксации во все вторичные реплики. После получения ответа от большинства реплик все изменения данных считаются постоянными, и все блокировки, связанные с ключами, которые обрабатывались с помощью объекта ITransaction, отменяются. Теперь другие потоки или транзакции могут обрабатывать те же ключи и их значения.

Если метод CommitAsync не вызывается (обычно из-за исключения), объект ITransaction ликвидируется. При утилизации незафиксированного объекта ITransaction Служба Fabric прикладирует информацию об прерывании к файлу журнала локального узла, и ничто не должно быть отправлено ни в один из вторичных реплик. Затем отменяются все блокировки, связанные с ключами, которые обрабатывались с помощью транзакции.

## <a name="volatile-reliable-collections"></a>Нестабильные надежные коллекции 
В некоторых рабочих нагрузках, например, в реплицируемом кэше, можно переносить случайные потери данных. Избежание сохранения данных на диске может обеспечить более поздние просечения и пропускную способность при написании надежных словарей. Компромисс из-за отсутствия настойчивости заключается в том, что в случае потери кворума произойдет полная потеря данных. Поскольку потеря кворума является редким явлением, повышенная производительность может стоить редкой возможности потери данных для этих рабочих нагрузок.

В настоящее время волатильной поддержки доступна только для надежных словарей и надежных очередей, а не для НадежныхConcurrentQueues. Пожалуйста, ознакомьтесь со списком [Caveats,](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) чтобы сообщить о своем решении об использовании нестабильных коллекций.

Чтобы включить нестабильную поддержку ```HasPersistedState``` в службе, ```false```установите флаг в декларации типа обслуживания, чтобы, как это:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Существующие сохраняющиеся службы не могут быть нестабильными, и наоборот. Если вы хотите сделать это, вам нужно будет удалить существующую службу, а затем развернуть службу с обновленным флагом. Это означает, что вы должны быть готовы нести полную ```HasPersistedState``` потерю данных, если вы хотите изменить флаг. 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Типичные проблемы и способы их решения
Теперь, когда вы понимаете, как надежные коллекции работают внутри, давайте взглянем на некоторые распространенные удосы в их изготавливства. Взгляните на этот код.

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Если вы работаете с обычным словарем .NET, вы можете добавить в словарь ключ и значение, а затем изменить значение свойства (например, LastLogin). Но этот код не будет правильно работать с надежным словарем. Как вы помните, вызов метода AddAsync сериализует объекты ключей и значений в массивы байтов, сохраняет эти массивы в локальный файл, а также отправляет их во вторичные реплики. Если позже вы измените свойство, это изменит значение свойства только в памяти; он не влияет на локальный файл или данные, отправленные в реплики. Если процесс выходит из строя, то, что в памяти, выбрасывается. Если запускается новый процесс или основной репликой становится другая реплика, доступным остается только старое значение свойства.

Допустить описанную выше ошибку очень просто. А узнаете вы о ней, только когда произойдет сбой. Чтобы получить правильный код, достаточно поменять местами две строки:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Вот другой пример, демонстрирующий распространенную ошибку.

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Опять же, если используются обычные словари .NET, приведенный выше код работает нормально. Это обычная ситуация — разработчик использует ключ для поиска значений. Если значение существует, разработчик изменяет стоимость свойства. Но если используются API-интерфейсы Reliable Collections, в коде возникает та же проблема. **Не изменяйте объект, если он отправлен в Reliable Collections.**

Чтобы правильно обновить значение в API, нужно получить ссылку на существующее значение и считать объект, на который указывает эта ссылка, неизменяемым. Теперь создайте новый объект, который будет точной копией исходного объекта. Вы можете изменить состояние этого нового объекта и записать новый объект в коллекцию, чтобы он был сериализован в массив байтов, добавлен в локальный файл и отправлен в реплику. После фиксации изменений объекты в памяти, локальный файл и все реплики будут в одинаковом состоянии. Теперь все хорошо.

Следующий пример кода показывает, как правильно обновить значение в коллекции Reliable Collections.

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Определение неизменяемых типов данных для предотвращения ошибок разработчика
В идеале мы хотели бы, чтобы компилятор отчитывался об ошибках при случайном производстве кода, который мутирует состояние объекта, который вы должны считать неизменяемым. Но компилятор C# не может это сделать. Таким образом, чтобы избежать потенциальных ошибок, мы настоятельно рекомендуем определять типы, используемые в Reliable Collections, как неизменяемые. Это означает, что вы будете использовать основные типы значений (например, числа [Int32, UInt64 и т. д.], DateTime, Guid, TimeSpan и т. п). Вы также можете использовать строку. Свойства коллекции лучше не использовать, так как их частая сериализация и десериализация может снизить производительность. Однако, если вы хотите использовать свойства коллекции, мы настоятельно рекомендуем использовать . Net в неизменной библиотеке коллекций ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Эта библиотека доступна для https://nuget.orgскачивания с . Мы также рекомендуем герметизировать ваши классы и при возможности приносить поля только для чтения.

Тип UserInfo ниже демонстрирует, как определить неизменяемый тип, чтобы воспользоваться преимуществами упомянутых выше рекомендаций.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Тип ItemId также является неизменяемым, как показано ниже.

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Управление версиями схемы (обновления)
Внутренне надежные коллекции сериализируют ваши объекты с помощью. NET в DataContractSerializer. Сериализованные объекты сохраняются на локальном диске основной реплики, а также передаются вторичным репликам. По мере развития службы, скорее всего, вы захотите изменить тип данных (схему), который требуется вашей службе. Управлять версиями данных необходимо предельно осторожно. Первое и самое главное: у вас должна быть возможность десериализовать старые данные. Это означает, что код десериализации должен быть неограниченно обратно совместимым: версия 333 кода службы должна поддерживать обработку данных, которые версия 1 кода службы разместила в надежной коллекции 5 лет назад.

Кроме того, код службы обновляет один домен обновления одновременно. Таким образом, во время обновления одновременно работают две различные версии кода службы. Проследите, чтобы новая версия кода службы не использовала новую схему, так как старые версии кода службы могут не работать с новой схемой. По возможности следует разрабатывать каждую новую версию службы так, чтобы она была совместима с версией 1. Это означает, что версия 1 кода службы должна игнорировать все элементы схемы, которые она явно не обрабатывает. Тем не менее, он должен быть в состоянии сохранить любые данные, которые он явно не знает о и записать его обратно при обновлении словарного ключа или значения.

> [!WARNING]
> В то время как вы можете изменить схему ключа, вы должны убедиться, что хэш-код ключа и равные алгоритмы стабильны. Если вы измените работу одного из этих алгоритмов, вы никогда больше не сможете искать ключ в надежном словаре.
> Строки .NET можно использовать в качестве ключа, но использовать саму строку в качестве ключа - не используйте результат String.GetHashCode в качестве ключа.

Кроме того, можете выполнить так называемое двухэтапное обновление. С двухэтапным обновлением службы с V1 до V2: V2 содержит код, который знает, как справиться с новым изменением схемы, но этот код не выполняется. Когда код версии 2 считывает данные версии 1, он работает с этими данными, а затем записывает их. Затем, когда обновление завершится на всех доменах обновления, вы можете сообщить запущенным экземплярам версии 2, что обновление завершено. (Один из способов сигнализировать об этом заключается в развертывании обновления конфигурации; это то, что делает это двухэтапным обновлением.) Теперь экземпляры V2 могут читать данные V1, конвертировать их в данные V2, работать на нем и записывать их в виде данных V2. Когда другие экземпляры считывают данные версии 2, им не нужно преобразовывать их. Они могут работать с ними и записывать как данные версии 2.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании контрактов данных с прямой совместимостью см. в статье [Контракты данных, совместимые с любыми будущими изменениями](https://msdn.microsoft.com/library/ms731083.aspx).

Рекомендации по управлению версиями контрактов данных см. в статье [Управление версиями контракта данных](https://msdn.microsoft.com/library/ms731138.aspx).

Указания по реализации контрактов данных, которые не зависят от версий, см. в статье [Обратные вызовы сериализации, независимые от версий](https://msdn.microsoft.com/library/ms733734.aspx).

Указания по предоставлению структуры данных, которые могут взаимодействовать в нескольких версиях, см. в статье [Интерфейс IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
