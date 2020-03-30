---
title: Отправка файлов с устройств в Центр Интернета вещей Azure | Документация Майкрософт
description: Сведения об отправке файлов с устройства в облако с помощью пакета SDK для устройств Azure IoT для .NET. Отправленные файлы хранятся в контейнере больших двоичных объектов службы хранилища Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: b379f158672a9df3056acb09c63c392869a53283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77108712"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Загрузка файлов с устройства в облако с помощью Концентратора IoT (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Этот учебник основан на коде в [сообщении облакения к устройству С помощью](iot-hub-csharp-csharp-c2d.md) учебника IoT Hub, чтобы показать, как использовать возможности загрузки файлов IoT Hub. В нем показано следующее:

* как безопасно предоставить устройству универсальный код ресурса (URI) большого двоичного объекта Azure для передачи файла;

* как использовать уведомления о передаче файлов Центра Интернета вещей для активации обработки файла в серверной части приложения.

[Телеметрия отправки с устройства на быстрый запуск концентратора IoT](quickstart-send-telemetry-dotnet.md) и [отправка сообщений об об лактапой](iot-hub-csharp-csharp-c2d.md) к устройству с помощью учебника IoT Hub показывает основную функциональность обмена сообщениями от IoT и облачных сообщений от облака к устройству. В настройке [реуктора сообщений с](tutorial-routing.md) помощью учебника IoT Hub описывается способ надежного хранения сообщений от устройства к облаку в хранилище Microsoft Azure Blob. Однако в некоторых сценариях нельзя легко сопоставить данные, которые ваши устройства отправляют, в относительно небольшие сообщения от устройства к облаку, которые принимает IoT Hub. Пример:

* Большие файлы, содержащие изображения.

* Видео

* Данные вибрации с высокой частотой выборки.

* Некоторые виды предварительно обработанных данных.

Эти файлы обычно обрабатываются в виде пакета в облаке с помощью таких инструментов, как [фабрика данных Azure](../data-factory/introduction.md) или стек [Hadoop](../hdinsight/index.yml). При передаче файлов с устройства вы можете рассчитывать на безопасность и надежность Центра Интернета вещей.

В конце этого руководства запускаются два консольных приложения для .NET:

* **ИмитированоеУстройство**. Это приложение передает файл в хранилище с помощью универсального кода ресурса (URI) SAS, предоставленного вашим Центром Интернета вещей. Это модифицированная версия приложения, созданного в [сообщениях облакения к устройству С помощью](iot-hub-csharp-csharp-c2d.md) учебника IoT Hub.

* **ReadFileUploadУведомление**. Это приложение получает уведомления о загрузке файлов от вашего концентратора IoT.

> [!NOTE]
> IoT концентратор поддерживает многие платформы и языки устройств, включая C, Java, Python и Javascript, через SDK-устройства Azure IoT. Пошаговые инструкции по подключению устройства к Центру Интернета вещей Azure см. в [Центре разработчика для Центра Интернета вещей Azure](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Предварительные требования

* Visual Studio

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за пару минут.

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Передача файла из приложения устройства

В этом разделе вы измените приложение устройства, созданное в [сообщении от облака к устройству с помощью Концентратора IoT,](iot-hub-csharp-csharp-c2d.md) для получения сообщений от облака к устройству из концентратора IoT.

1. В Visual Studio Solution Explorer, правой кнопкой мыши **проекта SimulatedDevice,** и выберите **Добавить** > **существующий пункт**. Найдите файл изображений и включите его в свой проект. В этом учебнике используется образ `image.jpg`.

1. Нажмите справа на изображение, а затем выберите **Свойства**. Убедитесь, что параметр **Копировать в выходной каталог** имеет значение **Всегда копировать**.

    ![Покажите, где можно обновить свойство изображения для параметра Copy to Output Directory (Копирование в выходной каталог)](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Добавьте в начало файла **Program.cs** следующие инструкции:

    ```csharp
    using System.IO;
    ```

1. Добавьте в класс **Program** следующий метод:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    Метод `UploadToBlobAsync` принимает имя передаваемого файла и источник его передачи и обрабатывает передачу в хранилище. Консольное приложение отображает время, необходимое для отправки файла.

1. Добавьте следующую строку в `Console.ReadLine()` **основной** метод, прямо перед:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В производственном коде следует реализовать политики повторная попытка, такие как экспоненциальное резервное копирование, как это предлагается при [обработке временных неисправностей.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

В этой статье создается бэк-энд-сервис для получения сообщений уведомлений о загрузке файлов из созданного в телеметрии IoT центра [Отправки с устройства на концентратор IoT.](quickstart-send-telemetry-dotnet.md) Для получения сообщений уведомлений о загрузке файлов службе требуется разрешение **на подключение службы.** По умолчанию каждый концентратор IoT создается с помощью службы общего доступа, названной **службой,** которая предоставляет это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Получение уведомления о передачи файла

В этом разделе вам предстоит создать консольное приложение для .NET, которое получает уведомления об отправке файлов из Центра Интернета вещей.

1. В текущем решении Visual Studio выберите **Файл** > **Новый** > **проект**. В **создании нового проекта**выберите **консоль новейшее приложение (.NET Framework),** а затем выберите **Следующий**.

1. Назовите проект *ReadFileUploadNotification*. Под **решением**выберите **Добавить в решение.** Выберите **Создать**, чтобы создать проект.

    ![Налажить проект ReadFileUploadNotification в Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. В Solution Explorer нажмите правой кнопкой мыши на проект **readFileUploadNotification** и выберите **пакеты Управления NuGet.**

1. В **NuGet менеджер пакетов**, выберите **просмотр**. Поиск и выбор **Microsoft.Azure.Devices,** а затем выберите **Install.**

    Этот шаг загружается, устанавливается и добавляет ссылку на [пакет Службы IoT Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) в проекте **ReadFileUploadNotification.**

1. В **Program.cs** файле для этого проекта добавьте следующее утверждение в верхней части файла:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Добавьте следующие поля в класс **Program** . Замените `{iot hub connection string}` значение заполнителя строкой подключения концентратора IoT, которую вы скопировали ранее в [строке подключения концентратора IoT:](#get-the-iot-hub-connection-string)

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Добавьте в класс **Program** следующий метод:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Обратите внимание, что шаблон получения здесь аналогичен шаблону, использовавшемуся для получения сообщений из облака на устройство из приложения устройства.

1. Наконец, добавьте следующие строки в **основной** метод:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В Solutions Explorer нажмите правой кнопкой мыши и выберите **проекты StartUp.**

1. В**проекте запуска** **Common Properties** > выберите **несколько проектов запуска,** затем выберите действие **Start** для **ReadFileUploadNotification** и **SimulatedDevice.** Нажмите кнопку **ОК** , чтобы сохранить изменения.

1. Нажмите клавишу **F5**. Должны запуститься оба приложения. Вы должны увидеть, как завершится передача в одном консольном приложении и как другое консольное приложение получит уведомление о передаче. Наличие отправленного файла в учетной записи хранения Azure можно проверить с помощью [портала Azure](https://portal.azure.com/) или обозревателя серверов Visual Studio.

    ![Снимок экрана, показывающий экран выходных данных](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководство показано, как использовать возможности передачи файлов Центра Интернета вещей, чтобы упростить передачу файлов из устройств. Вы можете продолжить изучение функций и сценариев IoT Hub со следующими статьями:

* [Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (PowerShell)](iot-hub-rm-template-powershell.md)

* [Пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md)

* [IoT Hub SDKs](iot-hub-devguide-sdks.md) (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
