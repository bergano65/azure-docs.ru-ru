---
title: Общие сведения о поддержке AMQP центра Интернета вещей Azure | Документация Майкрософт
description: Руководство разработчика. Поддержка устройств, подключаемых к центру Интернета вещей для устройств и обращенную к службе конечных точек, с помощью протокола AMQP. Содержит сведения о встроенной поддержке AMQP в пакеты SDK для устройств Интернета вещей Azure.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: c304c9b7fe02e3396d49aee0b70576071d9fac92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055381"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Взаимодействовать с центром Интернета вещей с помощью протокола AMQP

Центр Интернета вещей Azure поддерживает [OASIS Advanced Message Queuing Protocol (AMQP) версии 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) предоставлять разнообразные функциональные возможности через обращенную к устройству и обращенную к службе конечные точки. В этом документе описывается использование клиентов AMQP для подключения к центру Интернета вещей, чтобы использовать функции центра Интернета вещей.

## <a name="service-client"></a>Клиент службы

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Подключиться и выполнить аутентификацию в центре Интернета вещей (клиент службы)
Чтобы подключиться к центру Интернета вещей с помощью AMQP, клиент может использовать [безопасности на основе утверждений (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) или [Simple Authentication and Security Layer (SASL) проверки подлинности](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Следующие сведения являются обязательным для клиента службы.

| Информация | Значение | 
|-------------|--------------|
| Имя узла центра Интернета вещей | `<iot-hub-name>.azure-devices.net` |
| Имя ключа | `service` |
| Ключ доступа | Первичный или вторичный ключ, который связан со службой |
| Подписанный URL-адрес | Кратковременное подписанный URL-адрес в следующем формате: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Чтобы получить код для создания подписи, см. в разделе [управление доступом к центру Интернета вещей](./iot-hub-devguide-security.md#security-token-structure).

В следующем фрагменте кода используется [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python) для подключения к центру Интернета вещей через ссылку отправителя.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Вызвать сообщений из облака на устройство (клиент службы)
Дополнительные сведения о cloud-to-device обмена сообщениями между службой и центром Интернета вещей, а также между устройством и центром Интернета вещей, см. в разделе [отправки сообщений из облака на устройство из центра Интернета вещей](iot-hub-devguide-messages-c2d.md). Служба клиента использует две ссылки для отправки сообщений и получение ответа на ранее отправили сообщения от устройств, как описано в следующей таблице:

| Созданные | Тип ссылки | Путь ссылки | Описание |
|------------|-----------|-----------|-------------|
| Service | Ссылке отправителя | `/messages/devicebound` | Служба сообщений из облака на устройство, предназначенные для устройств отправляются эту ссылку. Сообщений, отправляемых через конкретную связь их `To` свойство, задайте путь к ссылке получателя целевого устройства, `/devices/<deviceID>/messages/devicebound`. |
| Service | Ссылке получателя | `/messages/serviceBound/feedback` | Завершения, прерывания и отклонения сообщений обратной связи, поступающие от устройств получено по этой ссылке службой. Дополнительные сведения о сообщений обратной связи, см. в разделе [отправки сообщений из облака на устройство из центра Интернета вещей](./iot-hub-devguide-messages-c2d.md#message-feedback). |

В следующем фрагменте кода показано, как создать сообщение из облака на устройство и отправить его на устройство с помощью [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Для получения отзывов, клиент службы создает ссылку получателя. В следующем фрагменте кода показано, как создать связь с помощью [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

Как показано в приведенном выше коде cloud to device отзыв на сообщение с типом содержимого *application/vnd.microsoft.iothub.feedback.json*. Чтобы определить состояние доставки исходного сообщения, можно использовать свойства в текст сообщения JSON:
* Ключ `statusCode` отзыва текст имеет одно из следующих значений: *Успех*, *истек срок действия*, *DeliveryCountExceeded*, *Отклонено*, или *очищено*.
* Ключ `deviceId` отзыва текст имеет идентификатор целевого устройства.
* Ключ `originalMessageId` отзыва текст имеет идентификатор исходного сообщения облака на устройство, отправленного службой. Это состояние доставки можно использовать для корреляции отзыв для сообщений из облака на устройство.

### <a name="receive-telemetry-messages-service-client"></a>Получение сообщений телеметрии (клиент службы)

По умолчанию центра Интернета вещей сохраняет полученные сообщения телеметрии в встроенных уведомлений. Клиент службы можно использовать протокол AMQP для отправки хранимых событий.

Для этой цели клиента службы сначала необходимо подключиться к конечной точке центра Интернета вещей и получить адрес перенаправления для концентраторов событий встроенных. Затем клиент службы использует предоставленный адрес для подключения к концентратору событий встроенных.

На каждом шаге клиенту необходимо предоставить следующие сведения:
* Учетные данные службы допустимым (подпись коллективного доступа службы).
* Неверный формат пути для секции группы потребителей, он должен получить сообщения от. Для данного клиента с Идентификатором группы и секции, путь имеет следующий формат: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (группа потребителей по умолчанию — `$Default`).
* Дополнительный предикат фильтрации для обозначения отправной точкой для секции. Этот предикат может быть в виде последовательности номер, смещение или количество помещенных в очередь метку времени.

В следующем фрагменте кода используется [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python) для демонстрации описанные выше шаги:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Для идентификатора данного устройства в центр Интернета вещей использует хэш-код устройства, чтобы определить какой секции сохранить свое сообщения в. В предыдущем фрагменте кода показано, как будут получены события из одного такого секции. Тем не менее Обратите внимание на то, что типичное приложение часто необходимо извлечь события, которые хранятся во всех секциях концентратора событий.


## <a name="device-client"></a>Клиент устройства

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Подключиться и выполнить аутентификацию в центре Интернета вещей (клиент устройства)
Чтобы подключиться к центру Интернета вещей с помощью AMQP, устройство может использовать [на основе утверждений (CBS) безопасности](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) или [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) проверки подлинности.

Следующие сведения являются обязательным для клиента устройства.

| Информация | Значение | 
|-------------|--------------|
| Имя узла центра Интернета вещей | `<iot-hub-name>.azure-devices.net` |
| Ключ доступа | Первичный или вторичный ключ, который связан с устройством |
| Подписанный URL-адрес | Кратковременное подписанный URL-адрес в следующем формате: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Чтобы получить код для создания подписи, см. в разделе [управление доступом к центру Интернета вещей](./iot-hub-devguide-security.md#security-token-structure).


В следующем фрагменте кода используется [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python) для подключения к центру Интернета вещей через ссылку отправителя.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Следующие пути ссылки поддерживают операции с устройством:

| Созданные | Тип ссылки | Путь ссылки | Описание |
|------------|-----------|-----------|-------------|
| Устройства | Ссылке получателя | `/devices/<deviceID>/messages/devicebound` | Сообщений из облака на устройство, предназначенные для устройств, принимаются по этой ссылке каждого целевого устройства. |
| Устройства | Ссылке отправителя | `/devices/<deviceID>messages/events` | Device-to-cloud сообщений, отправленных с устройства, отправляются через конкретную связь. |
| Устройства | Ссылке отправителя | `/messages/serviceBound/feedback` | Отзыв на сообщение облака на устройство отправляются в службу через конкретную связь с устройствами. |


### <a name="receive-cloud-to-device-commands-device-client"></a>Получать команды cloud to device (устройство клиента)
Прибыть облаком и устройством команд, отправленных на устройства `/devices/<deviceID>/messages/devicebound` ссылку. Устройства могут получать эти сообщения в виде пакетов и использовать полезные данные сообщения, свойства сообщения, заметки или свойства приложения в сообщении, при необходимости.

В следующем фрагменте кода используется [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python)) для получения сообщений из облака на устройство устройством.

```python
# ... 
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Отправить сообщения телеметрии (клиент устройства)
Также можно отправлять сообщения телеметрии с устройства с помощью AMQP. Устройство можно при необходимости указать словарь свойств, приложения или различные сообщения свойства, такие как идентификатор сообщения.

В следующем фрагменте кода используется [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python) для отправки сообщений с устройства в облако с устройства.


```python
# ... 
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Дополнительные замечания
* Подключения AMQP может оказаться невозможным из-за сбоя сети или истечения срока действия проверки подлинности маркера (созданных в коде). Клиент службы необходимо обрабатывать эти ситуации и заново установить соединение и ссылки, при необходимости. Маркер проверки подлинности после истечения срока действия клиента можно избежать сброса соединения, заранее обновив маркер до истечения срока действия.
* Ваш клиент иногда должен быть способен для правильной обработки перенаправлений ссылку. Чтобы понять, такая операция, см. в документации клиента AMQP.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о протоколе AMQP, см. в разделе [AMQP версии 1.0 спецификации](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Дополнительные сведения о центре Интернета вещей сообщений, см. в разделе:

* [Сообщения из облака на устройство](./iot-hub-devguide-messages-c2d.md)
* [Поддержка дополнительных протоколов](iot-hub-protocol-gateway.md)
* [Поддержка протокол управления очередью сообщений телеметрии транспорта (MQTT)](./iot-hub-mqtt-support.md)
