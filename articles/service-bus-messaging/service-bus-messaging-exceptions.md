---
title: Сервисный автобус Azure - исключения для обмена сообщениями Документы Майкрософт
description: В этой статье приводится список исключений из сообщений Azure Service Bus и предложены действия, которые будут предприняты при возникновении исключения.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: d04902a8d53397b7e7d9712a1c75ce44cc7aa7ad
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880794"
---
# <a name="service-bus-messaging-exceptions"></a>Исключения обмена сообщениями служебной шины
В этой статье перечислены исключения .NET, генерируемые APIs .NET Framework. 

## <a name="exception-categories"></a>Категории исключений
API обмена сообщениями создают исключения, которые можно разделить на следующие категории с указанием связанного действия, предпринимаемого для их устранения. Значение и причины исключения могут различаться в зависимости от типа сущности обмена сообщениями.

1. Ошибка кодирования пользователей ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Общее действие: прежде чем продолжить, попытайтесь исправить код.
2. Ошибка настройки/конфигурации ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Общее действие: проверьте конфигурацию и измените ее при необходимости.
3. Временные исключения ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) Общее действие: повторите операцию или уведомите пользователей. Класс `RetryPolicy` в клиенте SDK может быть настроен для автоматической обработки повторов. Для получения дополнительной [Retry guidance](/azure/architecture/best-practices/retry-service-specific#service-bus)информации см.
4. Другие исключения ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.MessagingLostException , Microsoft.ServiceBus.Messaging.](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) Общее действие: специфичное для типа исключений; обратитесь к таблице в следующем разделе: 

## <a name="exception-types"></a>Типы исключений
В следующей таблице перечислены типы исключений обмена сообщениями и их причины, а также рекомендуемые действия, которые можно выполнять.

