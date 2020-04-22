---
title: Поймите поддержку концентратора Azure IoT am-P Документы Майкрософт
description: Руководство для разработчиков - поддержка устройств, подключенных к конечным точкам устройств IoT Hub и обслуживаемым услугам, с использованием протокола АМЗП. Включает информацию о встроенной поддержке АМЗП в SDK-устройства Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7b3dcfc51df7f0fe4291e9c5babccc1444ad32e9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730754"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Общайтесь с вашим концентратором IoT с помощью протокола АМЗП

Azure IoT Концентратор поддерживает [версию OASIS Advanced Message queuing Protocol (АМЗП) для](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) обеспечения различных функциональных возможностей через конечные точки, стоящие перед устройствами и обслуживающего обслуживание. В этом документе описывается использование клиентами Am-P для подключения к концентратору IoT для использования функциональности Концентратора IoT.

## <a name="service-client"></a>Сервисный клиент

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Подключение и аутентификации к концентратору IoT (клиент обслуживания)

Для подключения к концентратору IoT с помощью АМЗП клиент может использовать [систему безопасности на основе утверждений (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) или [простой аутентификации и уровня безопасности (SASL).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Клиенту службы требуется следующая информация:

| Сведения | Значение |
|-------------|--------------|
| Хост-хост-хост IoT | `<iot-hub-name>.azure-devices.net` |
| Имя ключа | `service` |
| Ключ доступа | Первичный или вторичный ключ, связанный с службой |
| Подписанный URL-адрес | Недолговечная общая подпись доступа в `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`следующем формате: . Чтобы получить код для создания этой [Control access to IoT Hub](./iot-hub-devguide-security.md#security-token-structure)подписи, см.

Следующий фрагмент кода использует [библиотеку uAM-P в Python](https://github.com/Azure/azure-uamqp-python) для подключения к концентратору IoT через ссылку отправителя.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Вызвать сообщения об лакта по устройству (клиент обслуживания)

Чтобы узнать об обмене сообщениями между службой и концентратором IoT, а также между устройством и концентратором IoT, [см.](iot-hub-devguide-messages-c2d.md) Клиент службы использует две ссылки для отправки сообщений и получения обратной связи для ранее отправленных сообщений с устройств, как описано в следующей таблице:

| Создан | Тип связи | Путь ссылки | Описание |
|------------|-----------|-----------|-------------|
| Служба | Ссылка отправителя | `/messages/devicebound` | Сообщения от облака к устройству, предназначенные для устройств, отправляются службой по этой ссылке. Сообщения, отправленные по `To` этой ссылке, имеют свое свойство, установленное на путь ссылки приемника целевого устройства, `/devices/<deviceID>/messages/devicebound`. |
| Служба | Ссылка на приемник | `/messages/serviceBound/feedback` | Сообщение обратной связи завершения, отвержения и отказа, поступают с устройств, полученных по этой ссылке службой. Для получения дополнительной информации о сообщениях обратной связи смотрите [Отправку сообщений от облака к устройству из концентратора IoT.](./iot-hub-devguide-messages-c2d.md#message-feedback) |

Следующий фрагмент кода демонстрирует, как создать сообщение об облаке к устройству и отправить его на устройство с помощью [библиотеки uAM-P в Python.](https://github.com/Azure/azure-uamqp-python)

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Чтобы получить обратную связь, клиент службы создает ссылку на приемник. Следующий фрагмент кода демонстрирует, как создать ссылку с помощью [библиотеки uAM-P в Python:](https://github.com/Azure/azure-uamqp-python)

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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

Как показано в предыдущем коде, сообщение обратной связи между облаками и устройствами имеет тип *содержимого приложения/vnd.microsoft.iothub.feedback.json*. Свойства в теле JSON сообщения можно сделать вывод о состоянии исходного сообщения:

* Ключ `statusCode` в органе обратной связи имеет одно из следующих значений: *Успех,* *Срок действия,* *DeliveryCountExceeded,* *Отклонено,* или *Очищено*.

* Ключ `deviceId` в корпусе обратной связи имеет идентификатор целевого устройства.

* Ключ `originalMessageId` в теле обратной связи имеет идентификатор исходного сообщения об облаке к устройству, отправленное службой. Этот статус доставки можно использовать для соотношения обратной связи с сообщениями от облака к устройству.

### <a name="receive-telemetry-messages-service-client"></a>Получение телеметрических сообщений (клиент обслуживания)

По умолчанию концентратор IoT хранит в встроенном концентраторе событий телеметрические сообщения устройств. Клиент службы может использовать протокол АМЗП для получения сохраненных событий.

Для этого клиенту службы сначала необходимо подключиться к конечной точке концентратора IoT и получить адрес перенаправления в встроенные концентраторы событий. Клиент службы использует предоставленный адрес для подключения к встроенному концентратору событий.

На каждом этапе клиент должен представить следующие части информации:

* Действительные учетные данные службы (сервис, общий токен подписи доступа).

* Хорошо отформатированный путь к разделу группы потребителей, из которого он намеревается получить сообщения. Для данной группы потребителей и идентификатора раздела путь имеет следующий формат: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (группа потребителей по умолчанию). `$Default`

* Дополнительная фильтрация предиката для обозначения отправной точки в разделе. Этот предикат может быть в виде номера последовательности, смещения или приуроченной к временной метке.

Следующий фрагмент кода использует [библиотеку uAM-P в Python,](https://github.com/Azure/azure-uamqp-python) чтобы продемонстрировать предыдущие шаги:

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
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Для идентификатора данного устройства концентратор IoT использует хэш идентификатора устройства, чтобы определить, в какой раздел хранить свои сообщения. Предыдущий фрагмент кода показывает, как события получаются от одного такого раздела. Однако обратите внимание, что обычному приложению часто требуется извлекать события, которые хранятся во всех разделах концентратора событий.

## <a name="device-client"></a>Клиент устройства

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Подключение и аутентификации к концентратору IoT (клиент устройства)

Для подключения к концентратору IoT с помощью АМЗП устройство может использовать [безопасность на основе утверждений (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) или [простой аутентификации и уровня безопасности (SASL).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Клиенту устройства требуется следующая информация:

| Сведения | Значение |
|-------------|--------------|
| Хост-хост-хост IoT | `<iot-hub-name>.azure-devices.net` |
| Ключ доступа | Первичный или вторичный ключ, связанный с устройством |
| Подписанный URL-адрес | Недолговечная общая подпись доступа в `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`следующем формате: . Чтобы получить код для создания этой [Control access to IoT Hub](./iot-hub-devguide-security.md#security-token-structure)подписи, см.

Следующий фрагмент кода использует [библиотеку uAM-P в Python](https://github.com/Azure/azure-uamqp-python) для подключения к концентратору IoT через ссылку отправителя.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Следующие пути соединения поддерживаются при операциях устройства:

| Создан | Тип связи | Путь ссылки | Описание |
|------------|-----------|-----------|-------------|
| Устройства | Ссылка на приемник | `/devices/<deviceID>/messages/devicebound` | Сообщения от облака к устройству, предназначенные для устройств, поступают по этой ссылке каждым устройством назначения. |
| Устройства | Ссылка отправителя | `/devices/<deviceID>/messages/events` | Сообщения от устройства к облаку, отправляемые с устройства, отправляются по этой ссылке. |
| Устройства | Ссылка отправителя | `/messages/serviceBound/feedback` | Обратная связь с сообщениями от облака к устройству, отправленная службе по этой ссылке устройствами. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Получение команд об лакони-устройства (клиент устройства)

Команды «облака к устройству», отправляемые на устройства, поступают по ссылке. `/devices/<deviceID>/messages/devicebound` Устройства могут получать эти сообщения в пакетах и при необходимости использовать полезную нагрузку данных сообщений, свойства сообщений, аннотации или свойства приложения.

Следующий фрагмент кода использует [библиотеку uAM-P в Python](https://github.com/Azure/azure-uamqp-python)) для получения сообщений об облаке к устройству с помощью устройства.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Отправка телеметрических сообщений (клиент устройства)

Вы также можете отправлять телеметрические сообщения с устройства с помощью АМЗП. Устройство может дополнительно предоставить словарь свойств приложения или различные свойства сообщений, такие как идентификатор сообщений.

Следующий фрагмент кода использует [библиотеку uAM-P в Python](https://github.com/Azure/azure-uamqp-python) для отправки сообщений от устройства от устройства.

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

* Соединения АМЗП могут быть нарушены из-за сбоя в сети или истечения срока действия маркера проверки подлинности (генерируемого в коде). Клиент службы должен справиться с этими обстоятельствами и восстановить соединение и ссылки, если это необходимо. Если срок действия маркера проверки подлинности истекает, клиент может избежать падения соединения, предварительно продлив токен до истечения срока его действия.

* Ваш клиент должен иногда быть в состоянии обрабатывать ссылки перенаправления правильно. Чтобы понять такую операцию, ознакомьтесь с документацией клиента am-P.

## <a name="next-steps"></a>Следующие шаги

Чтобы узнать больше о Протоколе [AMQP v1.0 specification](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)АМЗП, см.

Чтобы узнать больше о обмена сообщениями IoT Концентратор, см.:

* [Сообщения от облака до устройства](./iot-hub-devguide-messages-c2d.md)
* [Поддержка дополнительных протоколов](iot-hub-protocol-gateway.md)
* [Поддержка протокола телеметрии связи с сообщениями (МЗТТ)](./iot-hub-mqtt-support.md)
