---
title: Подключение подчиненных устройств — Azure IoT Edge | Документация Майкрософт
description: How to configure downstream or leaf devices to connect to Azure IoT Edge gateway devices.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 719ec736fd2f28f8d8b3b226109bc988c872d10f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457122"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Подключение подчиненного устройства к шлюзу Azure IoT Edge

This article provides instructions for establishing a trusted connection between downstream devices and IoT Edge transparent gateways. In a transparent gateway scenario, one or more devices can pass their messages through a single gateway device that maintains the connection to IoT Hub. Роль подчиненного устройства может выполнять любое приложение или платформа с идентификатором, созданным с помощью облачной службы [Центра Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub). Часто для этих приложений применяется [пакет SDK для устройств Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). A downstream device could even be an application running on the IoT Edge gateway device itself. 

There are three general steps to set up a successful transparent gateway connection. This article covers the third step:

1. The gateway device needs to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination. For more information, see [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md).
2. The downstream device needs a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **The downstream device needs to be able to securely connect to its gateway device.**

В этой статье описаны распространенные проблемы с подключениями подчиненных устройств, приведены инструкции по настройке подчиненных устройств и представлены следующие сведения: 

* принципы работы безопасности транспортного уровня (TLS) и сертификатов; 
* сведения о работе библиотек TLS и использовании сертификатов в разных операционных системах;
* пошаговое руководство по примерам Azure IoT на нескольких языках, которые помогут вам быстро приступить к работе. 

В этой статье под терминами *шлюз* и *шлюз IoT Edge* подразумевается устройство IoT Edge, которое настроенное в качестве прозрачного шлюза. 

## <a name="prerequisites"></a>Технические условия 

Have the **azure-iot-test-only.root.ca.cert.pem** certificate file that was generated in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md) available on your downstream device. Your downstream device uses this certificate to validate the identity of the gateway device. 

## <a name="prepare-a-downstream-device"></a>Подготовка подчиненного устройства

Роль подчиненного устройства может выполнять любое приложение или платформа с идентификатором, созданным с помощью облачной службы [Центра Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub). Часто для этих приложений применяется [пакет SDK для устройств Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). A downstream device could even be an application running on the IoT Edge gateway device itself. However, another IoT Edge device cannot be downstream of an IoT Edge gateway. 

>[!NOTE]
>IoT devices that have identities registered in IoT Hub can use [module twins](../iot-hub/iot-hub-devguide-module-twins.md) to isolate different process, hardware, or functions on a single device. IoT Edge gateways support downstream module connections using symmetric key authentication but not X.509 certificate authentication. 

Чтобы подключить подчиненное устройство к шлюзу Azure IoT Edge, вам потребуются следующие два компонента:

* Устройство или приложение, в настройках которого указана строка подключения устройств Центра Интернета вещей с данными для подключения к шлюзу. 

    This step is explained in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* The device or application has to trust the gateway's **root CA** certificate to validate the TLS connections to the gateway device. 

    This step is explained in detail in the rest of this article. This step can be performed one of two ways: by installing the CA certificate in the operating system's certificate store, or (for certain languages) by referencing the certificate within applications using the Azure IoT SDKs.

## <a name="tls-and-certificate-fundamentals"></a>Основные сведения о TLS и сертификатах

