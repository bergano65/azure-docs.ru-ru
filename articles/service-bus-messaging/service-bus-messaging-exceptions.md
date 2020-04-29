---
title: Служебная шина Azure — исключения обмена сообщениями | Документация Майкрософт
description: В этой статье представлен список исключений обмена сообщениями служебной шины Azure и предлагаемых действий, которые необходимо выполнить при возникновении исключения.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80880794"
---
# <a name="service-bus-messaging-exceptions"></a>Исключения обмена сообщениями служебной шины
В этой статье перечислены исключения .NET, созданные .NET Framework API. 

## <a name="exception-categories"></a>Категории исключений
API обмена сообщениями создают исключения, которые можно разделить на следующие категории с указанием связанного действия, предпринимаемого для их устранения. Значение и причины исключения могут различаться в зависимости от типа сущности обмена сообщениями.

1. Ошибка кодирования пользователя ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Общее действие: прежде чем продолжить, попытайтесь исправить код.
2. Ошибка установки или конфигурации ([Microsoft. servicebus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Общее действие: проверьте конфигурацию и измените ее при необходимости.
3. Временные исключения ([Microsoft. servicebus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. servicebus. Messaging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. servicebus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Общее действие: повторите операцию или уведомите пользователей. `RetryPolicy` Класс в КЛИЕНТСКОМ пакете SDK можно настроить на автоматическую обработку повторных попыток. Дополнительные сведения см. в разделе [руководство по повторным попыткам](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Другие исключения ([System. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft. servicebus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. servicebus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Общее действие: зависит от типа исключения; см. таблицу в следующем разделе: 

## <a name="exception-types"></a>Типы исключений
В следующей таблице перечислены типы исключений обмена сообщениями и их причины, а также рекомендуемые действия, которые можно выполнять.

| **Тип исключения** | **Описание, причина, примеры** | **Предлагаемое действие** | **Примечание к автоматическому или немедленному повтору** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Сервер не ответил на запрошенную операцию в течение указанного времени, который управляется [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Возможно, сервер выполнил запрошенную операцию. Это может произойти из-за задержек сети или других инфраструктур. |Проверьте состояние системы для обеспечения согласованности и при необходимости повторите попытку. Ознакомьтесь с разделом [Исключения времени ожидания](#timeoutexception). |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Запрошенная пользовательская операция не разрешена в пределах сервера или службы. Подробные сведения см. в сообщении об исключении. Например, [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) создает это исключение, если сообщение было получено в режиме [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Проверьте код и обратитесь к документации. Убедитесь, что запрошенная операция допустима. |Повторная попытка не помогает. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Предпринята попытка вызвать операцию с объектом, который уже закрыт, прерван или удален. Иногда это означает, что внешняя транзакция уже удалена. |Проверьте код и убедитесь, что он не вызывает операции с удаленным объектом. |Повторная попытка не помогает. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Объекту [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) не удалось получить маркер, токен является недопустимым, или маркер не содержит утверждений, необходимых для выполнения операции. |Убедитесь, что поставщик маркеров создан с использованием правильных значений. Проверьте конфигурацию службы контроля доступа. |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Для этого метода предоставлен один или несколько недопустимых аргументов.<br /> Универсальный код ресурса (URI), переданный в [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory), содержит сегменты пути.<br /> В [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) передана недопустимая схема универсального кода ресурса (URI). <br />Значение свойства превышает 32 КБ. |Проверьте вызывающий код и убедитесь, что аргументы заданы правильно. |Повторная попытка не помогает. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Сущность, связанная с операцией, не существует или была удалена. |Убедитесь, что сущность существует. |Повторная попытка не помогает. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Предпринята попытка получить сообщение с определенным порядковым номером. Это сообщение не найдено. |Убедитесь, что сообщение еще не получено. Проверьте очередь недоставленных сообщений, чтобы увидеть, не находится ли в ней сообщение. |Повторная попытка не помогает. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Клиент не может установить подключение к служебной шине. |Убедитесь, что имя узла указано правильно и узел доступен. |Повторная попытка может помочь при наличии периодических сбоев подключения. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |В данный момент служба не может обработать запрос. |Клиент может некоторое время подождать, после чего следует повторить операцию. |Клиент может повторить операцию через определенный промежуток времени. Если в результате повтора возникает другое исключение, проверьте поведение повтора этого исключения. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Срок действия маркера блокировки, связанного с сообщением, истек, или маркер блокировки не найден. |Удалите сообщение. |Повторная попытка не помогает. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Потеряна блокировка, связанная с данным сеансом. |Прервите объект [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Повторная попытка не помогает. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Универсальное исключение обмена сообщениями, порождаемое в следующих случаях.<p>Предпринята попытка создать [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) с использованием имени или пути, которые относятся к другому типу сущности (например, разделу).</p><p>Предпринята попытка отправить сообщение размером свыше 256 КБ. </p>Во время обработки запроса возникла ошибка сервера или службы. Подробные сведения см. в сообщении об исключении. Обычно это временное исключение.</p><p>Запрос был завершен, так как выполняется регулирование сущности. Код ошибки: 50001, 50002, 50008. </p> | Проверьте код и убедитесь, что для текста сообщения применяются только сериализуемые объекты (или используйте настраиваемый сериализатор). <p>Обратитесь к документации для получения сведений о поддерживаемых типах значений свойств и используйте только поддерживаемые типы.</p><p> Проверьте свойство [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Если **это так, можно**повторить операцию. </p>| Если исключение вызвано регулированием, подождите несколько секунд и повторите операцию. Поведение повторных попыток не определено и может не помочь в других сценариях.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Предпринята попытка создать сущность с именем, которое уже используется другой сущностью в этом пространстве имен службы. |Удалите существующую сущность или выберите другое имя для сущности, которую нужно создать. |Повторная попытка не помогает. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Сущность обмена сообщениями достигла максимально допустимого размера, или превышено максимально возможное количество подключений к пространству имен. |Создайте в сущности пространство, получая сообщения из сущности или ее подочередей. См. [QuotaExceededException](#quotaexceededexception). |Повторная попытка может помочь, если сообщения были удалены. |
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
Для очередей и разделов часто бывает размер очереди. Свойство сообщения об ошибке содержит дополнительные сведения, как показано в следующем примере:

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

1. **[Очередь недоставленных сообщений](service-bus-dead-letter-queues.md)** Модуль чтения не может завершить сообщения, и сообщения возвращаются в очередь или раздел по истечении срока действия блокировки. Это может произойти, если средство чтения встречает исключение, препятствующее вызову [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). После 10 попыток считывания сообщения оно по умолчанию перемещается в очередь недоставленных сообщений. Это поведение управляется свойством [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) со значением по умолчанию "10". По мере накапливания сообщений в очереди недоставленных сообщений они начинают занимать много места.
   
    Чтобы устранить эту проблему, считайте и завершите сообщения в очереди недоставленных сообщений, как и в любой другой очереди. Вы можете использовать метод [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath), который помогает форматировать путь очереди недоставленных сообщений.
2. **Получатель остановлен**. Получатель прекратил прием сообщений из очереди или подписки. Определить эту проблему можно с помощью свойства [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails), которое выводит полную статистику по сообщениям. Если свойство [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) имеет значение High или растет, то сообщения не считываются так быстро, как они записываются.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) указывает, что запущенная пользователем операция занимает больше времени, чем время ожидания операции. 

Следует проверить значение свойства [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), так как достижение этого ограничения также может породить исключение [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Очереди и разделы
Для очередей и разделов время ожидания указывается в свойстве [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) (как часть строки подключения) или с помощью [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Само сообщение об ошибке может отличаться, но оно всегда содержит значение времени ожидания, указанное для текущей операции. 

## <a name="next-steps"></a>Дальнейшие шаги
Полные справочные материалы по API служебной шины для .NET доступны в [справочнике по API Azure для .NET](/dotnet/api/overview/azure/service-bus).
Советы по устранению неполадок см. в разделе [руководство по устранению неполадок](service-bus-troubleshooting-guide.md)
