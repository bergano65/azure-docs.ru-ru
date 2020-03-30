---
title: Начало работы с двойниками устройств Центра Интернета вещей (Java) | Документация Майкрософт
description: Добавление тегов и последующее использование запроса Центра Интернета вещей с помощью двойников устройств Центра Интернета вещей. Используйте пакет SDK для устройств Центра Интернета вещей Azure для Java, чтобы реализовать приложение устройства, и пакет SDK для служб Интернета вещей Azure для Java, чтобы реализовать приложение-службу, которое добавит теги и выполнит запрос к Центру Интернета вещей.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e0114c37b2204a7ad1d7b0cf9c7f336dcd85883a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110487"
---
# <a name="get-started-with-device-twins-java"></a>Начало работы с двойниками устройств (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

В этом руководстве создаются два консольных приложения для Java:

* **add-tags-query** — внутреннее приложение Java, которое добавляет теги и выполняет запросы к двойникам устройств.
* **simulated-device** — это приложение устройства Java, которое подключается к Центру Интернета вещей и сообщает о его условии подключения c помощью передаваемого свойства.

> [!NOTE]
> Статья [IoT Hub SDKs](iot-hub-devguide-sdks.md) (Пакеты SDK для Центра Интернета вещей) содержит сведения о разных пакетах SDK для Azure IoT, с помощью которых можно создать приложения для устройств и внутренние приложения.

## <a name="prerequisites"></a>Предварительные требования

