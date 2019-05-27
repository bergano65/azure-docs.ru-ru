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
ms.openlocfilehash: f39f184bdc09677e347a2691351309dd6483f467
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965392"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Взаимодействовать с центром Интернета вещей с помощью протокола AMQP

Центр Интернета вещей поддерживает [AMQP версии 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) предоставлять разнообразные функциональные возможности через обращенную к устройству и обращенную к службе конечные точки. В этом документе описывается использование AMQP клиентам подключаться к центру Интернета вещей, чтобы использовать функциональные возможности центра Интернета вещей.

## <a name="service-client"></a>Клиент службы

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Подключение и проверка подлинности в центре Интернета вещей (клиент службы)
Чтобы подключиться к центру Интернета вещей с помощью AMQP, клиент может использовать [безопасности на основе утверждений (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) или [Simple Authentication and Security Layer (SASL) проверки подлинности](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Следующие сведения являются обязательным для клиента службы.

| Информация | Value | 
|-------------|--------------|
| Имя узла Центра Интернета вещей | `<iot-hub-name>.azure-devices.net` |
| Имя ключа | `service` |
| Ключ доступа | Первичный или вторичный ключ, связанный со службой |
| Подписанный URL-адрес | Краткосрочные Подписи в следующем формате: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (код для создания этой подписи можно найти [здесь](./iot-hub-devguide-security.md#security-token-structure)).


Фрагмент кода ниже используется [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python) для подключения к центру Интернета вещей через ссылку отправителя.

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

### <a name="invoking-cloud-to-device-messages-service-client"></a>Вызов сообщений из облака на устройство (клиент службы)
Cloud-to-device обмена сообщениями между службой и центром Интернета вещей, а также между устройством и центром Интернета вещей описан [здесь](iot-hub-devguide-messages-c2d.md). Клиент службы использует две ссылки, описанных ниже, чтобы отправлять сообщения и получать отзывы для ранее отправленных сообщений с устройств.

| Кем создано | Тип ссылки | Путь ссылки | Описание |
|------------|-----------|-----------|-------------|
| Service | Ссылке отправителя | `/messages/devicebound` | C2D сообщения, предназначенные для устройств отправляются службой эту ссылку. Сообщений, отправляемых через конкретную связь их `To` свойство, задайте путь к ссылке получателя целевого устройства: т. е. `/devices/<deviceID>/messages/devicebound`. |
| Service | Ссылке получателя | `/messages/serviceBound/feedback` | Завершение, прерывания и отклонения сообщений обратной связи, поступающие от устройств, полученных службой по этой ссылке. Дополнительные сведения о сообщений обратной связи, см. в разделе [здесь](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Фрагмент кода ниже показано, как создать сообщение C2D и отправить их в устройства с помощью [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

Для получения отзывов, клиент службы создает ссылку получателя. Фрагмент кода ниже показано, как это сделать с помощью [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python).

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
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

Как показано выше, отзыв на сообщение C2D имеет тип содержимого `application/vnd.microsoft.iothub.feedback.json` и определить состояние доставки исходного сообщения можно использовать свойства в его тексте JSON:
* Ключ `statusCode` отзывы текст имеет одно из следующих значений: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Ключ `deviceId` отзывы текст имеет идентификатор целевого устройства.
* Ключ `originalMessageId` отзывы текст имеет идентификатор исходное сообщение C2D, отправленных службой. Это может использоваться для корреляции отзывы на сообщения C2D.

### <a name="receive-telemetry-messages-service-client"></a>Получение сообщений телеметрии (клиент службы)
По умолчанию центр Интернета вещей сохраняет полученные сообщения телеметрии в встроенных концентратор событий. Клиент службы можно использовать протокол AMQP для отправки хранимых событий.

Для этой цели клиента службы сначала необходимо подключиться к конечной точке центра Интернета вещей и получить адрес перенаправления для встроенных концентраторов событий. Затем клиент службы использует предоставленный адрес для подключения к встроенных концентратор событий.

На каждом шаге клиенту необходимо предоставить следующие сведения:
* Учетные данные службы допустимым (токен SAS службы).
* Неверный формат пути для секции группы потребителей, он должен получить сообщения от. Для данного клиента с Идентификатором группы и секции, путь имеет следующий формат: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (группа потребителей по умолчанию — `$Default`).
* Дополнительный предикат фильтрации для обозначения отправной точки в раздел (это может быть в виде метки времени порядковый номер, смещение или количество помещенных в очередь).

Фрагмент кода ниже используется [uAMQP библиотеки на языке Python](https://github.com/Azure/azure-uamqp-python) для демонстрации описанные выше действия.

```python
import json
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified using endpiont_filter
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

Для идентификатора данного устройства центр Интернета вещей использует хэш-код устройства, чтобы определить какой секции сохранить свое сообщения в. Приведенный выше фрагмент кода демонстрирует прием событий из одной такой секции. Обратите внимание, что типичное приложение часто необходимо извлечь события из всех разделов концентратора событий.


### <a name="additional-notes"></a>Дополнительные замечания
* Подключения AMQP может быть прервана из-за сетевых сбоев или истечения срока действия токена проверки подлинности (созданных в коде). Клиент службы необходимо обрабатывать эти ситуации и повторно установить подключение и ссылки, при необходимости. В случае истечения срока действия маркера проверки подлинности клиента можно также заранее продлила до истечения его срока, чтобы избежать сброса соединения.
* В некоторых случаях клиент должен быть правильно обрабатывать перенаправления ссылки. Обратитесь к документации клиента AMQP на способ обработки данной операции.

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>Получать сообщения cloud to device (устройство и модуль клиента)
Ниже приведены ссылки AMQP, используемые на стороне устройства.

| Кем создано | Тип ссылки | Путь ссылки | Описание |
|------------|-----------|-----------|-------------|
| Устройства | Ссылке получателя | `/devices/<deviceID>/messages/devicebound` | C2D сообщения, предназначенного для устройств, принимаются по этой ссылке каждого целевого устройства. |
| Устройства | Ссылке отправителя | `/messages/serviceBound/feedback` | Отзыв на сообщение C2D отправляемые службе через конкретную связь с устройствами. |
| модули | Ссылке получателя | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | C2D сообщения, предназначенные для модулей, принимаются по этой ссылке каждого модуля назначения. |
| модули | Ссылке отправителя | `/messages/serviceBound/feedback` | Отзыв на сообщение C2D отправляемые службе через конкретную связь модулями. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о протоколе AMQP, см. в разделе [AMQP версии 1.0 спецификации](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Дополнительные сведения о центре Интернета вещей сообщений, см. в разделе:

* [Сообщения из облака на устройство](./iot-hub-devguide-messages-c2d.md)
* [Поддержка дополнительных протоколов](iot-hub-protocol-gateway.md)
* [Поддержка протокола MQTT](./iot-hub-mqtt-support.md)
