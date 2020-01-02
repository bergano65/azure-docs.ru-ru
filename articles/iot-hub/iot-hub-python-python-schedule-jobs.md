---
title: Планирование заданий с помощью Центра Интернета вещей Azure (Python) | Документация Майкрософт
description: Планирование заданий с помощью Центра Интернета вещей Azure для вызова прямого метода на нескольких устройствах. Используйте пакеты SDK для Центра Интернета вещей Azure для Python, чтобы реализовать приложения имитации устройства и приложение службы, на которых будет выполнено задание.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: c424c18538a4e428c0e713bb814c2febe28d2d04
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555573"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Планирование и трансляция заданий (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Центр Интернета вещей Azure — это полностью управляемая служба, которая позволяет внутреннему приложению создавать и отслеживать задания, осуществляющие планирование и обновление миллионов устройств.  Задания можно использовать для следующих действий:

* Обновление требуемых свойств
* Обновление тегов
* Вызов прямых методов

По сути, задание включает одно из этих действий, отслеживая ход его выполнения на наборе устройств (определяется запросом двойника устройства).  Например, с помощью задания внутреннее приложение может вызывать метод перезагрузки на 10 000 устройств, определенных запросом двойника устройства и запланированных в будущем.  Затем это приложение может отследить ход выполнения задания по мере получения и выполнения метода Reboot на каждом из этих устройств.

Дополнительные сведения о каждой из этих возможностей см. в следующих статьях:

* Двойники устройств и свойства: [Начало работы с двойниками устройств](iot-hub-python-twin-getstarted.md) и [Руководство. Настройка устройств из внутренней службы](tutorial-device-twins.md).

* Прямые методы: [Руководство разработчика для центра Интернета вещей. прямые методы](iot-hub-devguide-direct-methods.md) и [учебник. прямые методы](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

В этом учебнике описаны следующие процедуры.

* Создание приложения имитированного устройства Python с прямым методом, который позволяет выполнить действие **lockDoor** путем вызова из серверной части решения.

* Создание консольного приложения для Python, которое с помощью задания вызывает в приложении имитации устройства прямой метод **lockDoor** и обновляет требуемые свойства с помощью задания устройства.

По завершении работы с этим руководством у вас будет два приложения Python.

**simDevice.py**, которое подключается к Центру Интернета вещей с удостоверением устройства и получает прямой метод **lockDoor**.

**scheduleJobService.py**, которое вызывает прямой метод в приложении имитации устройства и обновляет требуемые свойства двойника устройства с помощью задания.

> [!NOTE]
> **Пакет SDK Центра Интернета вещей Azure для Python** непосредственно не поддерживает функциональную возможность **Задания**. Вместо этого в рамках этого руководства предлагается использовать альтернативное решение путем применения асинхронных потоков и таймеров. Последующие выпуски обновлений см. в списке компонентов **клиентских пакетов SDK** на [этой](https://github.com/Azure/azure-iot-sdk-python) странице.
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Создание центра IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе вы создадите консольное приложение Python, отвечающее на прямой метод, вызванный из облака, который активирует имитированный метод **lockDoor**.

1. В командной строке выполните следующую команду, чтобы установить пакет **Azure-IOT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. В текстовом редакторе создайте файл **simDevice.py** в рабочей папке.

3. Добавьте следующие инструкции и переменные `import` в начало файла **simDevice.py**. Замените `deviceConnectionString` строкой подключения созданного ранее устройства.

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Добавьте следующую функцию обратного вызова для обработки метода **lockDoor**.

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Добавьте другую функцию обратного вызова для обработки обновлений двойников устройства.

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Добавьте следующий код для регистрации обработчика для метода **lockDoor**. Добавьте также подпрограмму `main`.

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Сохраните и закройте файл **simDevice.py**.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

В этой статье вы создадите серверную службу, которая вызывает прямой метод на устройстве и обновляет двойника устройства. Службе требуется разрешение **Connect** для вызова прямого метода на устройстве. Службе также требуются разрешения на **Чтение реестра** и **запись в реестр** для чтения и записи реестра удостоверений. Политика общего доступа по умолчанию, которая содержит только эти разрешения, не существует, поэтому ее необходимо создать.

Чтобы создать политику общего доступа, которая предоставляет разрешения на подключение **к службе**, **Чтение реестра**и **запись в реестр** , а затем получите строку подключения для этой политики, выполните следующие действия.

1. Откройте центр Интернета вещей в [портал Azure](https://portal.azure.com). Самый простой способ открыть центр Интернета вещей — выбрать **группы ресурсов**, выбрать группу ресурсов, в которой находится центр Интернета вещей, а затем выбрать центр Интернета вещей в списке ресурсов.

2. В левой части центра Интернета вещей выберите **политики общего доступа**.

3. В верхнем меню над списком политик выберите **Добавить**.

4. В области **Добавить политику общего доступа** введите описательное имя политики. Например: *сервицеандрегистриреадврите*. В разделе **разрешения**выберите **Подключение к службе** и **запись в реестр** (**Чтение реестра** будет автоматически выбрано при выборе **записи в реестр**). Щелкните **Создать**.

    ![Покажите, как добавить новую политику общего доступа](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. Вернитесь на панель **политики общего доступа** и выберите в списке политик новую политику.

6. В разделе **ключи общего доступа**выберите значок копирования для **строки подключения — первичный ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Дополнительные сведения о политиках и разрешениях общего доступа центра Интернета вещей см. в разделе [Управление доступом и разрешения](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Планирование заданий для вызова прямого метода и обновления свойств двойника устройства

В этом разделе создается консольное приложение Python, которое инициирует удаленное действие **lockDoor** на устройстве с помощью прямого метода и обновляет свойства двойника устройства.

1. В командной строке выполните следующую команду, чтобы установить пакет **azure-iot-service-client**.

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Пакет PIP для Azure-iothub-Service-Client в настоящее время доступен только для ОС Windows. Сведения для Linux и Mac OS см. в разделах, посвященных Linux и Mac OS, в разделе [Подготовка среды разработки для Python](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) POST.
   >

2. В текстовом редакторе создайте файл **scheduleJobService.py** в рабочей папке.

3. Добавьте следующие `import`ные операторы и переменные в начало файла **scheduleJobService.py** . Замените заполнитель `{IoTHubConnectionString}` строкой подключения центра Интернета вещей, скопированным ранее в поле [Получение строки подключения для центра Интернета вещей](#get-the-iot-hub-connection-string). Замените заполнитель `{deviceId}` ИДЕНТИФИКАТОРом устройства, зарегистрированным в окне [Регистрация нового устройства в центре Интернета вещей](#register-a-new-device-in-the-iot-hub):

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

    ![Пример задания центра Интернета вещей 1 — выходные данные устройства](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Пример 2 задания центра Интернета вещей — выходные данные устройства](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике описано использование задания для планирования прямого метода на устройстве и обновления свойств двойника устройства.

Чтобы продолжить знакомство с центром Интернета вещей и шаблонами управления устройствами, такими как удаленный через обновление встроенного по Air, см. статью [как выполнить обновление встроенного по](tutorial-firmware-update.md).