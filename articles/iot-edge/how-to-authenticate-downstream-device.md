---
title: Проверка подлинности подчиненных устройств — Azure IoT Edge | Документация Майкрософт
description: Проверка подлинности подчиненных устройств или конечных устройств в центре Интернета вещей и маршрутизация их подключений с помощью Azure IoT Edge устройств шлюза.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a032056a684107de3dd00fe4861f34c013a80db
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003622"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Аутентификация подчиненного устройства в Центре Интернета вещей

В сценарии с прозрачным шлюзом подчиненные устройства (иногда называемые конечными устройствами или дочерними устройствами) должны иметь удостоверения в центре Интернета вещей, как и любые другие устройства. В этой статье рассматриваются варианты проверки подлинности подчиненного устройства в центре Интернета вещей, а также показано, как объявить подключение к шлюзу.

Существует три основных шага для настройки успешного подключения к прозрачному шлюзу. В этой статье рассматривается второй шаг.

1. Устройство шлюза должно иметь возможность безопасного подключения к подчиненным устройствам, получать подключения от подчиненных устройств и маршрутизировать сообщения в соответствующее место назначения. Дополнительные сведения см. [в разделе Настройка устройства IOT Edge для работы в качестве прозрачного шлюза](how-to-create-transparent-gateway.md).
2. **Подчиненное устройство должно иметь удостоверение устройства, чтобы иметь возможность проходить проверку подлинности в центре Интернета вещей и взаимодействовать через его устройство шлюза.**
3. Подчиненное устройство должно иметь возможность безопасного подключения к устройству шлюза. Дополнительные сведения см. в статье [Connect a downstream device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md) (Подключение подчиненного устройства к шлюзу Azure IoT Edge).

Подчиненные устройства могут проходить проверку подлинности в центре Интернета вещей одним из трех способов: симметричные ключи (иногда называемые общими ключами доступа), самозаверяющие сертификаты X. 509 или сертификаты X. 509 (CA), подписанные центром сертификации. Шаги проверки подлинности аналогичны шагам, используемым для настройки любого устройства, отличного от IoT-погранично с центром Интернета вещей, с небольшими отличиями для объявления связи шлюза.

Действия, описанные в этой статье, показывают подготовку устройств вручную, а не автоматическую подготовку с помощью службы подготовки устройств для центра Интернета вещей Azure. 

## <a name="prerequisites"></a>Предварительные требования

Выполните действия, описанные в разделе [Настройка устройства IOT Edge для работы в качестве прозрачного шлюза](how-to-create-transparent-gateway.md).

В этой статье *имя узла шлюза* содержится в нескольких точках. Имя узла шлюза объявляется в параметре **HostName** файла config. YAML на устройстве шлюза IOT Edge. Он используется для создания сертификатов в этой статье и упоминается в строке подключения подчиненных устройств. Имя узла шлюза необходимо разрешить в IP-адрес, используя DNS или запись файла узла.

## <a name="symmetric-key-authentication"></a>Проверка подлинности с симметричным ключом

Проверка подлинности с симметричным ключом или проверка подлинности с помощью ключа общего доступа является самым простым способом проверки подлинности в центре Интернета вещей. При использовании проверки подлинности с симметричным ключом ключ Base64 связывается с ИДЕНТИФИКАТОРом устройства IoT в центре Интернета вещей. Вы включаете этот ключ в свои приложения IoT, чтобы устройство может его отображать при подключении к центру Интернета вещей. 

### <a name="create-the-device-identity"></a>Создание удостоверения устройства 

Добавьте новое устройство IoT в центр Интернета вещей, используя портал Azure, Azure CLI или расширение IoT для Visual Studio Code. Помните, что подчиненные устройства должны быть идентифицированы в центре Интернета вещей как обычные устройства IoT, а не IoT Edge устройства. 

При создании нового удостоверения устройства укажите следующие сведения. 

* Создайте идентификатор для устройства.

* Выберите **симметричный ключ** в качестве типа проверки подлинности. 

