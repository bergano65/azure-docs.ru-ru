---
title: Планирование заданий с помощью Центра Интернета вещей Azure (Python) | Документация Майкрософт
description: Планирование заданий с помощью Центра Интернета вещей Azure для вызова прямого метода на нескольких устройствах. Используйте пакеты SDK для Центра Интернета вещей Azure для Python, чтобы реализовать приложения имитации устройства и приложение службы, на которых будет выполнено задание.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: c15db0766da3b4c18c306106ffdd5fc75a9143aa
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608815"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Планирование и трансляция заданий (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Центр Интернета вещей Azure — это полностью управляемая служба, которая позволяет внутреннему приложению создавать и отслеживать задания, осуществляющие планирование и обновление миллионов устройств.  Задания можно использовать для следующих действий:

* Обновление требуемых свойств
* Обновление тегов
* Вызов прямых методов

По сути, задание включает одно из этих действий, отслеживая ход его выполнения на наборе устройств (определяется запросом двойника устройства).  Например, с помощью задания внутреннее приложение может вызывать метод перезагрузки на 10 000 устройств, определенных запросом двойника устройства и запланированных в будущем.  Затем это приложение может отследить ход выполнения задания по мере получения и выполнения метода Reboot на каждом из этих устройств.

Дополнительные сведения о каждой из этих возможностей см. в следующих статьях:

* Двойник устройства и свойства: [Начало работы с двойниками устройств](iot-hub-python-twin-getstarted.md) и [руководства: Практическое использование свойств двойников устройств](tutorial-device-twins.md)

* Прямые методы: [Руководство разработчика для центра Интернета вещей — прямые методы](iot-hub-devguide-direct-methods.md) и [учебник: прямые методы](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

В этом учебнике описаны следующие процедуры.

* Создание приложения имитированного устройства Python с прямым методом, который позволяет выполнить действие **lockDoor** путем вызова из серверной части решения.

* Создание консольного приложения для Python, которое с помощью задания вызывает в приложении имитации устройства прямой метод **lockDoor** и обновляет требуемые свойства с помощью задания устройства.

По завершении работы с этим руководством у вас будет два приложения Python.

**simDevice.py**, которое подключается к Центру Интернета вещей с удостоверением устройства и получает прямой метод **lockDoor**.

**scheduleJobService.py**, которое вызывает прямой метод в приложении имитации устройства и обновляет требуемые свойства двойника устройства с помощью задания.

Для работы с этим учебником требуется:

* [Python 2.x или 3.x](https://www.python.org/downloads/). Обязательно используйте 32-разрядную или 64-разрядную версию установки согласно требованиям программы настройки. При появлении запроса во время установки обязательно добавьте Python в переменную среды соответствующей платформы. Если вы используете Python 2.x, может потребоваться [установка или обновление *pip* — системы управления пакетами Python](https://pip.pypa.io/en/stable/installing/).

* Если вы работаете с ОС Windows, потребуется [распространяемый пакет Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145), чтобы разрешить использовать собственные библиотеки DLL из Python.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

> [!NOTE]
> **Пакет SDK Центра Интернета вещей Azure для Python** непосредственно не поддерживает функциональную возможность **Задания**. Вместо этого в рамках этого руководства предлагается использовать альтернативное решение путем применения асинхронных потоков и таймеров. Последующие выпуски обновлений см. в списке компонентов **клиентских пакетов SDK** на [этой](https://github.com/Azure/azure-iot-sdk-python) странице. 
>

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе вы создадите консольное приложение Python, отвечающее на прямой метод, вызванный из облака, который активирует имитированный метод **lockDoor**.

1. В командной строке выполните следующую команду, чтобы установить пакет **azure-iot-device-client**.

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. В текстовом редакторе создайте файл **simDevice.py** в рабочей папке.

3. Добавьте следующие инструкции и переменные `import` в начало файла **simDevice.py**. Замените `deviceConnectionString` строкой подключения созданного ранее устройства.

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Добавьте следующую функцию обратного вызова для обработки метода **lockDoor**.

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Добавьте другую функцию обратного вызова для обработки обновлений двойников устройства.

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Добавьте следующий код для регистрации обработчика для метода **lockDoor**. Добавьте также подпрограмму `main`.

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. Сохраните и закройте файл **simDevice.py**.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Планирование заданий для вызова прямого метода и обновления свойств двойника устройства

В этом разделе создается консольное приложение Python, которое инициирует удаленное действие **lockDoor** на устройстве с помощью прямого метода и обновляет свойства двойника устройства.

1. В командной строке выполните следующую команду, чтобы установить пакет **azure-iot-service-client**.

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

2. В текстовом редакторе создайте файл **scheduleJobService.py** в рабочей папке.

3. Добавьте следующие инструкции и переменные `import` в начало файла **scheduleJobService.py**.

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Добавьте следующую функцию, которая используется для отправки запросов на устройства.

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. Добавьте следующие методы для выполнения заданий, вызывающих прямой метод и двойник устройств.

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Добавьте следующий код, чтобы запланировать задания и обновить их состояние. Добавьте также подпрограмму `main`.

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Сохраните и закройте файл **scheduleJobService.py**.

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке в рабочей папке выполните следующую команду, чтобы начать прослушивание прямого метода перезагрузки.

    ```cmd/sh
    python simDevice.py
    ```

2. В командной строке в рабочей папке выполните следующую команду, чтобы активировать задачи для блокировки дверей и обновления двойника.
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. В консоли отобразятся ответы устройства на прямой метод и двойники устройства.

    ![Пример заданий с помощью центра Интернета вещей 1--выходные данные устройства](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Выходные данные устройства IoT Hub Job образец 2:](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике описано использование задания для планирования прямого метода на устройстве и обновления свойств двойника устройства.

Чтобы продолжить знакомство с центром Интернета вещей и шаблонами управления устройствами, такими как удаленное обновление встроенного по воздуху по, см. в разделе [как обновление встроенного по](tutorial-firmware-update.md).