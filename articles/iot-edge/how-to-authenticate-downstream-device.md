---
title: Проверка подлинности подчиненными устройствами - Edge Интернета вещей Azure | Документация Майкрософт
description: Способы проверки подлинности подчиненные устройства или конечных устройств к центру Интернета вещей и направлять их подключение через шлюз устройств Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082395"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Проверка подлинности подчиненного устройства к центру Интернета вещей Azure

В сценарии прозрачного шлюза подчиненные устройства (иногда называется конечных устройств или устройств дочерних) должны удостоверений в центре Интернета вещей, как и любое другое устройство. В этой статье рассматриваются параметры проверки подлинности подчиненного устройства к центру Интернета вещей и демонстрируется объявление подключения шлюза.

Существует три общих действия для настройки подключения успешно прозрачного шлюза. В этой статье описывается второй этап:

1. Шлюз должен иметь возможность безопасного подключения для подчиненных устройств, получать сообщения из подчиненных устройств и маршрутизировать сообщения нужному адресату. Дополнительные сведения см. в разделе [настроить устройство IoT Edge в качестве прозрачного шлюза](how-to-create-transparent-gateway.md).
2. **Подчиненное устройство должен иметь удостоверение устройства, чтобы иметь возможность проверки подлинности с помощью центра Интернета вещей и знать для обмена данными через его устройство шлюза.**
3. Подчиненное устройство необходимо иметь возможность безопасного подключения к его устройство шлюза. Дополнительные сведения см. в статье [Connect a downstream device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md) (Подключение подчиненного устройства к шлюзу Azure IoT Edge).

Подчиненные устройства могут проходить проверку подлинности с помощью центра Интернета вещей с помощью одного из трех методов: симметричные ключи (иногда называют ключи общего доступа), самозаверяющие сертификаты X.509 или сертификат X.509, подписанный сертификации (ЦС) сертификаты. Шаги проверки подлинности похожи на шаги, используемые для настройки любого устройства IoT Edge с центром Интернета вещей с небольшими отличиями для объявления связь шлюза.

В этой статье показано, подготовка устройств вручную, не автоматической подготовки с помощью службу подготовки устройств центра Интернета вещей Azure. 

## <a name="symmetric-key-authentication"></a>Проверка подлинности симметричного ключа

Симметричного ключа проверки подлинности или проверки подлинности ключа общего доступа, — это самый простой способ проверки подлинности с помощью центра Интернета вещей. С помощью симметричного ключа проверки подлинности ключ base64, связанный с идентификатор устройства Интернета вещей в центр Интернета вещей. Вы можете включить этот ключ в приложениях Интернета вещей, таким образом можно получать устройства при подключении к центру Интернета вещей. 

### <a name="create-the-device-identity"></a>Создание удостоверения устройства 

Добавьте новое устройство Интернета вещей в центре Интернета вещей, с помощью портала Azure, Azure CLI или расширения Интернета вещей для Visual Studio Code. Помните, что подчиненные устройства должны быть идентифицированы в центре Интернета вещей как регулярных устройстве Интернета вещей, а не устройства IoT Edge. 

При создании нового удостоверения устройства, укажите следующие сведения: 

* Создайте код для вашего устройства.

* Выберите **симметричный ключ** как тип проверки подлинности. 

