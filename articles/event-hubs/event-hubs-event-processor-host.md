---
title: Получение событий от Центров событий Azure с помощью узла обработчика событий | Документация Майкрософт
description: В этой статье описывается узел обработчика событий в Центрах событий Azure, который упрощает управление параллельными контрольными точками, арендой и чтением событий.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 26f0abb48ba268f79167ed5d00e4f96d8b5e5998
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821875"
---
# <a name="receive-events-from-azure-event-hubs-using-event-processor-host"></a>Получение событий от Центров событий Azure с помощью узла обработчика событий

Центры событий Azure — это эффективная служба обработки данных телеметрии, которая может использоваться для потоковой передачи миллионов событий без лишних затрат. В этой статье описывается, как использовать получаемые события с помощью *Узла обработчика событий* (EPH). Этот интеллектуальный потребительский агент упрощает управление контрольными точками, сданными ресурсами и параллельными модулями чтения.  

Масштабирование в Центрах событий базируется на идее секционированных потребителей. В отличие от шаблона [конкурирующих потребителей](https://msdn.microsoft.com/library/dn568101.aspx), секционированный потребительский шаблон обеспечивает высокий уровень масштабирования путем удаления конфликтного узкого места и упрощения сквозного параллелизма.

## <a name="home-security-scenario"></a>Сценарий домашней безопасности

В качестве примера рассмотрим компанию по обеспечению безопасности дома, которая отслеживает 100 000 домов. Каждую минуту она получает данные с различных датчиков, таких как детектор движения, датчик открытия двери или окна, датчики разбития стекла и т. д., установленные в каждом доме. Компания предоставляет веб-сайт для жителей, чтобы наблюдать за деятельностью своего дома почти в реальном времени.

Каждый датчик отправляет данные в концентратор событий. Концентратор событий настроен на 16 секций. На стороне потребителя необходим механизм, который может читать эти события, объединять их (фильтровать, выполнять статистическое вычисление и т. д.), а также выгрузить резервную копию в большой двоичный объект хранилища, который затем проецируется на удобную веб-страницу.

## <a name="write-the-consumer-application"></a>Написание приложения-потребителя

При создании потребителя в распределенной среде сценарий должен удовлетворять следующие требования:

1. **Масштабирование.** Создайте несколько потребителей, где каждый потребитель получит право на чтение из нескольких секций Центров событий.
2. **Балансировка нагрузки.** Динамически изменяйте количество потребителей. Например, при добавлении нового типа датчика в каждый дом (например, детектора угарного газа) увеличивается число событий. В этом случае оператор (человек) увеличивает число экземпляров потребителя. Затем пул потребителей может перебалансировать количество секций, которыми они владеют, для распределения нагрузки на вновь добавленных потребителей.
3. **Бесперебойное возобновление в случае сбоев.** Если у потребителя (**потребитель А**) происходит сбой (например, на виртуальной машине, где размещается потребитель, происходит аварийное завершение), тогда другие пользователи должны иметь возможность выбирать секции, принадлежащие **потребителю A**, и продолжать работу. Кроме того, точка продолжения, называемая *контрольной точкой* или *смещением*, должна находиться в точке пересечения, в которой произошел сбой **потребителя А**, или немного раньше этого.
4. **Получение событий.** Пока предыдущие три точки занимаются управлением потребителя, следует внедрить код, который принимает события и обрабатывает их, например, выполняет статистическую обработку и загрузку в хранилище больших двоичных объектов.

Вместо создания собственного решения Центры событий предоставляют эту функциональную возможность через интерфейс [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) и класс [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost).

## <a name="ieventprocessor-interface"></a>Интерфейс IEventProcessor

Во-первых, потребляющие приложения реализуют интерфейс [IEventProcessor ](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor), который имеет четыре метода: [OpenAsync, CloseAsync, ProcessErrorAsync и ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Этот интерфейс содержит фактический код для получения событий, отправляемых в Центры событий. Ниже приведен пример простой реализации:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Затем вам следует создать экземпляр экземпляра [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). В зависимости от перегрузки при создании экземпляра [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) в конструкторе используются следующие параметры:

- **hostName.** Имя каждого экземпляра потребителя. Каждый экземпляр **EventProcessorHost** должен иметь уникальное значение для этой переменной в группе потребителей, поэтому не жестко задать это значение.
- **eventHubPath.** Имя концентратора событий.
- **consumerGroupName.** Центры событий используют **$Default** как имя группы получателей по умолчанию, но рекомендуется создать группу потребителей для вашего определенного аспекта обработки.
- **eventHubConnectionString.** Строка подключения к концентратору событий, которую можно получить на портале Azure. Эта строка подключения должна иметь разрешение **Прослушивание** в концентраторе событий.
- **storageConnectionString**. Учетная запись хранения, которая используется для управления внутренними ресурсами.

Наконец, потребители регистрируют экземпляр [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) в службе Центров событий. Регистрация класса обработчика событий с экземпляром EventProcessorHost запускает обработку событий. Регистрация указывает службе Центров событий ожидать потребления событий из секций в приложение-потребителе, а также вызова кода реализации [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) всякий раз, когда события передаются для потребления. 


### <a name="example"></a>Пример

В качестве примера представьте, что существует 5 виртуальных машин, предназначенных для потребления событий, и простое консольное приложение, которое выполняет фактическое потребление. Затем каждое консольное приложение создает один экземпляр [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) и регистрирует его в службе Центров событий.

В этом примере сценария предположим, что 16 секций выделены для 5 экземпляров **EventProcessorHost**. Некоторые экземпляры **EventProcessorHost** могут иметь несколько секций. Для каждой секции, которая принадлежит экземпляру **EventProcessorHost**, он создает экземпляр класса `SimpleEventProcessor`. Таким образом, существует 16 экземпляров `SimpleEventProcessor` в целом, по одному, присвоенному каждой секции.

В следующем списке представлены эти данные.

- 16 секций концентраторов событий.
- 5 виртуальных машин, 1 приложение-потребитель (например, Consumer.exe) в каждой виртуальной машине.
- 5 зарегистрированных экземпляров узлов обработчика событий, по 1 на каждой виртуальной машине с Consumer.exe.
- 16 объектов `SimpleEventProcessor`, созданных 5 экземплярами узлов обработчика событий.
- 1 приложение Consumer.exe может содержать 4 объекта `SimpleEventProcessor`, так как одному экземпляру узла обработчика событий может принадлежать 4 секции.

## <a name="partition-ownership-tracking"></a>Отслеживание владения секциями

Владение секцией экземпляра EPH (или потребителя) отслеживается через учетную запись службы хранилища Azure, которая предоставляется для отслеживания. Вы можете визуализировать отслеживание в виде простой таблицы следующим образом. Можно определить фактическую реализацию, проверив большие двоичные объекты в указанной учетной записи хранения:

| **Имя группы потребителей** | **Код раздела** | **Имя узла (владелец)** | **Полученное время аренды (или владения)** | **Смещение в секции (контрольная точка)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Consumer\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consumer\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Consumer\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Consumer\_VM3 | 2018-04-15T01:22:56 | 976 |

Здесь каждый узел приобретает право владения секцией в течении определенного периода времени (длительность аренды). Если узел выйдет из строя (виртуальная машина завершит работу), значит истекает срок действия аренды. Попробуйте другие узлы для получения права владения секцией и один из узлов получит это право. Этот процесс повторно назначает аренду секции с новым владельцем. Таким образом, только один читатель может считывать данные из любой заданной секции в группе потребителей.

## <a name="receive-messages"></a>Получение сообщений

Каждый вызов [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) обеспечивает сбор событий. Пользователь управляет событиями самостоятельно. Если вы хотите убедиться, что узел обработчика обрабатывает каждое сообщение по крайней мере один раз, необходимо написать собственный код выполнения повторных попыток. Но учитывайте при этом возможность получения поврежденных сообщений.

Рекомендуется выполнять это относительно быстро, то есть делать как можно меньше обработки. Вместо этого используйте группы потребителей. Если необходимо выполнить запись в хранилище и выполнить некоторые маршрутизации, то лучше использовать две группы потребителей и имеют два [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) реализаций, работающих отдельно.

В какой-то момент во время обработки вам может потребоваться список того, что вы прочитали и завершили. Отслеживание важно в том случае, если необходимо перезапустить чтение, чтобы не вернуться в начало потока. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) упрощает отслеживание с помощью *контрольных точек*. Контрольная точка — это расположение или смещение (для данной секции в рамках определенной группы потребителей), которое поможет вам убедиться, что сообщение обработано. Пометка контрольной точки в **EventProcessorHost** достигается за счет вызова метода [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) объекта [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext). Эта операция выполняется в рамках метода [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync), но также может осуществляться в [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Контрольные точки

Метод [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) имеет две перегрузки. Первая — без параметров, контрольные точки наивысшего смещения событий внутри коллекции, возвращаемой [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Это смещение имеет метку "высокий уровень". Она предполагает, что вы обработали все последние события при ее вызове. Если вы используете этот метод таким образом, имейте в виду, что вы должны вызвать его после возврата другого кода обработки событий. Вторая перегрузка позволяет указать экземпляр [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) для контрольной точки. Этот метод позволяет использовать другой тип метки для контрольной точки. С помощью этой метки можно реализовать метку "низкого уровня". Была обработана наименьшая последовательность событий, в которых вы уверены. Эта перегрузка предусмотрена для обеспечения гибкости управления смещением.

Когда контрольная точка выполняется, файл JSON со сведениями специфики раздела (в частности, смещения) записывается в учетную запись хранения, указанную в конструкторе [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Этот файл постоянно обновляется. Крайне важно учитывать контрольную точку в контексте. Было бы неразумно проверять каждое сообщение. Учетная запись хранения, используемая для контрольной точки, вероятно, не будет обрабатывать эту нагрузку, но, что более важно, контрольная точка каждого отдельного события указывает, что для шаблона очереди сообщений лучшим вариантом будет очередь служебной шины, а не концентратор событий. Идея Центров событий заключается в том, что вы получаете доставку по принципу "хотя бы раз" в масштабе. Благодаря тому, что ваши идемпотентные нисходящие системы легко восстанавливаются после сбоев или перезапуска, это приводит к тому, что одни и те же события принимаются несколько раз.

## <a name="thread-safety-and-processor-instances"></a>Потокобезопасность и экземпляры процессора

По умолчанию [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) является потокобезопасным и ведет себя синхронно по отношению к экземпляру [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). При получении событий для секции [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) вызывается в экземпляре **IEventProcessor** для этой секции и блокирует последующие вызовы **ProcessEventsAsync**. Последующие сообщения и вызовы **ProcessEventsAsync** ставятся в очередь "за кулисами", в то время как конвейер сообщений продолжает выполнение в фоновом режиме в других потоках. Потокобезопасность устраняет потребность в потокобезопасной коллекции и значительно повышает производительность.

## <a name="shut-down-gracefully"></a>Корректное завершение работы

Наконец, [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) обеспечивает безошибочное завершение работы всех читателей секции и должен вызываться при завершении работы экземпляра [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Невыполнение этого требования может привести к задержкам при запуске других экземпляров **EventProcessorHost** через истечения срока действия аренды и конфликты эпохи. Управление эпохи подробно рассматривается в [эпохи](#epoch) статьи. 

## <a name="lease-management"></a>Управление арендой
Регистрация класса обработчика событий с экземпляром EventProcessorHost запускает обработку событий. Экземпляр узла получает аренды в некоторых секциях концентратора событий, при возможности захватывая некоторые из других экземпляров узлов таким образом, в результате которого равномерное распределение секций сходится во всех экземплярах узла. Для каждой выделенной секции экземпляр узла создает экземпляр предоставленного класса обработчика событий, а затем получает события из этого раздела и передает их в экземпляр обработчика событий. По мере добавления экземпляров и получения аренд EventProcessorHost со временем распределяет нагрузку среди всех потребителей.

Как упоминалось ранее, таблица отслеживания значительно упрощает автоматическое масштабирование [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Когда экземпляр **EventProcessorHost** запускается, он получает максимальное количество аренды и приступает к чтению событий. Когда срок аренды подходит к концу, **EventProcessorHost** пытается обновить его, разместив резервирование. Если продление аренды доступно, процессор продолжает чтение, но если это не так, читатель закрывается и вызывается [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync). При вызове **CloseAsync** самое время для выполнения окончательной очистки этой секции.

**EventProcessorHost** включает в себя свойство [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions). Это свойство обеспечивает контроль над управлением арендой. Задайте эти параметры перед регистрацией реализации [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

## <a name="control-event-processor-host-options"></a>Параметры управления узла обработчика событий

Кроме того, одна перегрузка [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) принимает объект [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) в качестве параметра. Этот параметр используется для управления поведением [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) определяет четыре свойства и одно событие:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize). Максимальный размер коллекции, которую вы хотите получать при вызове [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Этот размер не имеет минимального значения, только максимальное. Если принимается меньшее количество сообщений, **ProcessEventsAsync** выполняется только с доступным количеством.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount). Значение, используемое базовым каналом AMQP, чтобы определить верхнюю границу количества сообщений, полученных клиентом. Это значение должно быть больше или равно [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout). Если этот параметр имеет значение **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) вызывается, когда время базового вызова для получения событий в разделе истекает. Этот метод полезен для выполнения временных действий во время простоя в разделе.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider). Позволяет установить указатель на функцию или лямбда-выражение, которое вызывается для предоставления первоначального смещения, когда читатель приступает к чтению раздела. Без указания этого смещения средство чтения начинает с самого старого события, если только файл JSON со смещением уже не был сохранен в учетной записи хранения, предоставленной конструктору [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Этот метод полезен в тех случаях, когда вы хотите изменить поведение при запуске читателя. При вызове этого метода параметр объекта содержит идентификатор секции, для которой запущен читатель.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs). Позволяет получать уведомления о любых базовых исключениях, возникающих в [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Если что-то работает не так, как ожидалось, стоит начать с этого события.

## <a name="epoch"></a>Эпохи

Эпохи receive работает следующим образом:

### <a name="with-epoch"></a>С начала эпохи
Состояние: Уникальный идентификатор (значение начала эпохи), который служба использует для принудительного применения владения секции/аренды. Создать приемник на основе эпохи, с помощью [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) метод. Этот метод создает приемник на основе эпохи. Получатель будет создана определенную секцию концентратора событий из заданного объекта-получателя группы.

Компонент эпохи дает пользователям возможность убедитесь в наличии только одного получателя на группу потребителей в любой момент времени, со следующими правилами:

- Если нет существующих приемника на группу потребителей, пользователь может создать получатель с любым значением эпохи.
- Если получатель с начала эпохи значение e1 и e2 значение эпохи создается новый приемник где e1 < = e2, получатель с e1 будет автоматически отключен, получатель с e2 создан успешно.
- Если получатель с начала эпохи значение e1 и e2 значение эпохи создается новый приемник где e1 > e2, а затем создание e2 с ошибкой: Получатель с начала эпохи e1 уже существует.

### <a name="no-epoch"></a>Нет эпохи
Создать получатель не под управлением эпохи, с помощью [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) метод. 

В некоторых ситуациях в потоковой обработки, где пользователи хотели для создания нескольких получателей в одной группе потребителей. Для поддержки таких сценариев, у нас есть возможность создать получатель без эпохи, и в этом случае разрешается не более 5 параллельных получателей на группу потребителей.

### <a name="mixed-mode"></a>Смешанный режим
Не рекомендуется использование приложения, где создать получатель с начала эпохи и переключитесь эпохи нет или наоборот в одной группе потребителей. Однако в этом случае служба обрабатывает его с помощью следующих правил:

- Если получатель уже создана с начала эпохи e1 и активную передачу событий и новый приемник создается с помощью нет эпохи, создание нового получателя завершится ошибкой. Приемники эпохи всегда имеют приоритет в системе.
- Если получатель уже создан с начала эпохи e1 и получили отключен, и новый приемник создается с не эпохи на новый MessagingFactory, создание нового получателя завершится успешно. Здесь есть один нюанс здесь что наша система обнаруживает «отключение получателя», после около 10 минут.
- Если существует один или несколько получателей, созданные с помощью нет эпохи, а новый приемник создается с начала эпохи e1, отключении всех старых получателей.


## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знакомы с узлом обработчика событий, см. следующие статьи, чтобы узнать больше о Центрах событий:

* Начало работы с помощью [учебника по Центрам событий](event-hubs-dotnet-standard-getstarted-send.md)
* [Руководство по программированию Центров событий](event-hubs-programming-guide.md)
* [Доступность и согласованность в Центрах событий](event-hubs-availability-and-consistency.md)
* [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)
* [Azure Event Hubs samples](https://github.com/Azure/azure-event-hubs/tree/master/samples) (Примеры Центров событий Azure)
