---
title: Отправка файлов с устройств в Центр Интернета вещей с помощью Python | Документация Майкрософт
description: Сведения об отправке файлов с устройства в облако с помощью пакета SDK для устройств Azure IoT для Python. Отправленные файлы хранятся в контейнере больших двоичных объектов службы хранилища Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 47fb7c615389e24322450ed1785aa7da9ec50db6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759695"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Загрузка файлов с устройства в облако с помощью Концентратора IoT (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

В этой статье описано, как использовать [возможности передачи файлов Центра Интернета вещей](iot-hub-devguide-file-upload.md), чтобы передать файл в [хранилище BLOB-объектов Azure](../storage/index.yml). В этом руководстве описаны следующие процедуры.

* Безопасное предоставление контейнера хранилища для передачи файла.

* Использование клиента Python для передачи файла через Центр Интернета вещей.

[Телеметрия «Отправить» с устройства на быстрый запуск концентратора IoT](quickstart-send-telemetry-python.md) демонстрирует основную функциональность обмена сообщениями от устройства к облаку IoT Hub. Тем не менее в некоторых случаях не просто сопоставить данные, отправляемые устройством, с относительно небольшими сообщениями, отправляемыми из устройства в облако, которые принимает Центр Интернета вещей. При передаче файлов с устройства вы можете рассчитывать на безопасность и надежность Центра Интернета вещей.

В конце этого урока вы запустите консоль приложение Python:

* **FileUpload.py**, которое отправляет файл в хранилище с помощью пакета SDK для устройств для Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Передача файла из приложения устройства

В этом разделе вы создадите приложение для устройства, чтобы отправлять файлы в Центр Интернета вещей.

1. В запросе команды выполнить следующую команду для установки пакета **лазурного устройства.** Этот пакет используется для координации загрузки файлов с помощью концентратора IoT.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. В запросе команды выполнить следующую команду для установки пакета [**azure.storage.blob.**](https://pypi.org/project/azure-storage-blob/) Вы используете этот пакет для выполнения загрузки файла.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Создайте тестовый файл, который вы будете загружать на хранение капли.

1. В текстовом редакторе создайте в рабочей папке файл **FileUpload.py**.

1. Добавьте следующие инструкции `import` и переменные в начало файла **FileUpload.py**.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. В файле замените `[Device Connection String]` строкой подключения устройства Центра Интернета вещей. Замените `[Full path to local file]` путь к созданного вами тестовому файлу или любому файлу на устройстве, который вы хотите загрузить.

1. Создайте функцию для загрузки файла в хранилище капли:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Эта функция разбирает *blob_info* структура перешла в нее, чтобы создать URL, который он использует для инициализации [azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python). Затем он загружает файл в хранилище Azure blob с помощью этого клиента.

1. Добавьте следующий код для подключения клиента и загрузки файла:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Этот код создает асинхронный **IoTHubDeviceистом** и использует следующие AI для управления загрузкой файлов с помощью концентратора IoT:

    * **get_storage_info_for_blob** получает информацию от вашего концентратора IoT о созданной ранее учетной записи хранения. Эта информация включает имя хоста, имя контейнера, имя капли и токен SAS. Информация о хранении передается **функции store_blob** (созданной на предыдущем этапе), поэтому **BlobClient** в этой функции может аутентифицироваться с помощью хранилища Azure. Метод **get_storage_info_for_blob** также возвращает correlation_id, которая используется в методе **notify_blob_upload_status.** Correlation_id является ioT концентраторспособ маркировки, какой капли вы работаете.

    * **notify_blob_upload_status** уведомляет IoT Hub о состоянии вашей операции хранения капли. Вы проходите его correlation_id, полученный **методом get_storage_info_for_blob.** Он используется IoT Hub для уведомления любой службы, которая может слушать уведомление о состоянии задачи загрузки файла.

1. Сохраните и закройте файл **UploadFile.py**.

## <a name="run-the-application"></a>Запуск приложения

Теперь вы готовы к запуску приложения.

1. В командной строке в рабочей папке выполните следующую команду.

    ```cmd/sh
    python FileUpload.py
    ```

2. На следующем снимке экрана показаны выходные данные приложения **FileUpload**.

    ![Выходные данные приложения simulated-device](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. На портале можно просмотреть отправленный файл в контейнере хранилища, который вы настроили.

    ![Отправленный файл](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Следующие шаги

В этом руководство показано, как использовать возможности передачи файлов Центра Интернета вещей, чтобы упростить передачу файлов из устройств. Изучение функций и сценариев Центра Интернета вещей можно продолжить в следующих руководствах:

* [Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (PowerShell)](iot-hub-rm-template-powershell.md)

* [Пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md)

* [Пакеты SDK для Центра Интернета вещей Azure](iot-hub-devguide-sdks.md)

Узнайте больше о хранилище Azure Blob со следующими ссылками:

* [Документация по хранению данных Azure Blob](https://docs.microsoft.com/azure/storage/blobs/)

* [Хранение Azure Blob для документации API Python](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
