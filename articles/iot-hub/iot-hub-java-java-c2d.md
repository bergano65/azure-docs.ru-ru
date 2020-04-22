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
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e16d0ed264f32746c11d89e88ea1e67f9383b773
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732521"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. [Телеметрия отправки с устройства на быстрый запуск концентратора IoT](quickstart-send-telemetry-java.md) показывает, как создать концентратор IoT, предоставить в нем идентификацию устройства и кодировать смоделированное приложение устройства, которое отправляет сообщения от устройства к облаку.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Этот учебник основан на [отправке телеметрии с устройства на концентратор IoT.](quickstart-send-telemetry-java.md) В ней описывается, как сделать следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.

* Получение на устройстве сообщений, передаваемых из облака на устройство.

* От задней части решения — запрос подтверждения доставки *(обратной связи)* для сообщений, отправленных на устройство из Концентратора IoT.

Более подробную информацию о сообщениях об [облаке-устройстве можно найти в руководстве разработчика IoT Hub.](iot-hub-devguide-messaging.md)

В конце этого руководства запускаются два консольных приложения для Java:

* **моделируемый устройство**, модифицированная версия приложения, созданного в Отправить телеметрии с устройства на [концентратор IoT](quickstart-send-telemetry-java.md), который подключается к концентратору IoT и получает сообщения от облака к устройству.

* **отправка-c2d-сообщения**, которая отправляет сообщение об лакт-устройству в приложение моделируемого устройства через IoT Hub, а затем получает подтверждение его доставки.

> [!NOTE]
> IoT Концентратор имеет поддержку SDK для многих платформ и языков устройств (включая C, Java, Python и Javascript) через SDK-устройства Azure IoT. Пошаговые указания по связыванию устройства с кодом из этого руководства, а также по подключению к Центру Интернета вещей Azure см. в [центре разработчиков для Интернета вещей Azure](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Предварительные требования

* Полная рабочая версия [телеметрии Отправки с устройства на быстрый запуск концентратора IoT](quickstart-send-telemetry-java.md) или [настройка направления сообщений с](tutorial-routing.md) помощью учебника IoT Hub.

* [Java SE Комплект разработки 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Щелкните ссылку **Java 8** в разделе **Долгосрочная поддержка**, чтобы скачать все необходимое для работы с JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за пару минут.

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства

В этом разделе вы модифицируете приложение с моделируемого устройства, созданное в [телеметрии Отправки с устройства на концентратор IoT,](quickstart-send-telemetry-java.md) для получения сообщений от облака к устройству из концентратора IoT.

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

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

В этой статье вы создаете бэкэнд-сервис для отправки сообщений от облака к устройству через созданный концентратор IoT, созданный в [телеметрии От устройства к концентратору IoT.](quickstart-send-telemetry-java.md) Для отправки сообщений об облаке и устройстве службе требуется разрешение **на подключение службы.** По умолчанию каждый концентратор IoT создается с помощью службы общего доступа, названной **службой,** которая предоставляет это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство

В этом разделе вам предстоит создать консольное приложение Java, которое отправляет сообщения, передаваемые из облака на устройство, в приложение имитации устройства. Вам нужен идентификатор устройства, добавленного в [телеметрию Отправки с устройства на быстрый запуск концентратора IoT.](quickstart-send-telemetry-java.md) Вам также нужна строка соединения концентратора IoT, которая была скопирована ранее в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string)

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

7. Добавьте следующие переменные уровня класса в класс **Приложения,** заменив **«yourhubconnectionstring»** и **«yourdeviceid»** на значения, которые вы отмечали ранее:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Замените **основной** метод следующим кодом. который подключается к Центру Интернета вещей, отправляет сообщение на устройство, а затем ждет подтверждения о том, что устройство получило и обработало это сообщение.

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
    > Для простоты, этот учебник не реализует какой-либо политики повторной попытки. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).

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