При создании безопасного подключения между подчиненными устройствами и IoT Edge возникают такие же трудности, как и при любом обмене данными через Интернет в режиме "клиент — сервер". Клиент и сервер безопасно обмениваются данными через Интернет по [протоколу TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). Протокол TLS основан на конструкциях стандарта [инфраструктуры открытых ключей (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure), называемых сертификатами. TLS is a fairly involved specification and addresses a wide range of topics related to securing two endpoints. This section summarizes the concepts relevant for you to securely connect devices to an IoT Edge gateway.

Когда клиент подключается к серверу, сервер предоставляет ему *цепочку сертификатов сервера*. Такая цепочка обычно состоит из сертификата корневого ЦС, одного или нескольких сертификатов промежуточных ЦС, и сертификата самого сервера. Клиент устанавливает отношения доверия с сервером, криптографически проверяя всю цепочку сертификатов сервера. This client validation of the server certificate chain is called *server chain validation*. The client cryptographically challenges the service to prove possession of the private key associated with the server certificate in a process called *proof of possession*. The combination of server chain validation and proof of possession is called *server authentication*. Чтобы проверить цепочку сертификатов сервера, клиенту нужна копия сертификата корневого ЦС, который использовался для создания (выдачи) этого сертификата сервера. Обычно браузеры при подключении к веб-сайтам используют предварительно настроенный набор часто используемых сертификатов ЦС, оптимизируя работу клиента. 

Когда устройство подключается к Центру Интернета вещей, оно выполняет роль клиента облачной службы, а служба Центра Интернета вещей представляет собой сервер. Облачная служба Центра Интернета вещей поддерживается сертификатом корневого ЦС с именем **Baltimore CyberTrust Root**, который является общедоступным и широко распространенным. Так как сертификат ЦС для Центра Интернета вещей уже установлен на большинстве устройств, во многих реализациях TLS (OpenSSL, Schannel, LibreSSL) он автоматически используется для проверки сертификата сервера. Возможна ситуация, когда устройство успешно подключается к Центру Интернета вещей, но при попытке подключения к шлюзу IoT Edge возникают проблемы.

Когда устройство подключается к шлюзу IoT Edge, подчиненное устройство выполняет роль клиента, а устройство шлюза представляет собой сервер. Azure IoT Edge позволяет операторам (или пользователям) по своему усмотрению создавать любые цепочки сертификатов шлюза. Оператор может использовать сертификат общедоступного ЦС, например Baltimore, или самозаверяющий сертификат внутреннего корневого ЦС. Сертификаты общедоступных ЦС часто предоставляются за плату, что делает их использование более оправданным для рабочих сценариев. Самозаверяющие сертификаты удобны для разработки и тестирования. The transparent gateway setup articles listed in the introduction use self-signed root CA certificates. 

Если вы используете для шлюза IoT Edge самозаверяющий корневой сертификат, он должен быть установлен или предоставлен для всех подчиненных устройств, которые будут подключаться к этому шлюзу. 

![Установка сертификата шлюза](./media/how-to-create-transparent-gateway/gateway-setup.png)

Дополнительные сведения о сертификатах IoT Edge и некоторых ограничениях для рабочей среды см. в статье [Сведения об использовании сертификатов Azure IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Provide the root CA certificate

To verify the gateway device's certificates, the downstream device needs its own copy of the root CA certificate. If you used the scripts provided in the IoT Edge git repository to create test certificates, then the root CA certificate is called **azure-iot-test-only.root.ca.cert.pem**. If you haven't already as part of the other downstream device preparation steps, move this certificate file to any directory on your downstream device. You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate file.

## <a name="install-certificates-in-the-os"></a>Install certificates in the OS

Installing the root CA certificate in the operating system's certificate store generally allows most applications to use the root CA certificate. There are some exceptions, like NodeJS applications that don't use the OS certificate store but rather use the Node runtime's internal certificate store. If you can't install the certificate at the operating system level, skip ahead to [Use certificates with Azure IoT SDKs](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

Команды из следующего примера устанавливают сертификат ЦС на узле под управлением ОС Ubuntu. This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

You should see a message that says, "Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done."

### <a name="windows"></a>Windows

В следующих шагах описано, как установить сертификат ЦС на узле под управлением ОС Windows. This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

You can install certificates using PowerShell's [Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) as an administrator:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

You can also install certificates using the **certlm** utility: 

1. В меню "Пуск" выполните поиск по элементу **Управление сертификатами компьютеров**. Откроется служебная программа **certlm**.
2. Откройте **Сертификаты — локальный компьютер** > **Доверенные корневые центры сертификации**.
3. Щелкните правой кнопкой мыши элемент **Сертификаты** и выберите **Все задачи** > **Импорт**. Откроется мастер импорта сертификатов. 
4. Выполните действия по его подсказкам и импортируйте файл сертификата `<path>/azure-iot-test-only.root.ca.cert.pem`. По завершении вы увидите сообщение "Импорт выполнен". 

Можно также установить сертификаты программным способом с помощью API .NET, как показано в примере кода для .NET далее в этой статье. 

Обычно приложения используют стек TLS [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel), предоставляемый ОС Windows, для безопасного подключения через TLS. При использовании Schannel *требуется*, чтобы все сертификаты были установлены в хранилище сертификатов Windows перед попыткой установить TLS-подключение.

## <a name="use-certificates-with-azure-iot-sdks"></a>Использование сертификатов с пакетами SDK для Azure IoT

В этом разделе на примере простых приложений описывается, как пакеты SDK для Azure IoT подключаются к устройству IoT Edge. Все эти примеры пытаются подключить клиент устройства и отправить сообщения телеметрии в шлюз, а затем закрывают подключение и завершают работу. 

Прежде чем использовать примеры уровня приложения, следует подготовить следующие два компонента:

* Your downstream device's IoT Hub connection string modified to point to the gateway device, and any certificates required to authenticate your downstream device to IoT Hub. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Полный путь к сертификату корневого ЦС, который вы скопировали (сохранили) на подчиненное устройство.

    Пример: `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJs

Этот раздел содержит пример приложения для подключения клиентского устройства Azure IoT NodeJS к шлюзу IoT Edge. For NodeJS applications, you must install the root CA certificate at the application level as shown here. NodeJS applications don't use the system's certificate store. 

1. Получите пример файла **edge_downstream_device.js** из [репозитория примеров использования пакета SDK для устройств Azure IoT для Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Убедитесь, что у вас есть все необходимые компоненты для запуска примера (см. файл **readme.md**). 
3. В файле edge_downstream_device.js измените переменные **connectionString** и **edge_ca_cert_path**. 
4. В документации по пакету SDK вы найдете инструкции по запуску примера на конкретном устройстве. 

Чтобы помочь вам разобраться в выполняемом примере, мы приводим ниже фрагмент кода, в котором клиентский пакет SDK считывает файл сертификата и использует его для создания безопасного TLS-подключения: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Этот раздел знакомит вас с примером приложения для подключения клиентского устройства Azure IoT .NET к шлюзу IoT Edge. Приложения .NET могут автоматически применять любые сертификаты, установленные в хранилище сертификатов операционной системы на узлах Windows и Linux.

1. Получите пример для **EdgeDownstreamDevice** из [папки примеров .NET для IoT Edge](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Убедитесь, что у вас есть все необходимые компоненты для запуска примера (см. файл **readme.md**). 
3. В файле **Properties/launchSettings.json** измените переменные **DEVICE_CONNECTION_STRING** и **CA_CERTIFICATE_PATH**. Если вы хотите использовать сертификат, установленный в хост-системе в хранилище доверенных сертификатов, оставьте значение этой переменной пустым. 
4. В документации по пакету SDK вы найдете инструкции по запуску примера на конкретном устройстве. 

Чтобы установить доверенный сертификат в хранилище сертификатов программным способом с помощью приложения .NET, воспользуйтесь функцией **InstallCACert()** , которая определена в файле **EdgeDownstreamDevice/Program.cs**. Эта операция является идемпотентной, то есть ее можно безопасно выполнять несколько раз и получать одинаковые значения без побочных эффектов. 

### <a name="c"></a>C

Этот раздел знакомит вас с примером приложения для подключения клиентского устройства Azure IoT C к шлюзу IoT Edge. Пакет SDK для C поддерживает множество библиотек TLS, включая OpenSSL, WolfSSL и Schannel. См. дополнительные сведения о [пакете SDK для Azure IoT C](https://github.com/Azure/azure-iot-sdk-c). 

1. Получите приложение **iotedge_downstream_device_sample** из [набора примеров использования пакета SDK для устройств Azure IoT для С](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Убедитесь, что у вас есть все необходимые компоненты для запуска примера (см. файл **readme.md**). 
3. В файле iotedge_downstream_device_sample.c измените переменные **connectionString** и **edge_ca_cert_path**. 
4. В документации по пакету SDK вы найдете инструкции по запуску примера на конкретном устройстве. 

Пакет SDK для устройств Azure IoT для C предоставляет возможность зарегистрировать сертификат ЦС при настройке клиента. Эта операция не устанавливает сертификат, а просто использует сертификат в текстовом формате прямо из памяти. Сохраненный сертификат предоставляется базовому стеку TLS при установке подключения. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Если вы не используете OpenSSL или другую библиотеку TLS, на узлах Windows пакет SDK по умолчанию применяет Schannel. Чтобы обеспечить правильную работу Schannel, сертификат корневого ЦС для IoT Edge должен быть установлен в хранилище сертификатов Windows, а не с помощью операции `IoTHubDeviceClient_SetOption`. 

### <a name="java"></a>Java:

Этот раздел знакомит вас с примером приложения для подключения клиентского устройства Azure IoT Java к шлюзу IoT Edge. 

1. Получите пример **Send-event** из [набора примеров использования пакета SDK для устройств Интернета Azure IoT для Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Убедитесь, что у вас есть все необходимые компоненты для запуска примера (см. файл **readme.md**). 
3. В документации по пакету SDK вы найдете инструкции по запуску примера на конкретном устройстве.

### <a name="python"></a>Python

Этот раздел знакомит вас с примером приложения для подключения клиентского устройства Azure IoT Python к шлюзу IoT Edge. 

1. Get the sample for **send_message** from the [Azure IoT device SDK for Python samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Ensure that you are either running in an IoT Edge container, or in a debug scenario, have the `EdgeHubConnectionString` and `EdgeModuleCACertificateFile` environment variables set.
3. В документации по пакету SDK вы найдете инструкции по запуску примера на конкретном устройстве. 


## <a name="test-the-gateway-connection"></a>Тестирование подключения к шлюзу

Use this sample command to test that your downstream device can connect to the gateway device: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

This command tests connections over MQTTS (port 8883). If you're using a different protocol, adjust the command as neccessary for AMQPS (5671) or HTTPS (433).

The output of this command may be long, including information about all the certificates in the chain. If your connection is successful, you'll see a line like `Verification: OK` or `Verify return code: 0 (ok)`.

![Verify gateway connection](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Troubleshoot the gateway connection

If your leaf device has intermittent connection to its gateway device, try the following steps for resolution. 

1. Is the gateway hostname in the connection string the same as the hostname value in the IoT Edge config.yaml file on the gateway device?
2. Is the gateway hostname resolvable to an IP Address? You can resolve intermittent connections either by using DNS or by adding a host file entry on the leaf device.
3. Are communication ports open in your firewall? Communication based on the protocol used (MQTTS:8883/AMQPS:5671/HTTPS:433) must be possible between downstream device and the transparent IoT Edge.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как IoT Edge расширяет [возможности автономной работы](offline-capabilities.md) для подчиненных устройств. 
