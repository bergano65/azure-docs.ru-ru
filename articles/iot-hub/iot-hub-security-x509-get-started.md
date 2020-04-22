---
title: Руководство по использованию сертификата X.509 для обеспечения безопасности в Центре Интернета вещей Azure | Документация Майкрософт
description: Приступая к работе с защитой на основе сертификата X.509 в Центре Интернета вещей Azure в смоделированной среде.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: ac45cf42ed174d3e9423b4ea39cadf16b84897ef
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759639"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Настройка сертификата безопасности X.509 в Центре Интернета вещей Azure

В этом уроке показаны шаги, необходимые для обеспечения безопасности концентратора Azure IoT с помощью *сертификата X.509.* Для иллюстрации мы используем инструмент open-source OpenSSL для создания сертификатов локально на вашей машине Windows. Мы рекомендуем использовать это руководство только для тестирования. Для рабочей среды следует приобрести сертификаты в *корневом центре сертификации (ЦС)*.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется подготовить следующие ресурсы:

* Центр Интернета вещей, созданный в вашей подписке Azure. Дополнительные сведения см. в статье [Создание Центра Интернета вещей с помощью портала Azure](iot-hub-create-through-portal.md).

* У вас есть [Visual Studio 2017 или Visual Studio 2019](https://www.visualstudio.com/vs/) установлен.

## <a name="get-x509-ca-certificates"></a>Получение сертификатов ЦС X.509

Для обеспечения безопасности на основе сертификатов X.509 в Центре Интернета вещей вам понадобится [цепочка сертификатов X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), которая включает корневой сертификат, а также любые промежуточные сертификаты вплоть до конечного.

Вы можете выбрать любой из следующих способов получения сертификатов:

* Приобретите сертификат X.509 в *корневом центре сертификации (ЦС)*. Этот метод рекомендуется для производственных сред.

* Создайте свои собственные сертификаты X.509 с помощью стороннего инструмента, такого как [OpenSSL.](https://www.openssl.org/) Этот метод подходит для целей тестирования и разработки. Сведения о создании тестовых сертификатов ЦС с помощью PowerShell или Bash см. в статье [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). В остальной части этого руководства используются тестовые сертификаты ЦС, созданные по инструкциям в статье [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Создайте [промежуточный сертификат CA X.509,](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) подписанный существующим корневым сертификатом CA, и загрузите его в концентратор. После того, как промежуточный сертификат загружен и проверен, в соответствии с инструкциями ниже, он может быть использован в месте корневого сертификата CA, упомянутого ниже. Такие инструменты, как OpenSSL[(opensl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) и [openssl ca)](https://www.openssl.org/docs/man1.1.0/man1/ca.html)могут быть использованы для создания и подписания промежуточного сертификата CA.

> [!NOTE]
> Не загружайте корень третьей стороны, если он не является уникальным для вас, потому что это позволит другим клиентам третьей стороны подключить свои устройства к вашему IoT Hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Регистрация сертификата ЦС X.509 в Центре Интернета вещей

Ниже описано, как добавить новый центр сертификации в Центр Интернета вещей с помощью портала.

1. На портале Azure перейдите к концентратору IoT и выберите**Сертификаты** **настроек** > для концентратора.

1. Выберите **Добавить,** чтобы добавить новый сертификат.

1. В **названии сертификата**введите имя дружественного дисплея и выберите файл сертификата, созданный в предыдущем разделе, с компьютера.

1. Как только вы получите уведомление об успешной загрузке сертификата, выберите **Сохранить**.

    ![Передача сертификата](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Ваш сертификат отображается в списке сертификатов со статусом **Unverified**.

1. Выберите сертификат, который вы только что добавили для отображения **деталей сертификата,** а затем выберите **Код проверки generate.**

   ![Проверка сертификата](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Скопируйте **код проверки** на буфер обмена. Вы используете его для проверки права собственности на сертификат.

1. Следуйте шагу 3 в [управлении сертификатами CA для тестирования образцов и учебников.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)  Этот процесс подписывает ваш код проверки с закрытым ключевым партнером с вашим сертификатом X.509 CA, который генерирует подпись. Выполнить этот процесс подписания можно с помощью инструментов, таких как OpenSSL. Этот процесс известен как [Доказательство владения](https://tools.ietf.org/html/rfc5280#section-3.1).

1. В **справке Справка**, в **соответствии с сертификатом проверки .pem или .cer файл**, найти и открыть файл подписи. Затем выберите **Проверить**.

   Статус сертификата изменяется на **Verified**. Выберите **Обновление,** если сертификат не обновляется автоматически.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Создание устройства X.509 для Центра Интернета вещей

1. На портале Azure перейдите к концентратору IoT, а затем выберите устройства **Explorers** > **IoT.**

1. Выберите **новое,** чтобы добавить новое устройство.

1. В **идентификаторе устройства**введите имя дружественного дисплея. Для **типа аутентификации**выберите **X.509 CA Signed,** а затем выберите **Сохранить.**

   ![Создание устройства X.509 на портале](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Аутентификация устройства X.509 с помощью сертификатов X.509

Чтобы аутентифицировать устройство X.509, сначала необходимо подписать устройство с помощью сертификата ЦС. Подписывание конечных устройств обычно выполняется на заводе, где соответствующим образом включены производственные инструменты. По мере того как устройство переходит от одного производителя к другому, действие подписи каждого производителя фиксируется как промежуточный сертификат в цепочке. Результатом является цепочка сертификатов от сертификата CA до сертификата листа устройства. В шаге 4 руководства [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) описывается создание сертификата устройства.

Далее мы рассмотрим, как создать приложение C#, чтобы смоделировать устройство X.509, зарегистрированное для вашего Центра Интернета вещей. Мы отправим значения температуры и влажности с виртуального устройства в ваш центр. В этом учебнике мы создадим только приложение устройства. Создание приложения службы Центра Интернета вещей, которое будет отправлять ответы на события, отправленные этим виртуальным устройством, останется в качестве упражнения для читателей. В примере приложения C# предполагается, что вы выполнили процедуру, описанную в статье [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Откройте Visual Studio, выберите **Создать новый проект,** а затем выбрать шаблон проекта **Console App (.NET Framework).** Выберите **Далее**.

1. В **настройке нового проекта,** назовите проект *SimulateX509Device*, а затем выберите **Создать.**

   ![Создание проекта устройства X.509 в Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. В Solution Explorer, правой кнопкой мыши **SimulateX509Device** проекта, а затем выберите **Управление NuGet пакеты**.

1. В **наборе NuGet —** выберите **просмотр** и поиск и выбор **Microsoft.Azure.Devices.Client**. Выберите пункт **Установить**.

   ![Добавление пакета NuGet SDK устройства в Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Этот шаг загружает, устанавливает и добавляет ссылку на пакет SDK NuGet-устройства Azure IoT и его зависимости.

1. Добавьте `using` следующие инструкции в верхней части файла **Program.cs:**

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Добавьте следующие поля в класс **Программы:**

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Вместо<_your_device_id>_ используйте имя дружественного устройства, которое вы использовали в предыдущем разделе.

1. Добавьте следующую функцию, чтобы создать случайные числа для значений температуры и влажности и отправить их в центр.

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Наконец, добавьте следующие строки кода в **основную** функцию, заменив заполнители _устройства-id,_ _ваш-йот-хаб-имя,_ и _абсолютный путь к вашему устройству-pfx-файл,_ как того требует ваша настройка.

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Этот код подключается к Центру Интернета вещей, создавая строку подключения для устройства X.509. После успешного подключения он отправляет события температуры и влажности в центр и ожидает его ответа.

1. Запустите приложение. Поскольку это приложение получает доступ к файлу *.pfx,* возможно, потребуется запустить это приложение в качестве администратора.

   1. Создайте решение Visual Studio.

   1. Откройте новое окно командного запроса, используя **Run в качестве администратора.**  

   1. Перейдите к папке, содержащей решение, а затем перейдите к пути *ячейки/ошибки* в папке решения.

   1. Выполнить приложение **SimulateX509Device.exe** из запроса команды.

   Устройство должно быть успешно подключено к центру и отправлять события.

   ![Запуск приложения устройства](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о безопасности решения IoT см.:

* [Лучшие практики безопасности IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Архитектура безопасности "Интернета вещей"](../iot-fundamentals/iot-security-architecture.md)

* [Защита развертывания IoT](../iot-fundamentals/iot-security-deployment.md)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
