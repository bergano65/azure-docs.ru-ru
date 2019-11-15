---
title: Краткое руководство. Использование разделов и подписок Служебной шины Azure с Python
description: Узнайте, как использовать разделы и подписки служебной шины Azure в Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 94a49b31139947c6323ab391b78ecd03ee911e0a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748504"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Краткое руководство. Использование разделов и подписок Служебной шины с Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этой статье описывается использование разделов и подписок Служебной шины Azure с Python. Пакет [SDK для Python][Azure Python package] используется в примерах в следующих целях: 

- Создание разделов и подписок на них
- Создание фильтров и правил подписки
- Отправка сообщений в разделы 
- Получение сообщений из подписок
- Удаление разделов и подписок

## <a name="prerequisites"></a>Предварительные требования
- Подписка Azure. Вы можете активировать [преимущества подписчика Visual Studio или MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) либо зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Пространство имен Служебной шины Azure, созданное с помощью инструкций в [руководстве по созданию раздела и подписок Служебной шины с помощью портала Azure](service-bus-quickstart-topics-subscriptions-portal.md). Скопируйте имя пространства имен, имя ключа общего доступа и значение первичного ключа из экрана **Политики общего доступа**. Эти данные понадобятся далее в этой статье. 
- Python 3.4x или более поздней версии с установленным пакетом [Azure SDK для Python][Azure Python package]. Дополнительные сведения см. в [руководстве по установке Python](/azure/python/python-sdk-azure-install).

## <a name="create-a-servicebusservice-object"></a>Создание объекта ServiceBusService

Объект **ServiceBusService** позволяет работать с разделами и подписками на них. Чтобы программным образом получить доступ к Служебной шине, добавьте в начало файла Python следующую строку:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Чтобы создать объект **ServiceBusService**, добавьте приведенный ниже код. Замените `<namespace>`, `<sharedaccesskeyname>` и `<sharedaccesskeyvalue>` именем пространства имен Службы шины, именем ключа подписанного URL-адреса (SAS) и значением первичного ключа. Эти значения можно найти на [портале Azure][Azure portal] в разделе **Политики общего доступа** в пространстве имен Служебной шины.

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Создание раздела

Метод `create_topic` в следующем коде позволяет создать раздел Служебной шины с именем `mytopic` и с параметрами по умолчанию:

```python
bus_service.create_topic('mytopic')
```

С помощью параметров можно переопределить настройки раздела по умолчанию, например срок жизни сообщения или максимальный размер раздела. Следующий код создает раздел с именем `mytopic` с максимальным размером 5 ГБ и сроком жизни сообщения, равным одной минуте:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Создание подписок

Для создания подписок на разделы также используется объект **ServiceBusService**. Подписка может иметь фильтр, который ограничивает набор сообщений, доставляемых в виртуальную очередь. Если не указать фильтр, новые подписки будут использовать фильтр **MatchAll** по умолчанию, который помещает все опубликованные в разделе сообщения в виртуальную очередь подписки. В следующем примере создается подписка на `mytopic` с именем `AllMessages` и используется фильтр по умолчанию **MatchAll**:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Использование фильтров с подписками

Метод `create_rule` объекта **ServiceBusService** позволяет фильтровать сообщения, отображаемые в подписке. При создании подписки можно указать правила. Кроме того, правила можно добавлять в имеющиеся подписки.

Наиболее гибкий тип фильтра — это **SqlFilter**. Он использует подмножество SQL-92. Фильтры SQL работают на основе свойств сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Поскольку фильтр **MatchAll** по умолчанию применяется автоматически ко всем новым подпискам, необходимо удалить его из подписок, которые нужно отфильтровать. В противном случае фильтр **MatchAll** переопределит любые другие указанные фильтры. Вы можете удалить правило по умолчанию с помощью метода `delete_rule` объекта **ServiceBusService**.

В приведенном ниже примере создается подписка на `mytopic` с именем `HighMessages` с правилом **SqlFilter** с именем `HighMessageFilter`. Правило `HighMessageFilter` выбирает только сообщения с настраиваемым свойством `messageposition` больше 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

В приведенном ниже примере создается подписка на `mytopic` с именем `LowMessages` с правилом **SqlFilter** с именем `LowMessageFilter`. Правило `LowMessageFilter` выбирает только сообщения со свойством `messageposition` меньшим или равным 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Если указаны правила `AllMessages`, `HighMessages` и `LowMessages`, сообщения, отправленные в `mytopic`, всегда доставляются в приемники подписки `AllMessages`. Сообщения также выборочно доставляются в подписку `HighMessages` или `LowMessages` (в зависимости от значения свойства `messageposition` сообщения). 

## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел

Приложения используют метод `send_topic_message` объекта **ServiceBusService** для отправки сообщений в раздел Служебной шины.

В приведенном ниже примере пять тестовых сообщений отправляются в раздел `mytopic`. Значение настраиваемого свойства `messageposition` зависит от итерации цикла и определяет подписки, которые получат это сообщение. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Квоты и ограничения на размер сообщений

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Число сообщений в разделе может быть любым, но действует ограничение на общий размер сообщений в разделе. Этот размер раздела, определяемый в момент ее создания, не должен превышать 5 ГБ. 

Дополнительные сведения о квотах см. в статье [Квоты на служебную шину][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки

Приложения используют метод `receive_subscription_message` объекта **ServiceBusService** для получения сообщений из подписки. Следующий пример получает сообщения из подписки `LowMessages` и удаляет их по мере их чтения:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Необязательный параметр `peek_lock` метода `receive_subscription_message` определяет, удаляет ли Служебная шина сообщения из подписки по мере их чтения. *PeekLock* — это режим получения сообщений по умолчанию (параметр `peek_lock` имеет значение **True**). В этом режиме сообщения читаются (просматриваются) и блокируются без удаления из подписки. После этого каждое сообщение необходимо явно завершить, чтобы удалить его из подписки.

Чтобы удалять сообщения из подписки по мере их чтения, для параметра `peek_lock` необходимо задать значение **False**, как показано в предыдущем примере. Удаление сообщений в ходе операции получения является самой простой моделью, которая работает только в том случае, если приложение допускает пропуск сообщений в случае сбоя. Чтобы понять это поведение, рассмотрим сценарий, в котором приложение выдает запрос на получение и выходит из строя до его обработки. Если сообщение было удалено при получении, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, полученное до сбоя.

Если приложение не допускает пропуска сообщений, процесс получения становится двухэтапной операцией. PeekLock находит следующее сообщение, блокирует его, чтобы другие получатели не могли его принять, а затем возвращает его приложению. После обработки или сохранения сообщения приложение завершает второй этап процесса получения, вызывая метод `complete` объекта **Message**.  Метод `complete` помечает сообщение как использованное и удаляет его из подписки.

В следующем примере демонстрируется сценарий блокировки:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Обработка сбоев приложения и нечитаемых сообщений

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если по какой-либо причине приложению-получателю не удается обработать сообщение, оно вызывает метод `unlock` для объекта **Message**. Служебная шина разблокирует сообщение в подписке и снова делает его доступным для получения в том же или другом приложении.

Кроме того, существует время ожидания сообщений, заблокированных в подписке. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Служебная шина автоматически разблокирует сообщение и снова сделает его доступным для получения.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода `complete`, сообщение будет повторно доставлено в приложение после его перезапуска. Такая реакция на событие часто называется *по крайней мере одна обработка*. Каждое приложение обрабатывается по крайней мере один раз, но в некоторых случаях одно и то же сообщение может быть доставлено повторно. Если сценарий не допускает обработки дубликатов, вы можете использовать свойство **MessageId** сообщения, которое остается постоянным при всех попытках доставки. Это позволяет обрабатывать повторную доставку сообщения. 

## <a name="delete-topics-and-subscriptions"></a>Удаление разделов и подписок

Удалить разделы и подписки можно на [портале Azure][Azure portal] или с помощью метода `delete_topic`. Следующий код удаляет раздел с именем `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

При удалении раздела удаляются все подписки на него. Подписки также можно удалять независимо друг от друга. Следующий код удаляет подписку с именем `HighMessages` из раздела `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

По умолчанию разделы и подписки сохраняются до тех пор, пока вы не удалите их. Для автоматического удаления подписок по истечении определенного периода времени в подписке можно задать параметр [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python). 

> [!TIP]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель Служебной шины позволяет без труда подключаться к пространству имен Служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта и возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Дополнительная информация

Вы узнали основные сведения о разделах Служебной шины. Для получения дополнительных сведений используйте следующие ссылки:

* [Очереди, разделы и подписки][Queues, topics, and subscriptions]
* Справочник по [SqlFilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
