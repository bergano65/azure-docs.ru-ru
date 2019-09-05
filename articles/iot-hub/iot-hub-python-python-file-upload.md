---
title: Отправка файлов с устройств в Центр Интернета вещей с помощью Python | Документация Майкрософт
description: Сведения об отправке файлов с устройства в облако с помощью пакета SDK для устройств Azure IoT для Python. Отправленные файлы хранятся в контейнере больших двоичных объектов службы хранилища Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: a529933cf4af572deacab1ae3c615ec0a0eca68f
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667870"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Передача файлов с устройства в облако с помощью центра Интернета вещей (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

В этой статье описано, как использовать [возможности передачи файлов Центра Интернета вещей](iot-hub-devguide-file-upload.md), чтобы передать файл в [хранилище BLOB-объектов Azure](../storage/index.yml). В этом руководстве описаны следующие процедуры.

* Безопасное предоставление контейнера хранилища для передачи файла.

* Использование клиента Python для передачи файла через Центр Интернета вещей.

Краткое руководство [Отправка данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-python.md) демонстрирует базовую функциональность обмена сообщениями между устройством и облаком в центре Интернета вещей. Тем не менее в некоторых случаях не просто сопоставить данные, отправляемые устройством, с относительно небольшими сообщениями, отправляемыми из устройства в облако, которые принимает Центр Интернета вещей. При передаче файлов с устройства вы можете рассчитывать на безопасность и надежность Центра Интернета вещей.

> [!NOTE]
> Пакет SDK для Центра Интернета вещей для Python в настоящее время поддерживает передачу только текстовых файлов, таких как **TXT**.

По завершении работы с этим руководством вы запустите консольное приложение Python:

* **FileUpload.py**, которое отправляет файл в хранилище с помощью пакета SDK для устройств для Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Ниже приведены инструкции по установке необходимых компонентов.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Передача файла из приложения устройства

В этом разделе вы создадите приложение для устройства, чтобы отправлять файлы в Центр Интернета вещей.

1. Чтобы установить пакет **azure-iothub-device-client**, в командной строке выполните следующую команду.

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. С помощью текстового редактора создайте тестовый файл, который будет загружен в хранилище BLOB-объектов.

    > [!NOTE]
    > Пакет SDK для Центра Интернета вещей для Python в настоящее время поддерживает передачу только текстовых файлов, таких как **TXT**.

3. В текстовом редакторе создайте в рабочей папке файл **FileUpload.py**.

4. Добавьте следующие инструкции `import` и переменные в начало файла **FileUpload.py**. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. В файле замените `[Device Connection String]` строкой подключения устройства Центра Интернета вещей. Замените `[Full path to file]` путем к созданному тестовому файлу или любому файлу на устройстве, который требуется передать. Замените `[File name for storage]` именем, которое вы хотите присвоить файлу после его передачи в хранилище BLOB-объектов. 

6. Создайте обратный вызов функции **upload_blob**:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Добавьте следующий код для подключения клиента и отправки файла. Добавьте также подпрограмму `main`.

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Сохраните и закройте файл **UploadFile.py**.

## <a name="run-the-application"></a>Выполнение приложения

Теперь все готово к запуску приложения.

1. В командной строке в рабочей папке выполните следующую команду.

    ```cmd/sh
    python FileUpload.py
    ```

2. На следующем снимке экрана показаны выходные данные приложения **FileUpload**.

    ![Выходные данные приложения simulated-device](./media/iot-hub-python-python-file-upload/1.png)

3. На портале можно просмотреть отправленный файл в контейнере хранилища, который вы настроили.

    ![Отправленный файл](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Следующие шаги

В этом руководство показано, как использовать возможности передачи файлов Центра Интернета вещей, чтобы упростить передачу файлов из устройств. Изучение функций и сценариев Центра Интернета вещей можно продолжить в следующих руководствах:

* [Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (PowerShell)](iot-hub-rm-template-powershell.md)

* [Пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md)

* [Пакеты SDK для Центра Интернета вещей Azure](iot-hub-devguide-sdks.md)