* При необходимости выберите, чтобы **задать родительское устройство** , и выберите устройство шлюза IOT EDGE, через которое будет подключаться это подчиненное устройство. Этот шаг является необязательным для проверки подлинности с симметричным ключом, но рекомендуется, потому что настройка родительского устройства включает [возможности автономного режима](offline-capabilities.md) для подчиненного устройства. Вы всегда можете обновить сведения об устройстве, чтобы добавить или изменить родительский объект позже. 

   ![Создание идентификатора устройства с проверкой подлинности симметричным ключом на портале](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Для выполнения той же операции можно использовать [расширение IOT для Azure CLI](https://github.com/Azure/azure-iot-cli-extension) . В следующем примере создается новое устройство IoT с проверкой подлинности симметричного ключа и назначается родительское устройство. 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Дополнительные сведения о Azure CLI командах для создания устройств и управления родительскими и дочерними устройствами см. в справочном материале о командах [AZ IOT для Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="connect-to-iot-hub-through-a-gateway"></a>Подключение к центру Интернета вещей через шлюз

Этот же процесс используется для проверки подлинности обычных устройств IoT в центре Интернета вещей с симметричными ключами и для подчиненных устройств. Единственное отличие заключается в том, что необходимо добавить указатель на устройство шлюза для маршрутизации подключения или, в автономном режиме, для выполнения проверки подлинности от имени центра Интернета вещей. 

Для проверки подлинности с симметричным ключом на устройстве не нужно выполнять никаких дополнительных действий для проверки подлинности в центре Интернета вещей. Вам по-прежнему нужны сертификаты, чтобы ваше подчиненное устройство могла подключаться к устройству шлюза, как описано в статье [Подключение подчиненного устройства к шлюзу Azure IOT Edge](how-to-connect-downstream-device.md).

После создания удостоверения устройства IoT на портале можно получить первичные или вторичные ключи. Один из этих ключей должен быть включен в строку подключения, включаемую в любое приложение, которое взаимодействует с центром Интернета вещей. Для проверки подлинности с симметричным ключом центр Интернета вещей предоставляет полностью сформированную строку подключения в сведениях об устройстве для вашего удобства. Необходимо добавить дополнительные сведения об устройстве шлюза в строку подключения. 

Строки подключения к симметричным ключам для подчиненных устройств должны иметь следующие компоненты: 

* Центр Интернета вещей, к которому подключается устройство:`Hostname={iothub name}.azure-devices.net`
* ИДЕНТИФИКАТОР устройства, зарегистрированный в концентраторе:`DeviceID={device ID}`
* Первичный или вторичный ключ:`SharedAccessKey={key}`
* Устройство шлюза, к которому подключается устройство. Укажите значение **имени узла** из файла config. YAML IOT Edge устройства шлюза:`GatewayHostName={gateway hostname}`

Все вместе, полная строка подключения выглядит следующим образом:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Если для этого подчиненного устройства установлена связь "родители-потомки", можно упростить строку подключения, вызвав шлюз непосредственно в качестве узла подключения. Пример: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Проверка подлинности X. 509 

Существует два способа проверки подлинности устройства IoT с помощью сертификатов X. 509. Как и при проверке подлинности, действия по подключению устройства к центру Интернета вещей одинаковы. Выберите самозаверяющий сертификат или сертификаты, подписанные ЦС, для проверки подлинности, а затем продолжайте научиться подключаться к центру Интернета вещей. 

Дополнительные сведения о том, как центр Интернета вещей использует проверку подлинности X. 509, см. в следующих статьях: 
* [Проверка подлинности устройства с помощью сертификатов ЦС X. 509](../iot-hub/iot-hub-x509ca-overview.md)
* [Концептуальное понимание сертификатов ЦС X. 509 в индустрии IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Создание удостоверения устройства с помощью самозаверяющих сертификатов X. 509

Для самостоятельно подписанной аутентификации X. 509, которая иногда называется проверкой подлинности отпечатков, необходимо создать новые сертификаты для размещения на устройстве IoT. Эти сертификаты имеют отпечаток, которые совместно используются с центром Интернета вещей для проверки подлинности. 

Самый простой способ протестировать этот сценарий — использовать тот же компьютер, который использовался для создания сертификатов, в окне [настройка IOT Edge устройства для работы в качестве прозрачного шлюза](how-to-create-transparent-gateway.md). Для создания сертификатов устройств IoT на этом компьютере уже должна быть настроена подходящее средство, сертификат корневого ЦС и сертификат промежуточного ЦС. После этого можно скопировать окончательные сертификаты и их закрытые ключи на подчиненное устройство. Следуя инструкциям в статье о шлюзе, настройте OpenSSL на компьютере, а затем клонировать репозиторий IoT Edge для доступа к скриптам создания сертификатов. Затем вы создали рабочий каталог, который мы вызываем  **\<вркдир >** для хранения сертификатов. Сертификаты по умолчанию предназначены для разработки и тестирования, поэтому только за последние 30 дней. Необходимо создать сертификат корневого ЦС и промежуточный сертификат. 

1. Перейдите к рабочему каталогу в окне bash или PowerShell. 

2. Создайте два сертификата (основной и дополнительный) для подчиненного устройства. Укажите имя устройства, а затем — первичную или вторичную метку. Эти сведения используются для именования файлов, чтобы можно было вести отслеживание сертификатов для нескольких устройств. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Получите отпечаток SHA1 (называемый отпечатком в интерфейсе центра Интернета вещей) из каждого сертификата, который является строкой шестнадцатеричного символа 40. Используйте следующую команду OpenSSL для просмотра сертификата и поиска отпечатка:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Перейдите в центр Интернета вещей на портал Azure и создайте новое удостоверение устройства IoT со следующими значениями: 

   * Выберите в качестве типа проверки подлинности значение **X. 509** .
   * Вставьте шестнадцатеричные строки, скопированные из основного и дополнительного сертификатов устройства.
   * Выберите **задать родительское устройство** и выберите устройство шлюза IOT EDGE, с помощью которого будет подключаться это подчиненное устройство. Родительское устройство требуется для проверки подлинности X. 509 подчиненного устройства. 

   ![Создание идентификатора устройства с помощью самоподписанной проверки подлинности X. 509 на портале](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Скопируйте следующие файлы в любой каталог на подчиненном устройстве:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Вы будете ссылаться на эти файлы в приложениях конечного устройства, которые подключаются к центру Интернета вещей. Для перемещения файлов сертификатов можно использовать службу, такую как [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , или функцию, например [протокол безопасного копирования](https://www.ssh.com/ssh/scp/) .

Вы можете использовать [расширение IOT для Azure CLI](https://github.com/Azure/azure-iot-cli-extension) для выполнения той же операции создания устройства. В следующем примере создается новое устройство Интернета вещей с собственной проверкой подлинности X. 509 и назначается родительское устройство. 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Дополнительные сведения о Azure CLI командах для создания устройств, создания сертификатов, а также управления родительскими и дочерними устройствами см. в справочном материале о командах AZ IOT, посвященных [удостоверениям для центра Интернета вещей](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Создание удостоверения устройства с помощью подписанных сертификатов ЦС X. 509

Для проверки подлинности, подписанной центром сертификации X. 509, необходим сертификат корневого ЦС, зарегистрированный в центре Интернета вещей, который используется для подписи сертификатов для устройства IoT. Для любого устройства, использующего сертификат, который был вызван сертификатом корневого ЦС или любым из его промежуточных сертификатов, будет разрешена проверка подлинности. 

Этот раздел основан на инструкциях, описанных в статье центр Интернета вещей: [Настройка безопасности X. 509 в центре Интернета вещей Azure](../iot-hub/iot-hub-security-x509-get-started.md). Выполните действия, описанные в этом разделе, чтобы определить, какие значения следует использовать для настройки подчиненного устройства, которое подключается через шлюз. 

Самый простой способ протестировать этот сценарий — использовать тот же компьютер, который использовался для создания сертификатов, в окне [настройка IOT Edge устройства для работы в качестве прозрачного шлюза](how-to-create-transparent-gateway.md). Для создания сертификатов устройств IoT на этом компьютере уже должна быть настроена подходящее средство, сертификат корневого ЦС и сертификат промежуточного ЦС. После этого можно скопировать окончательные сертификаты и их закрытые ключи на подчиненное устройство. Следуя инструкциям в статье о шлюзе, настройте OpenSSL на компьютере, а затем клонировать репозиторий IoT Edge для доступа к скриптам создания сертификатов. Затем вы создали рабочий каталог, который мы вызываем  **\<вркдир >** для хранения сертификатов. Сертификаты по умолчанию предназначены для разработки и тестирования, поэтому только за последние 30 дней. Необходимо создать сертификат корневого ЦС и промежуточный сертификат. 

1. Следуйте инструкциям в разделе [Регистрация сертификатов ЦС X. 509 в центре Интернета вещей](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) статьи *Настройка безопасности x. 509 в центре Интернета вещей Azure*. В этом разделе вы выполните следующие действия: 

   1. Отправьте сертификат корневого ЦС. Если вы используете сертификаты, созданные в статье о прозрачном шлюзе, отправьте  **\<вркдир >/цертс/Азуре-ИОТ-тест-Онли.Рут.ка.церт.пем** в качестве файла корневого сертификата. 
   2. Убедитесь, что вы владеете сертификатом корневого ЦС. Вы можете проверить владение с помощью средств CERT в \<вркдир >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Следуйте инструкциям в разделе [Создание устройства x. 509 для центра Интернета вещей](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) статьи *Настройка безопасности x. 509 в центре Интернета вещей Azure*. В этом разделе вы выполните следующие действия: 

   1. Добавьте новое устройство. Укажите имя в нижнем регистре для **идентификатора устройства**и выберите тип проверки подлинности **X. 509, подписанный центром сертификации**. 
   2. Задайте родительское устройство. Для подчиненных устройств выберите **задать родительское устройство** и выберите устройство шлюза IOT EDGE, которое будет предоставлять подключение к центру Интернета вещей. 

3. Создайте цепочку сертификатов для подчиненного устройства. Используйте тот же корневой сертификат ЦС, который вы перегрузили в центр Интернета вещей для создания этой цепочки. Используйте тот же идентификатор устройства в нижнем регистре, который вы присвоили удостоверению устройства на портале.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Скопируйте следующие файлы в любой каталог на подчиненном устройстве: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Вы будете ссылаться на эти файлы в приложениях конечного устройства, которые подключаются к центру Интернета вещей. Для перемещения файлов сертификатов можно использовать службу, такую как [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , или функцию, например [протокол безопасного копирования](https://www.ssh.com/ssh/scp/) .

Вы можете использовать [расширение IOT для Azure CLI](https://github.com/Azure/azure-iot-cli-extension) для выполнения той же операции создания устройства. В следующем примере создается новое устройство IoT с подписанным центром сертификации X. 509 и назначается родительское устройство: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Дополнительные сведения о Azure CLI командах для создания устройств и управления родительскими и дочерними устройствами см. в справочном материале о командах [AZ IOT для Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .


### <a name="connect-to-iot-hub-through-a-gateway"></a>Подключение к центру Интернета вещей через шлюз

Каждый пакет SDK для центра Интернета вещей Azure обрабатывает проверку подлинности X. 509 немного иначе. Тем не менее этот же процесс используется для проверки подлинности обычных устройств IoT в центре Интернета вещей с сертификатами X. 509 и применяется к подчиненным устройствам. Единственное отличие заключается в том, что необходимо добавить указатель на устройство шлюза для маршрутизации подключения или, в автономном режиме, для выполнения проверки подлинности от имени центра Интернета вещей. Как правило, можно выполнить те же действия по проверке подлинности X. 509 для всех устройств центра Интернета вещей, а затем просто заменить значение **HostName** в строке подключения на имя узла устройства шлюза. 

В следующих разделах приведены некоторые примеры для разных языков SDK. 

>[!IMPORTANT]
>В следующих примерах показано, как пакеты SDK для центра Интернета вещей используют сертификаты для проверки подлинности устройств. В рабочем развертывании следует хранить все секретные данные, такие как частные или SAS-ключи, в аппаратный модуль защиты (HSM). 

#### <a name="net"></a>.NET

Пример программы, выполняющей проверку подлинности в центре Интернета вещей с помощью сертификатов X. 509, см. [в разделе Настройка безопасности x. 509 в центре Интернета вещей Azure.](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates) C# Некоторые ключевые строки этого примера приведены здесь, чтобы продемонстрировать процесс проверки подлинности.

При объявлении имени узла для экземпляра DeviceClient используйте имя узла устройства шлюза IoT Edge. Имя узла можно найти в файле config. YAML устройства шлюза. 

Если вы используете тестовые сертификаты, предоставляемые IoT Edge репозиторием Git, ключ к сертификатам — **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

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

#### <a name="c"></a>В

Пример программы на языке C, выполняющей проверку подлинности в центре Интернета вещей с помощью сертификатов X. 509, см. в примере [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) для пакета SDK для IOT. Некоторые ключевые строки этого примера приведены здесь, чтобы продемонстрировать процесс проверки подлинности.

При определении строки подключения для подчиненного устройства используйте имя узла устройства шлюза IoT Edge для параметра **HostName** . Имя узла можно найти в файле config. YAML устройства шлюза. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Пример программы Node. js, выполняющей проверку подлинности в центре Интернета вещей с помощью сертификатов X. 509, см. в разделе пример [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) пакета SDK для центра Интернета вещей для Node. js. Некоторые ключевые строки этого примера приведены здесь, чтобы продемонстрировать процесс проверки подлинности.

При определении строки подключения для подчиненного устройства используйте имя узла устройства шлюза IoT Edge для параметра **HostName** . Имя узла можно найти в файле config. YAML устройства шлюза. 

Если вы используете тестовые сертификаты, предоставляемые IoT Edge репозиторием Git, ключ к сертификатам — **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

В настоящее время пакет SDK для Python поддерживает только использование сертификатов X509 и ключей из файлов, а не определенных встроенных. В следующем примере соответствующие пути хранятся в переменных среды.

При определении имени узла для подчиненного устройства используйте имя узла устройства шлюза IoT Edge для параметра **HostName** . Имя узла можно найти в файле config. YAML устройства шлюза. 

```python
import os
from azure.iot.device import IoTHubDeviceClient, X509

HOSTNAME = "[IoT Edge Gateway Hostname]"
DEVICE_ID = "[Device ID]"

def iothub_client_init():
    x509 = X509(
        cert_file=os.getenv("X509_CERT_FILE"),
        key_file=os.getenv("X509_KEY_FILE")
    )

    client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=HOSTNAME,
        device_id=DEVICE_ID
    )
)

if __name__ == '__main__':
    iothub_client_init()
```

#### <a name="java"></a>Java

Пример программы Java, выполняющей проверку подлинности в центре Интернета вещей с помощью сертификатов X. 509, см. в примере пакета SDK для Интернета вещей Java [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) . Некоторые ключевые строки этого примера приведены здесь, чтобы продемонстрировать процесс проверки подлинности.

При определении строки подключения для подчиненного устройства используйте имя узла устройства шлюза IoT Edge для параметра **HostName** . Имя узла можно найти в файле config. YAML устройства шлюза. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Следующие шаги

Выполнив эту статью, вы должны иметь устройство IoT Edge, работающее в качестве прозрачного шлюза и подчиненного устройства, зарегистрированного в центре Интернета вещей. Далее необходимо настроить подчиненные устройства, чтобы доверять устройству шлюза и отправить в него сообщения. Дополнительные сведения см. в статье [Connect a downstream device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md) (Подключение подчиненного устройства к шлюзу Azure IoT Edge).