| **Тип исключения** | **Описание, причина, примеры** | **Предлагаемые действия** | **Примечание к автоматическому или немедленному повтору** |
| --- | --- | --- | --- |
| [Тайм-аут Исключение](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Сервер не ответил на запрашиваемую операцию в указанное время, которое контролируется [OperationTimeout.](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) Возможно, сервер выполнил запрошенную операцию. Это может произойти из-за задержек сети или другой инфраструктуры. |Проверьте состояние системы для обеспечения согласованности и при необходимости повторите попытку. Ознакомьтесь с разделом [Исключения времени ожидания](#timeoutexception). |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Запрашиваемые операции пользователя не допускаются на сервере или в службу. Подробные сведения см. в сообщении об исключении. Например, [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) генерирует это исключение, если сообщение было получено в режиме [ReceiveAndDelete.](/dotnet/api/microsoft.azure.servicebus.receivemode) |Проверьте код и обратитесь к документации. Убедитесь, что запрошенная операция допустима. |Повторная попытка не помогает. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Предпринята попытка вызвать операцию с объектом, который уже закрыт, прерван или удален. Иногда это означает, что внешняя транзакция уже удалена. |Проверьте код и убедитесь, что он не вызывает операции на объекте с утилизанием. |Повторная попытка не помогает. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Объект [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) не может приобрести токен, токен недействителен или токен не содержит требований, необходимых для операции. |Убедитесь, что поставщик маркеров создан с использованием правильных значений. Проверьте конфигурацию службы управления доступом. |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Для этого метода предоставлен один или несколько недопустимых аргументов.<br /> Универсальный код ресурса (URI), переданный в [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory), содержит сегменты пути.<br /> В [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) передана недопустимая схема универсального кода ресурса (URI). <br />Значение свойства превышает 32 КБ. |Проверьте вызывающий код и убедитесь, что аргументы заданы правильно. |Повторная попытка не помогает. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Сущность, связанная с операцией, не существует или она удалена. |Убедитесь, что сущность существует. |Повторная попытка не помогает. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Предпринята попытка получить сообщение с определенным порядковым номером. Это сообщение не найдено. |Убедитесь, что сообщение еще не получено. Проверьте очередь недоставленных сообщений, чтобы увидеть, не находится ли в ней сообщение. |Повторная попытка не помогает. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Клиент не может установить подключение к Service Bus. |Убедитесь, что имя узла указано правильно и узел доступен. |Повторная попытка может помочь при наличии периодических сбоев подключения. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Служба не в состоянии обработать запрос в данный момент. |Клиент может некоторое время подождать, после чего следует повторить операцию. |Клиент может повторить операцию через определенный промежуток времени. Если в результате повтора возникает другое исключение, проверьте поведение повтора этого исключения. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Срок действия маркера блокировки, связанный с сообщением, истек, или маркер блокировки не найден. |Удалите сообщение. |Повторная попытка не помогает. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Потеряна блокировка, связанная с данным сеансом. |Прервите объект [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Повторная попытка не помогает. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Универсальное исключение обмена сообщениями, порождаемое в следующих случаях.<p>Предпринята попытка создать [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) с использованием имени или пути, которые относятся к другому типу сущности (например, разделу).</p><p>Предпринята попытка отправить сообщение размером свыше 256 КБ. </p>Во время обработки запроса возникла ошибка сервера или службы. Подробные сведения см. в сообщении об исключении. Обычно это преходящее исключение.</p><p>Запрос был прекращен из-за того, что сущность задушена. Код ошибки: 50001, 50002, 50008. </p> | Проверьте код и убедитесь, что для текста сообщения применяются только сериализуемые объекты (или используйте настраиваемый сериализатор). <p>Обратитесь к документации для получения сведений о поддерживаемых типах значений свойств и используйте только поддерживаемые типы.</p><p> Проверьте свойство [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Если это **правда,** вы можете повторить операцию. </p>| Если исключение связано с регулированием, подождите несколько секунд и повторите операцию. Повторное поведение не определено и может не помочь в других сценариях.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Предпринята попытка создать сущность с именем, которое уже используется другой сущностью в этом пространстве имен службы. |Удалите существующую сущность или выберите другое имя для сущности, которую нужно создать. |Повторная попытка не помогает. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Сущность обмена сообщениями достигла максимально допустимого размера, или превышено максимально возможное количество подключений к пространству имен. |Создайте в сущности пространство, получая сообщения из сущности или ее подочередей. Смотрите [КвотАВизвиостыс .](#quotaexceededexception) |Повторная попытка может помочь, если сообщения были удалены. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Служебная шина возвращает это исключение, если вы пытаетесь создать недопустимое действие правила. Служебная шина вкладывает это исключение в недоставленное сообщение, если во время обработки действия правила для данного сообщения произошла ошибка. |Проверьте правильность действия правила. |Повторная попытка не помогает. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Служебная шина возвращает это исключение, если вы пытаетесь создать недопустимый фильтр. Служебная шина вкладывает это исключение в недоставленное сообщение, если во время обработки фильтра для данного сообщения произошла ошибка. |Проверьте правильность фильтра. |Повторная попытка не помогает. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Предпринята попытка принять сеанс с конкретным идентификатором сеанса, но сеанс в данный момент заблокирован другим клиентом. |Убедитесь, что сеанс разблокирован другими клиентами. |Повторная попытка может помочь, если сеанс был освобожден до этого времени. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |В состав транзакции входит слишком много операций. |Сократите количество операций, входящих в состав этой транзакции. |Повторная попытка не помогает. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Выполнен запрос на операцию среды выполнения с отключенной сущностью. |Активируйте сущность. |Повторная попытка может помочь, если сущность была активирована. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Служебная шина возвращает это исключение, если сообщение отправлено в раздел, в котором включена предварительная фильтрация, и ни один из фильтров не соответствует. |Убедитесь, что соответствует по меньшей мере один фильтр. |Повторная попытка не помогает. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Полезные данные сообщения превышают предел в 256 КБ. Предел в 256 КБ является общим размером сообщения с учетом системных свойств и служебных данных .NET. |Сократите размер полезных данных сообщения, а затем повторите операцию. |Повторная попытка не помогает. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Внешняя транзакция (*Transaction.Current*) является недопустимой. Возможно, она завершена или прервана. Дополнительные сведения можно найти во внутреннем исключении. | |Повторная попытка не помогает. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Предпринята попытка выполнить операцию с сомнительной транзакцией либо попытка зафиксировать транзакцию, и транзакция становится сомнительной. |Ваше приложение должно обрабатывать это исключение (как особый случай), так как транзакция уже может быть зафиксирована. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) указывает на превышение квоты для конкретного объекта.

### <a name="queues-and-topics"></a>Очереди и разделы
Для очередей и тем часто размер очереди. Свойство сообщения об ошибке содержит дополнительные сведения, как показано в следующем примере:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Сообщение уведомляет о том, что для раздела превышено ограничение размера, в данном случае 1 ГБ (ограничение по умолчанию). 

### <a name="namespaces"></a>Пространства имен

Для пространств имен исключение [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) может указывать на то, что приложение превысило максимально возможное количество подключений к пространству имен. Пример:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Основные причины
Существует две основные причины этой ошибки: очередь недоставленных сообщений и неработающие получатели сообщений.

1. **[Очередь из мертвых букв](service-bus-dead-letter-queues.md)** Читатель не завершает сообщения, и сообщения возвращаются в очередь/тему по истечении срока действия блокировки. Это может произойти, если читатель сталкивается с исключением, которое не позволяет ему звонить [по телефону BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). После 10 попыток считывания сообщения оно по умолчанию перемещается в очередь недоставленных сообщений. Это поведение управляется свойством [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) со значением по умолчанию "10". По мере накапливания сообщений в очереди недоставленных сообщений они начинают занимать много места.
   
    Чтобы устранить эту проблему, считайте и завершите сообщения в очереди недоставленных сообщений, как и в любой другой очереди. Вы можете использовать метод [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath), который помогает форматировать путь очереди недоставленных сообщений.
2. **Получатель остановлен**. Получатель прекратил прием сообщений из очереди или подписки. Определить эту проблему можно с помощью свойства [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails), которое выводит полную статистику по сообщениям. Если свойство [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) является высоким или растет, то сообщения не читаются так быстро, как они пишутся.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) указывает, что запущенная пользователем операция занимает больше времени, чем время ожидания операции. 

Следует проверить значение свойства [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), так как достижение этого ограничения также может породить исключение [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Очереди и разделы
Для очередей и разделов время ожидания указывается в свойстве [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) (как часть строки подключения) или с помощью [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Само сообщение об ошибке может отличаться, но оно всегда содержит значение времени ожидания, указанное для текущей операции. 

## <a name="next-steps"></a>Дальнейшие действия
Полные справочные материалы по API служебной шины для .NET доступны в [справочнике по API Azure для .NET](/dotnet/api/overview/azure/service-bus).
Советы по устранению [Troubleshooting guide](service-bus-troubleshooting-guide.md) неполадок см.
