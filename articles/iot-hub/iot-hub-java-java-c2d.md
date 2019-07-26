---
title: Отправка сообщений из облака на устройство с помощью Центра Интернета вещей Azure (Java) | Документация Майкрософт
description: Сведения об отправке сообщений из облака на устройство из Центра Интернета вещей Azure с помощью пакетов SDK для Azure IoT для Java. Для получения сообщений, отправляемых из облака на устройство, следует изменить приложение имитации устройства, а для отправки таких сообщений следует изменить внутреннее приложение.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: e844fbe542e0517ffe1f99b550544e6eaca22885
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402395"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. В кратком руководстве [Отправка данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-java.md) показано, как создать центр Интернета вещей, подготавливать в нем удостоверение устройства и создать код для приложения имитации устройства, которое отправляет сообщения, отправляемые с устройства в облако.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Это руководство строится на [отправке данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-java.md). В ней описывается, как сделать следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.

* Получение на устройстве сообщений, передаваемых из облака на устройство.

* В серверной части решения запросите подтверждение доставки (*обратная связь*) для сообщений, отправляемых на устройство из центра Интернета вещей.

Дополнительные сведения о сообщениях, отправляемых из облака на устройство, см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide-messaging.md).

В конце этого руководства запускаются два консольных приложения для Java:

* **смоделированное устройство**, измененная версия приложения, созданная при отправке данных [телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-java.md), который подключается к центру Интернета вещей и получает сообщения из облака на устройство.

* **Send-C2D — messages**, которая отправляет сообщение из облака на устройство в приложение имитации устройства через центр Интернета вещей, а затем получает подтверждение доставки.

> [!NOTE]
> В Центре Интернета вещей реализована поддержка для пакетов SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые указания по связыванию устройства с кодом из этого руководства, а также по подключению к Центру Интернета вещей Azure см. в [центре разработчиков для Интернета вещей Azure](https://azure.microsoft.com/develop/iot).

Для работы с этим учебником требуется:

* Полная рабочая версия руководства отправка данных [телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-java.md) или руководство по [настройке маршрутизации сообщений с помощью центра Интернета вещей](tutorial-routing.md) .

* Последняя версия [пакета SDK для Java SE 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства

В этом разделе вы измените приложение имитации устройства, созданное при отправке данных [телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-java.md) для получения сообщений из облака на устройство из центра Интернета вещей.

1. Откройте в текстовом редакторе файл simulated-device\src\main\java\com\mycompany\app\App.java.

2. Добавьте следующий класс **MessageCallback** как вложенный класс внутри класса **App**. Метод **execute** вызывается, когда устройство получает сообщение из Центра Интернета вещей. В этом примере устройство всегда уведомляет Центр Интернета вещей о завершении отправки сообщения:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Измените метод **main**, чтобы создать экземпляр **AppMessageCallback** и вызвать метод **setMessageCallback** перед открытием клиента.

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Если в качестве транспорта вместо MQTT или AMQP используется HTTPS, то экземпляр **DeviceClient** редко проверяет наличие сообщений от Центра Интернета вещей (реже чем каждые 25 минут). Дополнительные сведения о различиях между поддержкой MQTT, AMQP и HTTPS, а также регулировании Центра Интернета вещей см. в статье[Отправка сообщений с устройства в облако и из облака на устройство с помощью Центра Интернета вещей](iot-hub-devguide-messaging.md).

4. Чтобы создать приложение **simulated-device** с помощью Maven, выполните в командной строке в папке simulated-device следующую команду:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

В этой статье вы создадите серверную службу для отправки сообщений из облака на устройство через центр Интернета вещей, созданный при отправке данных [телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-java.md). Для отправки сообщений из облака на устройство службе требуется разрешение на **Подключение к службе** . По умолчанию каждый центр Интернета вещей создается с помощью политики общего доступа с именем **Service** , предоставляющей это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство

В этом разделе вам предстоит создать консольное приложение Java, которое отправляет сообщения, передаваемые из облака на устройство, в приложение имитации устройства. Вам потребуется идентификатор устройства, добавленного в краткое руководство по отправке данных [телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-java.md) . Кроме того, вам потребуется строка подключения центра Интернета вещей, скопированная ранее в [поле получение строки подключения для центра Интернета вещей](#get-the-iot-hub-connection-string).

1. Создайте проект Maven **send-c2d-messages**, выполнив следующую команду в командной строке. Обратите внимание, что это одна длинная команда.

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке send-c2d-messages.

3. Откройте в текстовом редакторе файл pom.xml из папки send-c2d-messages и добавьте зависимость, приведенную ниже, в узел **dependencies** . Добавление зависимости позволяет использовать в приложении пакет **iothub-java-service-client** для обмена данными со службой Центра Интернета вещей.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **iot-service-client** можно проверить с помощью [поиска Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Сохраните и закройте файл pom.xml.

5. Откройте в текстовом редакторе файл send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Добавьте следующие переменные уровня класса в класс **app** , заменив **{yourhubconnectionstring}** и **{йоурдевицеид}** значениями, которые были записаны ранее:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Замените метод **main** следующим кодом, который подключается к Центру Интернета вещей, отправляет сообщение на устройство, а затем ждет подтверждения о том, что устройство получило и обработало это сообщение.

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Для простоты в этом руководстве не реализована политика повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).

9. Чтобы создать приложение **simulated-device** с помощью Maven, выполните в командной строке в папке simulated-device следующую команду:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке в папке simulated-device выполните приведенную ниже команду, чтобы начать отправку данных телеметрии в Центр Интернета вещей и прослушивание сообщений из облака на устройство, отправляемых из центра:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Запуск приложения виртуального устройства](./media/iot-hub-java-java-c2d/receivec2d.png)

2. В командной строке в папке send-c2d-messages выполните следующую команду, чтобы отправить сообщение из облака на устройство и ожидать подтверждения доставки:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Выполнение команды для отправки сообщения из облака на устройство](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Следующие шаги

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по акселераторам решений Интернета вещей Azure](https://azure.microsoft.com/documentation/suites/iot-suite/).

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide.md).
