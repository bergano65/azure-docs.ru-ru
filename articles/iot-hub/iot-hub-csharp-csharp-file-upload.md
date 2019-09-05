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
ms.openlocfilehash: db020092c076680eddd575f8e7e85a2060603dd8
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147772"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Передача файлов с устройства в облако с помощью центра Интернета вещей (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Это руководство строится на коде в руководстве [Отправка сообщений из облака на устройство с помощью центра Интернета вещей](iot-hub-csharp-csharp-c2d.md) , чтобы продемонстрировать, как использовать возможности отправки файлов в центре Интернета вещей. В нем показано следующее:

* как безопасно предоставить устройству универсальный код ресурса (URI) большого двоичного объекта Azure для передачи файла;

* как использовать уведомления о передаче файлов Центра Интернета вещей для активации обработки файла в серверной части приложения.

В учебнике [Отправка данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-dotnet.md) и [Отправка сообщений из облака на устройство с помощью центра Интернета вещей](iot-hub-csharp-csharp-c2d.md) показаны базовые функции обмена сообщениями между устройством и облаком и из облака на устройство в центре Интернета вещей. В учебнике [Настройка маршрутизации сообщений с помощью центра Интернета вещей](tutorial-routing.md) описывается способ надежного хранения сообщений, отправляемых с устройства в облако, в Microsoft Azure хранилище BLOB-объектов. Однако в некоторых сценариях вы не можете легко сопоставлять данные, отправляемые устройствами, в сообщения относительно малых устройств в облако, которые принимает центр Интернета вещей. Пример:

* Большие файлы, содержащие изображения.

* Видео

* Данные вибрации с высокой частотой выборки.

* Некоторые виды предварительно обработанных данных.

Эти файлы обычно обрабатываются в виде пакета в облаке с помощью таких инструментов, как [фабрика данных Azure](../data-factory/introduction.md) или стек [Hadoop](../hdinsight/index.yml). При передаче файлов с устройства вы можете рассчитывать на безопасность и надежность Центра Интернета вещей.

В конце этого руководства запускаются два консольных приложения для .NET:

* **SimulatedDevice**. Это приложение передает файл в хранилище с помощью универсального кода ресурса (URI) SAS, предоставленного вашим Центром Интернета вещей. Это измененная версия приложения, созданного в руководстве [Отправка сообщений из облака на устройство с помощью центра Интернета вещей](iot-hub-csharp-csharp-c2d.md) .

* **ReadFileUploadNotification**. Это приложение получает уведомления об отправке файлов из центра Интернета вещей.

> [!NOTE]
> Центр Интернета вещей поддерживает множество платформ и языков устройств, включая C, Java, Python и JavaScript, с помощью пакетов SDK для устройств Azure IoT. Пошаговые инструкции по подключению устройства к Центру Интернета вещей Azure см. в [Центре разработчика для Центра Интернета вещей Azure](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Предварительные требования

* Visual Studio

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Передача файла из приложения устройства

В этом разделе вы измените приложение устройства, созданное при [отправке сообщений из облака на устройство с помощью центра Интернета вещей](iot-hub-csharp-csharp-c2d.md) , для получения сообщений из облака на устройство из центра Интернета вещей.

1. В Обозреватель решений Visual Studio щелкните правой кнопкой мыши проект **SimulatedDevice** и выберите команду **Добавить** > **существующий элемент**. Найдите файл изображения и включите его в свой проект. В этом учебнике используется образ `image.jpg`.

1. Щелкните изображение правой кнопкой мыши и выберите пункт **Свойства**. Убедитесь, что параметр **Копировать в выходной каталог** имеет значение **Всегда копировать**.

    ![Покажите, где можно обновить свойство изображения для параметра Copy to Output Directory (Копирование в выходной каталог)](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Добавьте в начало файла **Program.cs** следующие инструкции:

    ```csharp
    using System.IO;
    ```

1. Добавьте следующий метод в класс **Program** .

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

1. Добавьте следующую строку в метод **Main** , прямо перед `Console.ReadLine()`:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора, такие как экспоненциальная задержка, как предлагается при [обработке временных сбоев](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

В этой статье вы создадите серверную службу для получения уведомлений об отправке файлов из центра Интернета вещей, созданного при [отправке данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-dotnet.md). Для получения уведомлений об отправке файлов службе требуется разрешение на **Подключение к службе** . По умолчанию каждый центр Интернета вещей создается с помощью политики общего доступа с именем **Service** , предоставляющей это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Получение уведомления о передачи файла

В этом разделе вам предстоит создать консольное приложение для .NET, которое получает уведомления об отправке файлов из Центра Интернета вещей.

1. В текущем решении Visual Studio выберите **файл** > **создать** > **проект**. В окне **Создание нового проекта**выберите **консольное приложение (.NET Framework)** , а затем нажмите кнопку **Далее**.

1. Назовите проект *ReadFileUploadNotification*. В разделе **решение**выберите **Добавить в решение**. Выберите **Создать**, чтобы создать проект.

    ![Настройка проекта ReadFileUploadNotification в Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. В обозреватель решений щелкните правой кнопкой мыши проект **ReadFileUploadNotification** и выберите пункт **Управление пакетами NuGet**.

1. В **диспетчере пакетов NuGet**нажмите кнопку **Обзор**. Найдите и выберите **Microsoft. Azure. Devices**, а затем нажмите кнопку **установить**.

    На этом шаге вы скачиваете, устанавливаете и добавляете ссылку на [пакет NUGET SDK для службы Azure IOT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) в проекте **ReadFileUploadNotification** .

1. В файле **Program.CS** для этого проекта добавьте следующую инструкцию в начало файла:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения центра Интернета вещей, скопированным ранее в [поле получение строки подключения для центра Интернета вещей:](#get-the-iot-hub-connection-string) `{iot hub connection string}`

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Добавьте следующий метод в класс **Program** .

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

1. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В обозревателе решений щелкните правой кнопкой мыши решение и выберите команду **задать запускаемые проекты**.

1. В меню **стандартные свойства** > **Запуск проекта**выберите **Несколько запускаемых проектов**, а затем выберите действие при **запуске** для **ReadFileUploadNotification** и **SimulatedDevice**. Нажмите кнопку **ОК** , чтобы сохранить изменения.

1. Нажмите клавишу **F5**. Должны запуститься оба приложения. Вы должны увидеть, как завершится передача в одном консольном приложении и как другое консольное приложение получит уведомление о передаче. Наличие отправленного файла в учетной записи хранения Azure можно проверить с помощью [портала Azure](https://portal.azure.com/) или обозревателя серверов Visual Studio.

    ![Снимок экрана, показывающий экран выходных данных](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Следующие шаги

В этом руководство показано, как использовать возможности передачи файлов Центра Интернета вещей, чтобы упростить передачу файлов из устройств. Вы можете продолжить изучение функций и сценариев центра Интернета вещей в следующих статьях:

* [Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (PowerShell)](iot-hub-rm-template-powershell.md)

* [Пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md)

* [Пакеты SDK для Центра Интернета вещей Azure](iot-hub-devguide-sdks.md)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
