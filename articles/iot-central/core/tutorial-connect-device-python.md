---
title: Руководство. Подключение универсального клиентского приложения Python к Azure IoT Central | Документация Майкрософт
description: Из этого руководства вы узнаете, как разработчик устройства может подключить устройство, на котором выполняется клиентское приложение Python, к приложению Azure IoT Central. Чтобы создать шаблон устройства, вам нужно импортировать модель его возможностей и добавить к ней представления для взаимодействия с устройством.
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: tracking-python
ms.openlocfilehash: 30ceed388412f08e31b9c9b0c7ea6fdf2fed143e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607216"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Руководство по Создание клиентского приложения и его подключение к приложению Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Эта статья предназначена для создателей решений и разработчиков устройств.*

Из этого руководства вы узнаете, как разработчик устройства может подключить клиентское приложение Python к приложению Azure IoT Central. Приложение Python имитирует поведение устройства датчика окружающей среды. С помощью примера _модели возможностей устройства_ вы создадите _шаблон устройства_ в IoT Central. Затем вы добавите представления в шаблон устройства, чтобы разрешить оператору взаимодействовать с устройством.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * импорт модели возможностей устройства для создания шаблона устройства;
> * добавление значений по умолчанию и пользовательских представлений в шаблон устройства;
> * публикация шаблона устройства и добавление реального устройства в приложение IoT Central;
> * создание кода устройства Python и его выполнение для подключения к приложению IoT Central;
> * просмотр смоделированной телеметрии, которую отправляет устройство;
> * управление свойствами устройства через представления;
> * вызов синхронных и асинхронных команд для управления устройством.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Приложение Azure IoT Central, созданное на основе шаблона **Пользовательское приложение**. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
* Компьютер для разработки с установленным [Python](https://www.python.org/) 3.7 или более поздней версии. Вы можете запустить `python3 --version` в командной строке, чтобы проверить версию. Python доступен для разных операционных систем. В инструкциях в этом руководстве предполагается, что вы выполняете команду **python3** в командной строке Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Создание приложения Python

Ниже показано, как создать клиентское приложение Python, которое подключается к реальному устройству, добавленному в приложение. Приложение Python моделирует поведение реального устройства.

1. Перейдите к созданной ранее папке `environmental-sensor` в среде командной строки.

1. Чтобы установить обязательные библиотеки, выполните следующие команды:

    ```cmd
    pip install azure-iot-device
    ```

1. Создайте файл с именем **environmental_sensor.py** в папке `environmental-sensor`.

1. Добавьте следующие инструкции `import` в начало файла **environmental_sensor.py**:

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Добавьте следующую асинхронную функцию `main` и объявления переменных в файл:

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    Замените заполнители `{your Scope ID}`, `{your Device ID}` и `{your Primary Key}` записанными ранее значениями. В реальном приложении не следует прописывать в коде эти данные.

    Все приведенные ниже определения функций и код вложены в функцию `main`.

1. Добавьте следующие две функции в функцию `main`, чтобы зарегистрировать устройство и подключить его к приложению IoT Central. При регистрации используется служба подготовки устройств Azure:

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Добавьте следующую функцию в функцию `main` для отправки данных телеметрии в приложение IoT Central:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    Имена элементов телеметрии (`temp` и `humid`) должны совпадать с именами в шаблоне устройства.

1. Добавьте следующие функции в функцию `main` для обработки команд, вызываемых из приложения IoT Central:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    Имена команд (`blink`, `turnon`, `turnoff` и `rundiagnostics`) должны совпадать с именами в шаблоне устройства.

    Сейчас в IoT Central не используется схема ответа, определенная в модели возможностей устройства. При использовании синхронной команды полезные данные ответа могут включать любой допустимый текст JSON. При использовании асинхронной команды устройство должно немедленно вернуть ответ 202 с последующим обновлением передаваемого свойства по завершении работы. Обновление передаваемого свойства имеет следующий формат:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Оператор может просмотреть полезные данные ответа в журнале команд.

1. Добавьте следующие функции в функцию `main` для обработки обновлений свойств, отправляемых из приложения IoT Central:

    ```python
        async def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        settings = {
          'name': name_setting,
          'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
          while True:
            patch = await device_client.receive_twin_desired_properties_patch() # blocking
            to_update = patch.keys() & settings.keys()
            await asyncio.gather(
              *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
            )
    ```

    Когда оператор задает доступное для записи свойство в приложении IoT Central, приложение использует требуемое свойство двойника устройства для отправки значения на устройство. Затем устройство отправляет ответ, используя передаваемое свойство двойника устройства. Когда IoT Central получает значение передаваемого свойства, состояние в представлении свойства меняется на **синхронизировано**.

    Имена свойств (`name` и `brightness`) должны совпадать с именами в шаблоне устройства.

1. Добавьте следующие функции в функцию `main` для управления приложением:

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. Сохраните файл **environmental_sensor.py**.

## <a name="run-your-python-application"></a>Запуск приложения Python

Чтобы запустить клиентское приложение устройства, в среде командной строки введите следующую команду:

```cmd
python3 environmental_sensor.py
```

Вы увидите, как устройство подключается к приложению Azure IoT Central и начинает отправлять данные телеметрии:

![Выполнение клиентского приложения](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Вы можете увидеть, как устройство реагирует на команды и обновления свойств:

![Действия клиентского приложения](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы как разработчик устройства узнали о принципах создания устройств с помощью Node.js, ознакомьтесь со следующими руководствами:

* Узнайте, как [подключить реальное устройство MXChip IoT DevKit к приложению Azure IoT Central](./howto-connect-devkit.md).
* Прочитайте статью [Что такое шаблоны устройств?](./concepts-device-templates.md), чтобы узнать больше о роли шаблонов устройств при реализации кода устройства.
* Узнайте о [регистрации устройств с помощью IoT Central и безопасном подключении устройств к Azure IoT Central](./concepts-get-connected.md).

Если вы хотите продолжать работу с набором руководств по IoT Central, чтобы узнать больше о создании решении IoT Central, ознакомьтесь со следующим руководством:

> [!div class="nextstepaction"]
> [Создайте шаблон устройства шлюза](./tutorial-define-gateway-device-type.md)