* [Java SE Комплект разработки 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Щелкните ссылку **Java 8** в разделе **Долгосрочная поддержка**, чтобы скачать все необходимое для работы с JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе вы создадите приложение Java, которое добавляет метаданные расположения в виде тега в двойник устройства в Центре Интернета вещей, связанный с **myDeviceId**. Сначала приложение запрашивает в Центре Интернета вещей устройства, расположенные в США, а затем устройства, подключенные к сотовой сети.

1. На машине разработки создайте пустую папку под названием **iot-java-twin-getstarted.**

2. В папке **iot-java-twin-getstarted** создайте проект Maven под названием **add-tags-query,** используя следующую команду в вашей команде:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. В запросе команды перейдите в папку **с добавлением-запрос.**

4. Используя текстовый редактор, откройте файл **pom.xml** в папке **добавления-запросов** и добавьте следующую зависимость в узло **зависимых.** Эта зависимость позволяет использовать пакет **iot-service-client** в приложении для связи с концентратором IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **iot-service-client** можно проверить с помощью [поиска Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Добавьте следующий узел, **build**, после узла **dependencies**. Эта конфигурация поручает Maven использовать Java 1.8 для создания приложения.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Сохраните и закройте файл **pom.xml**.

7. Используя текстовый редактор, откройте файл **add-tags-query-main-java-com-mycompany-app-App.java.**

8. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Добавьте следующие переменные уровня класса в класс **Приложения.** Заменить `{youriothubconnectionstring}` строку соединения концентратора IoT, скопированную в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Обновите подпись метода **main**, добавив следующее предложение `throws`:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Замените код в **основном** методе следующим кодом для создания объектов **DeviceTwin** и **DeviceTwinDevice.** Объект **DeviceTwin** обрабатывает взаимодействие с вашим Центром Интернета вещей. Объект **DeviceTwinDevice** представляет двойник устройства, его свойства и теги:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Добавьте следующий блок `try/catch` в метод **main**:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Чтобы обновить теги **region** и **plant** двойника устройства, добавьте следующий код в блок `try`:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Чтобы отправить запрос к двойникам устройств в Центре Интернета вещей, добавьте следующий код в блок `try` после кода, добавленного на предыдущем шаге. Код выполняет два запроса. Каждый запрос возвращает максимум 100 устройств.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Сохранить и закрыть **файл надстройки-запросы src-main-java-com-mycompany-App.java**

16. Создайте приложение **add-tags-query** и исправьте все ошибки. В запросе команды перейдите в папку **с добавлением-запрос и** запустите следующую команду:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Создание приложения устройства

В этом разделе вы создадите консольное приложение Java, которое задает значение сообщаемого свойства, отправляемое в Центр Интернета вещей.

1. В папке **iot-java-twin-getstarted** создайте проект Maven под названием **смоделированное устройство,** используя следующую команду в вашей команде:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В запросе команды перейдите к **папке с моделям устройств.**

3. Используя текстовый редактор, откройте файл **pom.xml** в папке **с моделируемым устройством** и добавьте следующие зависимости в узло **зависимостей.** Эта зависимость позволяет использовать пакет **iot-устройство-клиент** в вашем приложении для связи с вашим концентратором IoT.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **iot-device-client** можно проверить с помощью [поиска Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Добавьте следующую зависимость в узло **зависимостей.** Эта зависимость настраивает NOP на лесозаготовительный фасад Apache [SLF4J,](https://www.slf4j.org/) который используется клиентом устройства SDK для реализации журналов. Эта конфигурация не является обязательной, но, если вы опустите ее, вы можете увидеть предупреждение в консоли при запуске приложения. Для получения дополнительной информации о входе в клиент устройства SDK, [см. Регистрация](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) в *образцах для устройства SDK Azure IoT для* файла Java readme.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Добавьте следующий узел, **build**, после узла **dependencies**. Эта конфигурация дает указание Maven использовать Java версии 1.8 для создания приложения:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Сохраните и закройте файл **pom.xml**.

7. Используя текстовый редактор, откройте смоделированное устройство, которое является основным файлом **java-com-app.java.**

8. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Добавьте следующие переменные уровня класса в класс **Приложения.** Замените `{yourdeviceconnectionstring}` строку подключения устройства, скопированную в [Регистрации, новое устройство в концентраторе IoT.](#register-a-new-device-in-the-iot-hub)

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    При создании экземпляра объекта **DeviceClient** в этом примере приложения используется переменная **protocol**.

10. Чтобы распечатать сведения об обновлениях двойников, добавьте в класс **App** следующий метод:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Замените код в **основном** методе следующим кодом на:

    * Создать клиент устройства для взаимодействия с Центром Интернета вещей.

    * Создать объект **Device** для хранения свойств двойника устройства.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. Добавьте следующий код в метод **main**, чтобы создать сообщаемое свойство **connectivityType** и отправить его в Центр Интернета вещей:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. Добавьте следующий код в конец **основного** метода. Ожидание ключа **Enter** позволяет IoT Hub сообщить о состоянии двух операций устройства.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Измените подпись метода **main** , чтобы включить исключения, указанные ниже.

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Сохранить и закрыть **смоделированное устройство src-main-java-com-mycompany-app.java** файл.

16. Создайте приложение **simulated-device** и исправьте все ошибки. В запросе команды перейдите к папке **с моделируемым устройствами** и запустите следующую команду:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску консоли приложений.

1. В запросе команды в **папке добавления-запросов** выполнить следующую команду для запуска приложения службы обслуживания **дополнительных тегов::**

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Обновление значений тегов и выполнение запросов устройств с помощью приложения службы Центра Интернета вещей](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Вы увидите теги **plant** и **region**, добавленные в двойник устройства. Первый запрос возвращает устройство, а второй — нет.

2. В запросе команды в папке **с моделируемым устройством** запустите следующую команду, чтобы добавить свойство **connectivityType,** сообщенное в близнец абии устройства:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Клиент устройства добавляет сообщаемое свойство **connectivityType**](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. В запросе команды в **папке добавления-запросов** запустите следующую команду для запуска приложения **службы дополнительных тегов-запросов** во второй раз:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Обновление значений тегов и выполнение запросов устройств с помощью приложения службы Центра Интернета вещей](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Теперь, когда устройство отправило свойство **connectivityType** в Концентратор IoT, второй запрос возвращает устройство.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из внутреннего приложения и написали код приложения устройства, чтобы сообщить сведения о подключении в двойнике устройства. Вы также узнали, как запрашивать сведения о двойнике устройства, используя похожий на SQL язык запросов Центра Интернета вещей.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* Отправить телеметрию с устройств с помощью учебника [IoT Hub.](quickstart-send-telemetry-java.md)

* Устройства управления в интерактивном режиме (например, включение вентилятора из приложения, управляемого пользователем) с руководством [по прямым методам использования.](quickstart-control-device-java.md)
