---
title: Создание устройства прозрачного шлюза с помощью Azure IoT Edge | Документация Майкрософт
description: Использование устройства Azure IoT Edge в качестве прозрачного шлюза, позволяющего обрабатывать информацию из подчиненных устройств
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d0ac7fa3a1dbb1c91da5b9919bc2c62de74213b5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456779"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Настройка устройства IoT Edge в качестве прозрачного шлюза

This article provides detailed instructions for configuring an IoT Edge device to function as a transparent gateway for other devices to communicate with IoT Hub. В этой статье под термином *шлюз IoT Edge* подразумевается устройство IoT Edge, которое используется в качестве прозрачного шлюза. For more information, see [How an IoT Edge device can be used as a gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>В данный момент:
> * устройства с поддержкой Edge не могут подключиться к шлюзам IoT Edge; 
> * подчиненные устройства не поддерживают отправку файлов.

There are three general steps to set up a successful transparent gateway connection. This article covers the first step:

1. **The gateway device needs to be able to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination.**
2. The downstream device needs to have a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. The downstream device needs to be able to securely connect to its gateway device. Дополнительные сведения см. в статье [Connect a downstream device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md) (Подключение подчиненного устройства к шлюзу Azure IoT Edge).


For a device to function as a gateway, it needs to be able to securely connect to its downstream devices. Решение Azure IoT Edge позволяет настраивать безопасные подключения между устройствами с использованием инфраструктуры открытых ключей (PKI). В этом случае мы разрешаем подчиненному устройству подключаться к устройству IoT Edge, выполняющему роль прозрачного шлюза. To maintain reasonable security, the downstream device should confirm the identity of the gateway device. This identity check prevents your devices from connecting to potentially malicious gateways.

A downstream device in a transparent gateway scenario can be any application or platform that has an identity created with the [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloud service. Часто для этих приложений применяется [пакет SDK для устройств Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Для любых практических целей в качестве подчиненного устройства можно использовать даже приложение, запущенное на самом устройстве шлюза IoT Edge. However, an IoT Edge device cannot be downstream of an IoT Edge gateway. 

Вы можете создать любую инфраструктуру сертификата, обеспечивающую доверие, необходимое для топологии "устройство — шлюз". In this article, we assume the same certificate setup that you would use to enable [X.509 CA security](../iot-hub/iot-hub-x509ca-overview.md) in IoT Hub, which involves an X.509 CA certificate associated to a specific IoT hub (the IoT hub root CA), a series of certificates signed with this CA, and a CA for the IoT Edge device.

![Установка сертификата шлюза](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>The term "root CA" used throughout this article refers to the topmost authority public certificate of the PKI certificate chain, and not necessarily the certificate root of a syndicated certificate authority. In many cases, it is actually an intermediate CA public certificate. 

The gateway presents its IoT Edge device CA certificate to the downstream device during the initiation of the connection. The downstream device checks to make sure the IoT Edge device CA certificate is signed by the root CA certificate. This process allows the downstream device to confirm that the gateway comes from a trusted source.

The following steps walk you through the process of creating the certificates and installing them in the right places on the gateway. Можно создать сертификаты с помощью любого компьютера и затем скопировать их на устройство IoT Edge. 

## <a name="prerequisites"></a>Технические условия

* A development machine to create certificates. 
* Устройство Azure IoT Edge, настраиваемое в качестве шлюза. Use the IoT Edge installation steps for one of the following operating systems:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Создание сертификатов с помощью Windows

Use the steps in this section to generate test certificates on Windows. You can use a Windows machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

Сертификаты, создаваемые в этом разделе, предназначены только для тестирования. 

### <a name="install-openssl"></a>Установка OpenSSL

Установите OpenSSL для Windows на компьютере, который используется для создания сертификатов. If you already have OpenSSL installed on your Windows device, you may skip this step, but ensure that openssl.exe is available in your PATH environment variable. 

There are several ways to install OpenSSL, including:

* **Easier:** Download and install any [third-party OpenSSL binaries](https://wiki.openssl.org/index.php/Binaries), for example, from [OpenSSL on SourceForge](https://sourceforge.net/projects/openssl/). Добавьте полный путь к файлу openssl.exe в переменную среды PATH. 
   
* **Рекомендуемый способ.** Скачайте исходный код OpenSSL и создайте двоичные файлы на компьютере самостоятельно или с помощью [vcpkg](https://github.com/Microsoft/vcpkg). В следующих инструкциях используется vcpkg для скачивания исходного кода, а также компиляции и установки OpenSSL на компьютере Windows с помощью простых действий.

   1. Перейдите в каталог для установки vcpkg. В дальнейшем он будет называться *\<VCPKGDIR>* . Следуйте указаниям, чтобы скачать и установить [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Once vcpkg is installed, run the following command from a powershell prompt to install the OpenSSL package for Windows x64. Обычно установка занимает около пяти минут.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Добавьте `<VCPKGDIR>\installed\x64-windows\tools\openssl` в переменную среды PATH, чтобы сделать файл openssl.exe доступным для вызова.

### <a name="prepare-creation-scripts"></a>Подготовка скриптов создания

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. Откройте окно PowerShell с правами администратора. 

2. Клонируйте репозиторий Git, который содержит скрипты для создания сертификатов, не являющихся рабочими. Эти скрипты помогут вам создать необходимые сертификаты для настройки прозрачного шлюза. Используйте команду `git clone` или [скачайте ZIP-файл](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Перейдите в каталог, в котором вы планируете работать. Throughout this article, we'll call this directory *\<WRKDIR>* . All certificates and keys will be created in this working directory.

4. Copy the configuration and script files from the cloned repo into your working directory. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   If you downloaded the repo as a ZIP, then the folder name is `iotedge-master` and the rest of the path is the same. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Включите PowerShell для выполнения скриптов.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Bring the functions used by the scripts into PowerShell's global namespace.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   The PowerShell window will display a warning that the certificates generated by this script are only for testing purposes, and should not be used in production scenarios.

8. Verify that OpenSSL has been installed correctly and make sure that there won't be name collisions with existing certificates. При возникновении проблем сценарий должен описывать способы их устранения.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Создание сертификатов

В этом разделе мы создадим три сертификата, а затем соединим их в цепочку. Размещение сертификатов в файле цепочки позволяет легко установить их на устройстве шлюза IoT Edge и любых подчиненных устройствах.  

1. Create the root CA certificate and have it sign one intermediate certificate. The certificates are all placed in your working directory.

   ```powershell
   New-CACertsCertChain rsa
   ```

   This script command creates several certificate and key files, but we're going to refer to one in particular later in this article:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   This script command creates several certificate and key files, including two that we're going to refer to later in this article:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

Now that you have the certificates, skip ahead to [Install certificates on the gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Создание сертификатов с помощью Linux

Use the steps in this section to generate test certificates on Linux. You can use a Linux machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

Сертификаты, создаваемые в этом разделе, предназначены только для тестирования. 

### <a name="prepare-creation-scripts"></a>Подготовка скриптов создания

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. Клонируйте репозиторий Git, который содержит скрипты для создания сертификатов, не являющихся рабочими. Эти скрипты помогут вам создать необходимые сертификаты для настройки прозрачного шлюза. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Перейдите в каталог, в котором вы планируете работать. We'll refer to this directory throughout the article as *\<WRKDIR>* . All certificate and key files will be created in this directory.
  
3. Copy the config and script files from the cloned IoT Edge repo into your working directory.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>Создание сертификатов

В этом разделе мы создадим три сертификата, а затем соединим их в цепочку. Размещение сертификатов в файле цепочки позволяет легко установить их на устройстве шлюза IoT Edge и любых подчиненных устройствах.  

1. Create the root CA certificate and one intermediate certificate. Эти сертификаты помещаются в каталог *\<WRKDIR>* .

   If you've already created root and intermediate certificates in this working directory, don't run this script again. Rerunning this script will overwrite the existing certificates. Instead, proceed to the next step. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   The script creates several certificates and keys. Make note of one, which we'll refer to in the next section:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   The script creates several certificates and keys. Make note of two, which we'll refer to in the next section: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

## <a name="install-certificates-on-the-gateway"></a>Установка сертификатов в шлюзе

Теперь, когда мы создали цепочку сертификатов, необходимо установить ее на устройство шлюза IoT Edge и настроить среду выполнения IoT Edge с указанием ссылки на новые сертификаты. 

1. Скопируйте следующие файлы из каталога *\<WRKDIR>* . Сохраните их в любом расположении на устройстве IoT Edge. Каталог назначения на устройстве IoT Edge мы будем называть *\<CERTDIR>* . 

   * Сертификат ЦС устройства — `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`.
   * Закрытый ключ ЦС устройства — `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`.
   * Root CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate files.  If you generated the certificates on the IoT Edge device itself, you can skip this step and use the path to the working directory.

2. Откройте файл конфигурации управляющей программы безопасности IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Set the **certificate** properties in the config.yaml file to the full path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Remember that indents in yaml are two spaces.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. On Linux devices, make sure that the user **iotedge** has read permissions for the directory holding the certificates. 

## <a name="deploy-edgehub-to-the-gateway"></a>Развертывание модуля EdgeHub в шлюзе

When you first install IoT Edge on a device, only one system module starts automatically: the IoT Edge agent. Для работы устройства в качестве шлюза потребуются оба системных модуля. If you haven't deployed any modules to your gateway device before, create an initial deployment for your device to start the second system module, the IoT Edge hub. The deployment will look empty because you don't add any modules in the wizard, but it will make sure that both system modules are running. 

Проверить, какие модули выполняются на устройстве, можно с помощью команды `iotedge list`. If the list only returns the module **edgeAgent** without **edgeHub**, use the following steps:

1. Найдите нужный Центр Интернета вещей на портале Azure.

2. Перейдите к **IoT Edge** и выберите устройство IoT Edge, которое нужно использовать в качестве шлюза.

3. Щелкните **Set Modules** (Настроить модули).

4. Щелкните **Далее**.

5. На странице **Укажите маршруты** необходимо настроить маршрут по умолчанию для отправки всех сообщений из всех модулей в Центр Интернета вещей. Если такого маршрута нет, добавьте приведенный ниже код, а затем щелкните **Далее**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. На странице **Review template** (Проверка шаблона) щелкните **Отправить**.

## <a name="open-ports-on-gateway-device"></a>Open ports on gateway device

Standard IoT Edge devices don't need any inbound connectivity to function, because all communication with IoT Hub is done through outbound connections. Gateway devices are different because they need to receive messages from their downstream devices. If a firewall is between the downstream devices and the gateway device, then communication needs to be possible through the firewall as well.

For a gateway scenario to work, at least one of the IoT Edge hub's supported protocols must be open for inbound traffic from downstream devices. The supported protocols are MQTT, AMQP, and HTTPS. 

| Port | Протокол |
| ---- | -------- |
| 8883 | MQTT; |
| 5671 | AMQP; |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Маршрутизация сообщений с подчиненных устройств
Среда выполнения IoT Edge может маршрутизировать сообщения, отправляемые с подчиненных устройств. Например, сообщения, отправляемые модулями. This feature allows you to perform analytics in a module running on the gateway before sending any data to the cloud. 

В настоящее время маршрутизация сообщений, отправляемых подчиненными устройствами, заключается в дифференцировании их от сообщений, отправляемых модулями. Все сообщения, отправляемые модулями, в отличие от сообщений, отправляемых подчиненными устройствами, содержат системное свойство с именем **connectionModuleId**. Вы можете использовать предложение маршрута WHERE, чтобы исключить любые сообщения, содержащие это системное свойство. 

The below route is an example that would send messages from any downstream device to a module named `ai_insights`, and then from `ai_insights` to IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Дополнительные сведения о маршрутизации сообщений см. в разделе [Объявление маршрутов](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Enable extended offline operation

Starting with the [v1.0.4 release](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) of the IoT Edge runtime, the gateway device and downstream devices connecting to it can be configured for extended offline operation. 

With this capability, local modules or downstream devices can re-authenticate with the IoT Edge device as needed and communicate with each other using messages and methods even when disconnected from the IoT hub. Дополнительные сведения см. в разделе [Сведения о расширенных возможностях автономной работы устройств, модулей и дочерних устройств IoT Edge](offline-capabilities.md).

To enable extended offline capabilities, you establish a parent-child relationship between an IoT Edge gateway device and downstream devices that will connect to it. Those steps are explained in more detail in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть устройство IoT Edge, работающее в качестве прозрачного шлюза, необходимо настроить подчиненные устройства так, чтобы они доверяли шлюзу и отправляли ему сообщения. Continue on to [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md) for the next steps in setting up your transparent gateway scenario. 
