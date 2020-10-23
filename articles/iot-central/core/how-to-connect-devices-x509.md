---
title: Подключение устройств с помощью сертификатов X. 509 в приложении IoT Central Azure
description: Как подключить устройства с сертификатами X. 509 с помощью пакета SDK для Node.js устройств для IoT Central приложения
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: c2af331304decd7955892ef4911d1644518f57b8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427887"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Как подключить устройства с сертификатами X. 509 с помощью пакета SDK для Node.js устройств для IoT Central приложения

IoT Central поддерживает подписанные URL-адрес (SAS) и сертификаты X. 509 для защиты обмена данными между устройством и приложением. В руководстве по [созданию и подключению клиентского приложения к IOT Centralному приложению Azure](./tutorial-connect-device-nodejs.md) используется SAS. Из этой статьи вы узнаете, как изменить пример кода для использования X. 509.  Сертификаты X. 509 рекомендуется использовать в рабочих средах. Дополнительные сведения см. [в статье подключение к Azure IOT Central](./concepts-get-connected.md).

В этой статье описаны два способа использования [регистраций](how-to-connect-devices-x509.md#use-a-group-enrollment) X. 509-Group, обычно используемых в рабочей среде, и [индивидуальных регистраций](how-to-connect-devices-x509.md#use-an-individual-enrollment) , которые можно использовать для тестирования.

## <a name="prerequisites"></a>Предварительные требования

- Завершение [создания и подключения клиентского приложения к учебнику по IOT Central приложения Azure (Node.js)](./tutorial-connect-device-nodejs.md) .
- [Git](https://git-scm.com/download/).
- Скачайте и установите [OpenSSL](https://www.openssl.org/). Если вы используете Windows, вы можете использовать двоичные файлы на [странице OpenSSL в SourceForge](https://sourceforge.net/projects/openssl/).

## <a name="use-a-group-enrollment"></a>Использование групповой регистрации

Используйте сертификаты X. 509 с групповой регистрацией в рабочей среде. При регистрации группы вы добавляете корневой или промежуточный сертификат X. 509 в приложение IoT Central. Устройства с конечными сертификатами, полученными из корневого или промежуточного сертификата, могут подключаться к приложению.

## <a name="generate-root-and-device-cert"></a>Создание корня и сертификата устройства

В этом разделе вы используете сертификат X. 509 для подключения устройства с сертификатом, полученным от сертификата группы регистрации, который может подключиться к приложению IoT Central.

> [!WARNING]
> Такой способ создания сертификатов X. 509 предназначен только для тестирования. Для рабочей среды следует использовать официальный, безопасный механизм создания сертификатов.

1. Откройте командную строку. Клонировать репозиторий GitHub для скриптов создания сертификатов:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Перейдите к скрипту генератора сертификатов и установите необходимые пакеты:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Создайте корневой сертификат, а затем выберете сертификат устройства, выполнив следующий сценарий:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

    > [!TIP]
    > ИДЕНТИФИКАТОР устройства может содержать буквы, цифры и `-` символ.

Эти команды создают по три файла для корня и сертификата устройства.

filename | содержимое
-------- | --------
\<name\>_cert PEM | Открытая часть сертификата X509.
\<name\>_key PEM | Закрытый ключ для сертификата X509
\<name\>_fullchain PEM | Вся цепочка ключей для сертификата X509.

## <a name="create-a-group-enrollment"></a>Создание группы регистрации

1. Откройте приложение IoT Central и перейдите к элементу **Администрирование**  в левой области и выберите **Подключение устройства**.

1. Выберите **+ создать группу регистрации**и создайте новую группу регистрации с именем _MyX509Group_ с типом аттестации **Certificates (X. 509)**.

1. Откройте созданную группу регистрации и выберите **Управление первичными**.

1. Выберите параметр файл и отправьте файл корневого сертификата с именем _mytestrootcert_cert. pem_ , созданный ранее.

    ![Отправка сертификата](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Чтобы завершить проверку, создайте код проверки, скопируйте его, а затем используйте его для создания сертификата проверки X. 509 в командной строке:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Передайте подписанный сертификат проверки _verification_cert. pem_ , чтобы завершить проверку:

    ![Проверенный сертификат](./media/how-to-connect-devices-x509/verified.png)

Теперь вы можете подключать устройства, имеющие сертификат X. 509, производный от этого основного корневого сертификата. После сохранения группы регистрации запишите область ИДЕНТИФИКАТОРов.

## <a name="run-sample-device-code"></a>Запуск примера кода устройства

1. В приложении IoT Central Azure выберите **устройства**и создайте новое устройство с _МИТЕСТДЕВИЦЕ_ в качестве **идентификатора устройства** в шаблоне устройства **датчик окружающей среды** .

1. Скопируйте файлы _mytestdevice_key. pem_ и _mytestdevice_cert. pem_ в папку, содержащую приложение _environmentalSensor.js_ . Это приложение было создано после завершения работы с [руководством подключение устройства (Node.js)](./tutorial-connect-device-nodejs.md).

1. Перейдите в папку, содержащую приложение environmentalSensor.js, и выполните следующую команду, чтобы установить пакет X. 509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Измените файл **environmentalSensor.js** .
    - Замените `idScope` значение на **область идентификатора** , в которой было внесено Примечание ранее.
    - Замените `registrationId` значение на `mytestdevice` .

1. Измените `require` инструкции следующим образом:

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Измените раздел, который создает клиент, следующим образом:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

1. Измените раздел, который открывает подключение, следующим образом:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

1. Выполните сценарий и убедитесь, что устройство успешно подготовлено:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Вы также можете проверить, отображается ли телеметрия на панели мониторинга.

    ![Проверка телеметрии устройства](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Использование индивидуальной регистрации

Используйте сертификаты X. 509 с индивидуальной регистрацией для тестирования устройства и решения. В индивидуальной регистрации отсутствует корневой или промежуточный сертификат X. 509 в приложении IoT Central. Устройства используют самозаверяющий сертификат X. 509 для подключения к приложению.

## <a name="generate-self-signed-device-cert"></a>Создание самозаверяющего сертификата устройства

В этом разделе вы используете самозаверяющий сертификат X. 509 для подключения устройств к отдельной регистрации, которая используется для регистрации одного устройства. Самозаверяющие сертификаты предназначены только для тестирования.

Создайте самозаверяющий сертификат устройства X. 509, выполнив сценарий. Используйте для имени сертификата только буквы в нижнем регистре и дефисы:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Создание отдельной регистрации

1. В приложении IoT Central Azure выберите **устройства**и создайте новое устройство с **идентификатором устройства** как _Митестселфцертпримари_ из шаблона устройства датчика окружающей среды. Запишите **область идентификаторов**, которая будет использоваться позже.

1. Откройте созданное устройство и нажмите кнопку **подключить**.

1. Выберите **отдельные регистрации** в качестве **метода подключения** и **сертификатов (X. 509)** в качестве механизма:

    ![Отдельная регистрация](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Выберите параметр файл в разделе основная и отправьте файл сертификата с именем _mytestselfcertprimary_cert. pem_ , который был создан ранее.

1. Выберите параметр файл для дополнительного сертификата и отправьте файл сертификата с именем _mytestselfcertsecondary_cert. PEM._ Затем выберите **сохранить**:

    ![Отправка сертификата отдельной регистрации](./media/how-to-connect-devices-x509/individual-enrollment.png)

Теперь устройство подготовлено с помощью сертификата X. 509.

## <a name="run-a-sample-individual-enrollment-device"></a>Запуск примера отдельного устройства регистрации

1. Скопируйте файлы _mytestselfcertprimary_key. pem_ и _mytestselfcertprimary_cert. pem_ в папку, содержащую приложение environmentalSensor.js. Это приложение было создано после завершения работы с [руководством подключение устройства (Node.js)](./tutorial-connect-device-nodejs.md).

1. Измените файл **environmentalSensor.js** , как показано ниже, и сохраните его.
    - Замените `idScope` значение на **область идентификатора** , в которой было внесено Примечание ранее.
    - Замените `registrationId` значение на `mytestselfcertprimary` .
    - Заменить **var девицецерт** как:

        ```javascript
        var deviceCert = {
        cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
        key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
        };
        ```

1. Выполните сценарий и убедитесь, что устройство успешно подготовлено:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Вы также можете проверить, отображается ли телеметрия на панели мониторинга.

    ![Индивидуальная регистрация телеметрии](./media/how-to-connect-devices-x509/telemetry-primary.png)

Вы также можете повторить описанные выше действия для сертификата _митестселфцертсекондари_ .

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как подключать устройства с помощью сертификатов X. 509, предлагаем следующий шаг: Узнайте, как [отслеживать подключение устройств с помощью Azure CLI](howto-monitor-devices-azure-cli.md)
