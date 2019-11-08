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
ms.openlocfilehash: 32219eeaee7980b685ac3453c6af3beff716abe2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824086"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Настройка сертификата безопасности X.509 в Центре Интернета вещей Azure

В этом руководстве описаны действия, необходимые для защиты центра Интернета вещей Azure с помощью *проверки подлинности на основе сертификата X. 509*. В целях иллюстрации мы используем инструмент с открытым кодом OpenSSL для локального создания сертификатов на компьютере Windows. Мы рекомендуем использовать это руководство только для тестирования. Для рабочей среды следует приобрести сертификаты в *корневом центре сертификации (ЦС)* .

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется подготовить следующие ресурсы:

* Центр Интернета вещей, созданный в вашей подписке Azure. Дополнительные сведения см. в статье [Создание Центра Интернета вещей с помощью портала Azure](iot-hub-create-through-portal.md).

* У вас установлена [Visual studio 2017 или Visual studio 2019](https://www.visualstudio.com/vs/) .

## <a name="get-x509-ca-certificates"></a>Получение сертификатов ЦС X.509

Для обеспечения безопасности на основе сертификатов X.509 в Центре Интернета вещей вам понадобится [цепочка сертификатов X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), которая включает корневой сертификат, а также любые промежуточные сертификаты вплоть до конечного.

Вы можете выбрать любой из следующих способов получения сертификатов:

* Приобретите сертификат X.509 в *корневом центре сертификации (ЦС)* . Этот метод рекомендуется для рабочих сред.

* Создавайте собственные сертификаты X. 509 с помощью стороннего средства, например [OpenSSL](https://www.openssl.org/). Этот метод подходит для целей тестирования и разработки. Сведения о создании тестовых сертификатов ЦС с помощью PowerShell или Bash см. в статье [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). В остальной части этого руководства используются тестовые сертификаты ЦС, созданные по инструкциям в статье [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Создайте [промежуточный сертификат ЦС X. 509](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) , подписанный существующим сертификатом корневого ЦС, и отправьте его в центр. После отправки и проверки промежуточного сертификата, как указано ниже, его можно использовать вместо сертификата корневого ЦС, упомянутого ниже. Такие средства, как OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) и [OpenSSL CA](https://www.openssl.org/docs/man1.1.0/man1/ca.html)), можно использовать для создания и подписи промежуточного сертификата ЦС.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Регистрация сертификата ЦС X.509 в Центре Интернета вещей

Ниже описано, как добавить новый центр сертификации в Центр Интернета вещей с помощью портала.

1. В портал Azure перейдите в центр Интернета вещей и выберите **параметры** > **Сертификаты** для концентратора.

1. Нажмите кнопку **Добавить** , чтобы добавить новый сертификат.

1. В поле **имя сертификата**введите понятное отображаемое имя и выберите файл сертификата, созданный в предыдущем разделе, с компьютера.

1. Получив уведомление о том, что сертификат успешно отправлен, нажмите кнопку **сохранить**.

    ![Передача сертификата](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Сертификат появится в списке Сертификаты со статусом не **Проверено**.

1. Выберите только что добавленный сертификат, чтобы отобразить **сведения о сертификате**, а затем выберите **создать код проверки**.

   ![Проверка сертификата](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Скопируйте **код проверки** в буфер обмена. Он используется для проверки владельца сертификата.

1. Выполните шаг 3 в статье [Управление сертификатами ЦС для примеров и учебников](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Этот процесс подписывает код проверки с помощью закрытого ключа, связанного с сертификатом ЦС X. 509, который создает подпись. Выполнить этот процесс подписания можно с помощью инструментов, таких как OpenSSL. Этот процесс известен как [доказательство принадлежности](https://tools.ietf.org/html/rfc5280#section-3.1).

1. На странице **сведения о сертификате**в разделе **Проверка PEM или CER**найдите файл сигнатуры и откройте его. Затем нажмите кнопку **проверить**.

   Состояние сертификата изменится на **Проверено**. Выберите **Обновить** , если сертификат не обновляется автоматически.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Создание устройства X.509 для Центра Интернета вещей

1. В портал Azure перейдите в центр Интернета вещей, а затем выберите **explorer** > **устройства IOT**.

1. Выберите **создать** , чтобы добавить новое устройство.

1. В окне **идентификатор устройства**введите понятное отображаемое имя. В качестве **типа проверки подлинности**выберите пункт **сертификат ЦС X. 509**и нажмите кнопку **сохранить**.

   ![Создание устройства X.509 на портале](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Аутентификация устройства X.509 с помощью сертификатов X.509

Чтобы аутентифицировать устройство X.509, сначала необходимо подписать устройство с помощью сертификата ЦС. Подписывание конечных устройств обычно выполняется на заводе, где соответствующим образом включены производственные инструменты. Так как устройство переходит от одного производителя к другому, действие подписывания каждого производителя записывается в качестве промежуточного сертификата в цепочке. Результатом является цепочка сертификатов от сертификата ЦС до конечного сертификата устройства. В шаге 4 руководства [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) описывается создание сертификата устройства.

Далее мы рассмотрим, как создать приложение C#, чтобы смоделировать устройство X.509, зарегистрированное для вашего Центра Интернета вещей. Мы отправим значения температуры и влажности с виртуального устройства в ваш центр. В этом учебнике будет создано только приложение устройства. Создание приложения службы Центра Интернета вещей, которое будет отправлять ответы на события, отправленные этим виртуальным устройством, останется в качестве упражнения для читателей. В примере приложения C# предполагается, что вы выполнили процедуру, описанную в статье [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Откройте Visual Studio, выберите **создать новый проект**, а затем выберите шаблон проекта **консольное приложение (.NET Framework)** . Щелкните **Далее**.

1. В окне **Настройка нового проекта**назовите проект *SimulateX509Device*, а затем выберите **создать**.

   ![Создание проекта устройства X. 509 в Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. В обозреватель решений щелкните правой кнопкой мыши проект **SimulateX509Device** , а затем выберите пункт **Управление пакетами NuGet**.

1. В **диспетчере пакетов NuGet**нажмите кнопку **Обзор** и найдите и выберите **Microsoft. Azure. Devices. Client**. Щелкните **Установить**.

   ![Добавление пакета NuGet SDK для устройств в Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    На этом шаге выполняется скачивание, установка и Добавление ссылки на пакет SDK для устройств Azure IoT и его зависимости.

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Добавьте в класс **Program** следующие поля:

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Используйте понятное имя устройства, которое использовалось в предыдущем разделе вместо _< your_device_id >_ .

1. Добавьте следующую функцию, чтобы создать случайные числа для значений температуры и влажности и отправить их в центр.

    ```CSharp
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

1. Наконец, добавьте следующие строки кода в функцию **Main** , заменив заполнители _Device-ID_, My _-IOT-Hub-Name_и _Absolute-path-to-for-Device-PFX_ , в соответствии с требованиями программы установки.

    ```CSharp
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

1. Запустите приложение. Так как это приложение обращается к *PFX* -файлу, может потребоваться запустить это приложение от имени администратора.

   1. Создайте решение Visual Studio.

   1. Откройте новое окно командной строки с помощью команды **Запуск от имени администратора**.  

   1. Перейдите в папку, содержащую решение, а затем перейдите к папке *bin/debug* в папку решения.

   1. Запустите приложение **SimulateX509Device. exe** из командной строки.

   Устройство должно быть успешно подключено к центру и отправлять события.

   ![Запустить приложение устройства](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о безопасности решения IoT см.:

* [Рекомендации по обеспечению безопасности IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Архитектура безопасности IoT](../iot-fundamentals/iot-security-architecture.md)

* [Безопасность развертывания Интернета вещей](../iot-fundamentals/iot-security-deployment.md)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
