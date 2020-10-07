---
title: включить файл
description: включить файл
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/03/2020
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: 9265caa3054cde6af311e655db2b2496524e8d24
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757162"
---
Начало работы со Службами коммуникации Azure с помощью клиентской библиотеки SMS Служб коммуникации Azure для Python для отправки SMS-сообщений.

Выполнение этого краткого руководства предполагает небольшую дополнительную плату в несколько центов США в учетной записи Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- [Python](https://www.python.org/downloads/) 2.7, 3.5 или более поздней версии.
- Активный ресурс Служб коммуникации и строка подключения. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- Номер телефона с поддержкой SMS-сообщений. [Получите номер телефона.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Проверка предварительных условий

- В окне терминала или команды выполните команду `python --version`, чтобы проверить, установлен ли пакет Python.
- Чтобы просмотреть номера телефонов, связанные с ресурсом Служб коммуникации, войдите на [портал Azure](https://portal.azure.com/), перейдите к ресурсу Служб коммуникации и откройте вкладку с **номерами телефонов** в области навигации слева.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-python-application"></a>Создание приложения Python

Откройте терминал или командное окно, создайте каталог для своего приложения и перейдите к нему.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Используйте текстовый редактор, чтобы создать файл с именем **send-sms.py** в корневом каталоге проекта и добавить структуру для программы, включая базовую обработку исключений. В следующих разделах показано, как добавить в этот файл весь исходный код для примера из этого краткого руководства.

```python
import os
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Установка пакета

Оставаясь в каталоге приложения, установите пакет клиентской библиотеки SMS Служб коммуникации для Python с помощью команды `pip install`.

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки SMS Служб коммуникации для Python.

| Имя                                  | Описание                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Этот класс требуется для реализации всех функций обмена текстовыми сообщениями. Его экземпляр можно создать на основе сведений о подписке и использовать для отправки SMS. |
| SendSmsOptions | Этот класс предоставляет параметры для настройки отчетов о доставке. Если enable_delivery_report имеет значение True, при успешной доставке будет создано событие. |

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте экземпляр **SmsClient** с использованием строки подключения. Приведенный ниже код извлекает строку подключения для ресурса из переменной среды с именем `COMMUNICATION_SERVICES_CONNECTION_STRING`. См. сведения о том, как [управлять строкой подключения ресурса](../../create-communication-resource.md#store-your-connection-string).

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>Отправка SMS-сообщения

Отправьте SMS-сообщение, вызвав метод Send. Добавьте следующий код в конец блока `try` в **send-sms.py**:

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

Необходимо изменить `<leased-phone-number>` на номер телефона с поддержкой SMS, связанный с ресурсом Служб коммуникации, и `<to-phone-number>` на номер телефона, на который вы хотите отправить сообщение. 

Параметр `send_sms_options` является необязательным. Его можно использовать для настройки отчетов о доставке. Это полезно, если вы хотите, чтобы при доставке SMS-сообщений создавались события. Сведения о настройке отчетов о доставке SMS-сообщений см. в кратком руководстве [Обработка событий SMS-сообщений](../handle-sms-events.md).

## <a name="run-the-code"></a>Выполнение кода

Запустите приложение из каталога приложения с помощью команды `python`.

```console
python send-sms.py
```