* При необходимости, выбрать **задать родительское устройство** и выберите этот подчиненного устройства будут подключаться через устройство шлюза IoT Edge. Этот шаг является необязательным для проверки подлинности симметричного ключа, но рекомендуется, так как родительское устройство включает [возможностей автономной работы](offline-capabilities.md) подчиненного устройства. Вы всегда можете обновить сведения об устройстве, чтобы добавить или изменить родительский объект в более поздней версии. 

   ![Создание идентификатора устройства с помощью симметричного ключа проверки подлинности на портале](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Можно использовать [расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension) для завершения одной операции. В следующем примере создается новое устройство Интернета вещей с помощью симметричного ключа проверки подлинности и назначает родительского устройства: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Дополнительные сведения о командах Azure CLI для создания устройства "и" родители потомки управления см. Справочные материалы по [az iot hub-удостоверения устройства](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) команды.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Подключение к центру Интернета вещей через шлюз

Тот же процесс используется для проверки подлинности регулярных Интернета вещей устройства к центру Интернета вещей с помощью симметричного ключа, также применяется для подчиненных устройств. Единственным различием является необходимость добавить указатель к устройству шлюза для маршрутизации подключения или при автономном использовании, для проверки подлинности от имени центра Интернета вещей. 

Для проверки подлинности симметричного ключа нет никаких дополнительных действий, вам потребуется выполнить на устройстве для его для проверки подлинности с помощью центра Интернета вещей. По-прежнему необходимые сертификаты на месте, что подчиненные устройства могут подключаться к его устройство шлюза, как описано в разделе [соединиться подчиненное устройство шлюза Интернета вещей Azure](how-to-connect-downstream-device.md).

После создания удостоверение устройства Интернета вещей на портале, можно получить его первичный или вторичный ключи. Один из этих ключей необходимо включить в строке подключения, которую хотите включить в любое приложение, которое взаимодействует с центром Интернета вещей. Для проверки подлинности симметричного ключа центр Интернета вещей предоставляет строку полностью сформированные подключения в сведениях об устройстве для вашего удобства. Необходимо добавить дополнительные сведения об устройстве шлюза к строке подключения. 

Строки подключения симметричного ключа для подчиненных устройств необходимы следующие компоненты: 

* Центр Интернета вещей, к которому подключается устройство: `Hostname={iothub name}.azure-devices.net`
* Идентификатор устройства, зарегистрированные в центре: `DeviceID={device ID}`
* Либо первичный или вторичный ключ: `SharedAccessKey={key}`
* Устройство подключается через устройство шлюза. Укажите **hostname** значение из файла config.yaml устройство шлюза Edge Интернета вещей: `GatewayHostName={gateway hostname}`

Все вместе полная строка подключения выглядит как:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Если вы создали отношение "родители потомки" для этого подчиненного устройства, вы можете упростить строку подключения, вызвав шлюза непосредственно в качестве узла подключения. Пример: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Аутентификации на основе X.509 

Существует два способа проверки подлинности на устройстве Интернета вещей с использованием сертификатов X.509. Так вы решили выполнить аутентификацию, инструкции по подключению устройства к центру Интернета вещей одинаковы. Выберите самозаверяющий или подписанный центром сертификации сертификаты для проверки подлинности, а затем сведения о подключении к центру Интернета вещей по-прежнему. 

Дополнительные сведения об использовании аутентификации на основе X.509 в центре Интернета вещей см. в разделе со следующими статьями: 
* [Проверка подлинности устройства с помощью сертификатов X.509 ЦС](../iot-hub/iot-hub-x509ca-overview.md)
* [Концептуальное представление о сертификатах ЦС X.509 в отрасли Интернета вещей](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Создание удостоверения устройства с помощью самозаверяющих сертификатов X.509

Для самозаверяющего проверкой подлинности X.509, иногда называют отпечаток проверки подлинности необходимо создать новые сертификаты для размещения на устройстве Интернета вещей. Эти сертификаты имеют отпечаток в них, которые совместно используют с центром Интернета вещей для проверки подлинности. 

Чтобы протестировать этот сценарий проще всего использовать тот же компьютер, который использовался для создания сертификатов в [настроить устройство IoT Edge в качестве прозрачного шлюза](how-to-create-transparent-gateway.md). Этого компьютера следует уже настраиваться с помощью подходящего средства, сертификат корневого ЦС и сертификат промежуточного ЦС для создания сертификатов устройства Интернета вещей. Можно скопировать окончательный сертификаты и закрытые ключи по в вашей подчиненное устройство позже. Следующие действия, описанные в статье о шлюзах установите openssl на компьютере, затем клонирован репозиторий Edge Интернета вещей для сценариев создания сертификата доступа. Затем, внесенные в рабочий каталог, который мы называем  **\<WRKDIR >** для хранения сертификатов. По умолчанию сертификаты предназначены для разработки и тестирования, поэтому только последние 30 дней. Вам необходимо создать сертификат корневого ЦС и промежуточный сертификат. 

1. Перейдите в рабочий каталог, в окно PowerShell или bash. 

2. Создайте два сертификата (первичный и вторичный) для подчиненного устройства. Укажите имя устройства, а затем метки первичной или вторичной. Эта информация используется для именования файлов таким образом, вы можете хранить список сертификатов для нескольких устройств. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Получить отпечаток SHA1 (называется отпечатка в интерфейсе центра Интернета вещей) из каждого сертификата, который представляет собой строку 40 шестнадцатеричных символов. Чтобы просмотреть сертификат и найти отпечаток, используйте следующую команду openssl:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Перейдите в центр Интернета вещей на портале Azure и создайте новое удостоверение устройства Интернета вещей со следующими значениями: 

   * Выберите **самозаверяющий сертификат X.509** как тип проверки подлинности.
   * Вставьте шестнадцатеричных строк, скопированных из основного и дополнительного сертификатов на устройстве.
   * Выберите **задать родительское устройство** и выбрать этот подчиненного устройства будут подключаться через устройство шлюза IoT Edge. Родительское устройство является обязательным для проверки подлинности X.509 подчиненного устройства. 

   ![Создать идентификатор устройства с собственной подписью проверкой подлинности X.509 на портале](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Скопируйте следующие файлы в любой каталог в вашей подчиненного устройства:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Вы будете ссылаться на эти файлы в приложениях конечных устройств, подключающихся к центру Интернета вещей. Можно использовать службу, например [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) или функцией, такой как [параметр безопасного копирования](https://www.ssh.com/ssh/scp/) для перемещения файлов сертификатов.

Можно использовать [расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension) для завершения одной операции создания устройства. В следующем примере создается новое устройство Интернета вещей с собственной подписью проверкой подлинности X.509 и назначает родительского устройства: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Дополнительные сведения о командах Azure CLI для создания устройства создания сертификата и родительским и дочерним управления см. Справочные материалы по [az iot hub-удостоверения устройства](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) команды.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Создайте устройство сертификаты подписи удостоверения, центром сертификации X.509.

Для центра сертификации (ЦС) со знаком проверкой подлинности сертификатов X.509 требуется сертификат корневого ЦС, зарегистрированных в центре Интернета вещей, используемого для подписи сертификатов для устройства Интернета вещей. Любое устройство, использует сертификат, который был проблемы сертификат корневого ЦС или любой из его промежуточные сертификаты будет разрешено проходить проверку подлинности. 

В этом разделе основан на инструкции, описанные в статье центра Интернета вещей [Настройка сертификата безопасности X.509 в центре Интернета вещей Azure](../iot-hub/iot-hub-security-x509-get-started.md). Выполните действия, описанные в этом разделе, чтобы знать, какие значения следует использовать для настройки подчиненного устройства, которая подключается через шлюз. 

Чтобы протестировать этот сценарий проще всего использовать тот же компьютер, который использовался для создания сертификатов в [настроить устройство IoT Edge в качестве прозрачного шлюза](how-to-create-transparent-gateway.md). Этого компьютера следует уже настраиваться с помощью подходящего средства, сертификат корневого ЦС и сертификат промежуточного ЦС для создания сертификатов устройства Интернета вещей. Можно скопировать окончательный сертификаты и закрытые ключи по в вашей подчиненное устройство позже. Следующие действия, описанные в статье о шлюзах установите openssl на компьютере, затем клонирован репозиторий Edge Интернета вещей для сценариев создания сертификата доступа. Затем, внесенные в рабочий каталог, который мы называем  **\<WRKDIR >** для хранения сертификатов. По умолчанию сертификаты предназначены для разработки и тестирования, поэтому только последние 30 дней. Вам необходимо создать сертификат корневого ЦС и промежуточный сертификат. 

1. Следуйте инструкциям в [сертификаты регистрации ЦС X.509 в центр Интернета вещей](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) раздел *Настройка сертификата безопасности X.509 в центре Интернета вещей Azure*. В этом разделе выполните следующие действия: 

   1. Загрузите корневой сертификат ЦС. Если вы используете сертификаты, созданные в этой статье прозрачного шлюза, передайте  **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem** как файл корневого сертификата. 
   2. Убедитесь, что вы являетесь владельцем этого сертификата корневого ЦС. Вы можете проверить принадлежности со средствами cert в \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Следуйте инструкциям в [создание устройства X.509 для центра Интернета вещей](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) раздел *Настройка сертификата безопасности X.509 в центре Интернета вещей Azure*. В этом разделе выполните следующие действия: 

   1. Добавьте новое устройство. Укажите имя нижний регистр для **идентификатор устройства**и выберите тип проверки подлинности **подписанного сертификатом ЦС X.509**. 
   2. Задайте родительское устройство. Подчиненные устройства выберите **задать родительское устройство** и IoT Edge устройства шлюза, который предоставит подключение к центру Интернета вещей. 

3. Создайте цепочку сертификатов подчиненного устройства. Используйте тот же сертификат корневого ЦС, загруженный в центр Интернета вещей, чтобы сделать эту цепочку. Используйте одинаковым Идентификатором устройства нижнего регистра, введенное в удостоверение устройства на портале.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Скопируйте следующие файлы в любой каталог в вашей подчиненного устройства: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Вы будете ссылаться на эти файлы в приложениях конечных устройств, подключающихся к центру Интернета вещей. Можно использовать службу, например [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) или функцией, такой как [параметр безопасного копирования](https://www.ssh.com/ssh/scp/) для перемещения файлов сертификатов.

Можно использовать [расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension) для завершения одной операции создания устройства. В следующем примере создается новое устройство Интернета вещей с помощью проверки подлинности подписанный ЦС X.509 и назначает родительского устройства: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Дополнительные сведения о командах Azure CLI для создания устройства "и" родители потомки управления см. Справочные материалы по [az iot hub-удостоверения устройства](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) команды.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Подключение к центру Интернета вещей через шлюз

Каждый пакет SDK Интернета вещей Azure обрабатывает аутентификации на основе X.509 немного по-разному. Тем не менее, тот же процесс используется для проверки подлинности регулярных Интернета вещей устройств к центру Интернета вещей с помощью сертификатов X.509, также применяется для подчиненных устройств. Единственным различием является необходимость добавить указатель к устройству шлюза для маршрутизации подключения или при автономном использовании, для проверки подлинности от имени центра Интернета вещей. Как правило, можно выполнить те же действия проверки подлинности X.509 для всех устройств центра Интернета вещей, а затем просто замените значение **Hostname** в строке подключения необходимо имя узла устройства шлюза. 

В следующих разделах показано несколько примеров для различных языков для пакета SDK. 

>[!IMPORTANT]
>Приведенные ниже примеры демонстрируют, как пакеты SDK для центра Интернета вещей использовать сертификаты для проверки подлинности устройств. В рабочей среде следует хранить все секреты, таких как private или ключи SAS в аппаратный модуль безопасности (HSM). 

#### <a name="net"></a>.NET

Пример C# программировать аутентификации в центре Интернета вещей с использованием сертификатов X.509, см. в разделе [Настройка сертификата безопасности X.509 в центре Интернета вещей Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Некоторые из основных строк этого образца они включены здесь для демонстрации процесса проверки подлинности.

При объявлении имени узла для экземпляра DeviceClient, используйте имя узла устройства шлюза IoT Edge. Имя узла можно найти в файле config.yaml устройство шлюза. 

Если вы используете тестовые сертификаты, предоставляемые репозиторий git Edge Интернета вещей, лежат сертификаты **1234**.

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

#### <a name="c"></a>C

Пример программы C аутентификации в центре Интернета вещей с использованием сертификатов X.509, см. в разделе C SDK Интернета вещей [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) образца. Некоторые из основных строк этого образца они включены здесь для демонстрации процесса проверки подлинности.

При определении строку подключения для вашего подчиненное устройство, используйте имя узла устройства шлюза IoT Edge для **HostName** параметра. Имя узла можно найти в файле config.yaml устройство шлюза. 

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

Пример программы Node.js аутентификации в центре Интернета вещей с использованием сертификатов X.509, см. в Node.js SDK Интернета вещей [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) образца. Некоторые из основных строк этого образца они включены здесь для демонстрации процесса проверки подлинности.

При определении строку подключения для вашего подчиненное устройство, используйте имя узла устройства шлюза IoT Edge для **HostName** параметра. Имя узла можно найти в файле config.yaml устройство шлюза. 

Если вы используете тестовые сертификаты, предоставляемые репозиторий git Edge Интернета вещей, лежат сертификаты **1234**.

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

Пример программы Python аутентификации в центре Интернета вещей с использованием сертификатов X.509, см. в разделе пакет Java SDK IoT [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) образца. Некоторые из основных строк этого образца они включены здесь для демонстрации процесса проверки подлинности.

При определении строку подключения для вашего подчиненное устройство, используйте имя узла устройства шлюза IoT Edge для **HostName** параметра. Имя узла можно найти в файле config.yaml устройство шлюза. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)

def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Пример программы Java аутентификации в центре Интернета вещей с использованием сертификатов X.509, см. в разделе пакет Java SDK IoT [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) образца. Некоторые из основных строк этого образца они включены здесь для демонстрации процесса проверки подлинности.

При определении строку подключения для вашего подчиненное устройство, используйте имя узла устройства шлюза IoT Edge для **HostName** параметра. Имя узла можно найти в файле config.yaml устройство шлюза. 

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

## <a name="next-steps"></a>Дальнейшие действия

Выполнив описанные в этой статье, вы должны иметь устройства IoT Edge работает как прозрачный шлюз и подчиненного устройства, зарегистрированные с помощью центра Интернета вещей. Далее необходимо настроить вашего подчиненных устройств, чтобы определить устройства шлюза и отправку сообщений. Дополнительные сведения см. в статье [Connect a downstream device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md) (Подключение подчиненного устройства к шлюзу Azure IoT Edge).
