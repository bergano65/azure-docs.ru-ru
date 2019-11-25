---
title: Настройка устройства для прокси-серверов сети — Azure IoT Edge | Документация Майкрософт
description: Сведения о настройке среды выполнения Azure IoT Edge и модулей IoT Edge с подключением к Интернету для обмена данными через прокси-сервер.
author: kgremban
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a4ed17ceddf01ec628d80dc3ba9f4d7ee305f3b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457155"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Настройка устройства IoT Edge для обмена данными через прокси-сервер

Устройства IoT Edge отправляют HTTPS-запросы для обмена данными с центром IoT. Если устройство подключено к сети, которая использует прокси-сервер, необходимо настроить среду выполнения IoT Edge для обмена данными через сервер. Прокси-серверы могут также влиять на отдельные модули IoT Edge, если они отправляют HTTP- или HTTPS-запросы, которые невозможно направить через центр IoT Edge. 

This article walks through the following four steps to configure and then manage an IoT Edge device behind a proxy server: 

1. **Install the IoT Edge runtime on your device.**

   The IoT Edge installation scripts pull packages and files from the internet, so your device needs to communicate through the proxy server to make those requests. For detailed steps, see the [Install the runtime through a proxy](#install-the-runtime-through-a-proxy) section of this article. For Windows devices, the installation script also provides an [Offline installation](how-to-install-iot-edge-windows.md#offline-installation) option. 

   This step is a one-time process performed on the IoT Edge device when you first set it up. The same connections are also required when you update the IoT Edge runtime. 

2. **Configure the Docker daemon and the IoT Edge daemon on your device.**

   IoT Edge uses two daemons on the device, both of which need to make web requests through the proxy server. The IoT Edge daemon is responsible for communications with IoT Hub. The Moby daemon is responsible for container management, so communicates with container registries. For detailed steps, see the [Configure the daemons](#configure-the-daemons) section of this article. 

   This step is a one-time process performed on the IoT Edge device when you first set it up.

3. **Configure the IoT Edge agent properties in the config.yaml file on your device.**

   The IoT Edge daemon starts the edgeAgent module initially, but then the edgeAgent module is responsible for retrieving the deployment manifest from IoT Hub and starting all the other modules. For the IoT Edge agent to make the initial connection to IoT Hub, configure the edgeAgent module environment variables manually on the device itself. After the initial connection, you can configure the edgeAgent module remotely. For detailed steps, see the [Configure the IoT Edge agent](#configure-the-iot-edge-agent) section of this article.

   This step is a one-time process performed on the IoT Edge device when you first set it up.

4. **For all future module deployments, set environment variables for any module communicating through the proxy.**

   Once your IoT Edge device is set up and connected to IoT Hub through the proxy server, you need to maintain the connection in all future module deployments. For detailed steps, see the [Configure deployment manifests](#configure-deployment-manifests) section of this article. 

   This step is an ongoing process performed remotely so that every new module or deployment update maintains the device's ability to communicate through the proxy server. 

## <a name="know-your-proxy-url"></a>Узнайте URL-адрес своего прокси-сервера

Before you begin any of the steps in this article, you need to know your proxy URL.

URL-адрес прокси-сервера имеет такой формат: **протокол**://**узел прокси-сервера**:**порт прокси-сервера**.

* Используется **протокол** HTTP или HTTPS. The Docker daemon can use either protocol, depending on your container registry settings, but the IoT Edge daemon and runtime containers should always use HTTP to connect to the proxy.

* **Узел прокси-сервера** — это адрес прокси-сервера. If your proxy server requires authentication, you can provide your credentials as part of the proxy host with the following format: **user**:**password**\@**proxy_host**.

* **Порт прокси-сервера** — это сетевой порт, по которому прокси-сервер отвечает на сетевой трафик.

## <a name="install-the-runtime-through-a-proxy"></a>Install the runtime through a proxy

Whether your IoT Edge device runs on Windows or Linux, you need to access the installation packages through the proxy server. Depending on your operating system, follow the steps to install the IoT Edge runtime through a proxy server. 

### <a name="linux"></a>Linux

При установке среды выполнения IoT Edge на устройстве Linux настройте в диспетчере пакетов прохождение через прокси-сервер для доступа к пакету установки. Например, [настройте apt-get для использования прокси-сервера HTTP](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Once your package manager is configured, follow the instructions in [Install Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md) as usual.

### <a name="windows"></a>Windows

If you're installing the IoT Edge runtime on a Windows device, you need to go through the proxy server twice. The first connection downloads the installer script file, and the second connection is during the installation to download the necessary components. You can configure proxy information in Windows settings, or include your proxy information directly in the PowerShell commands. 

The following steps demonstrate an example of a windows installation using the `-proxy` argument:

1. The Invoke-WebRequest command needs proxy information to access the installer script. Then the Deploy-IoTEdge command needs the proxy information to download the installation files. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. The Initialize-IoTEdge command doesn't need to go through the proxy server, so the second step only requires proxy information for Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Если для прокси-сервера применяются сложные учетные данные, которые невозможно добавить в URL-адрес, используйте параметр `-ProxyCredential` в `-InvokeWebRequestParameters`. Например,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Дополнительные сведения о параметрах прокси-сервера см. в статье [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). For more information about Windows installation options, including offline installation, see [Install Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Настройка управляющих программ

IoT Edge relies on two daemons running on the IoT Edge device. The Moby daemon makes web requests to pull container images from container registries. Управляющая программа IoT Edge отправляет HTTPS-запросы для обмена данными с центром IoT.

Both the Moby and the IoT Edge daemons need to be configured to use the proxy server for ongoing device functionality. This step takes place on the IoT Edge device during initial device setup. 

### <a name="moby-daemon"></a>Moby daemon

Since Moby is built on Docker, refer to the Docker documentation to configure the Moby daemon with environment variables. Большинство реестров контейнеров (включая DockerHub и реестры контейнеров Azure) поддерживают HTTPS-запросы, поэтому необходимо задать параметр **HTTPS_PROXY**. Если вы извлекаете образы из реестра, который не поддерживает протокол TLS, необходимо задать параметр **HTTP_PROXY**. 

Choose the article that applies to your IoT Edge device operating system: 

* [Configure Docker daemon on Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * The Moby daemon on Linux devices keeps the name Docker.
* [Configure Docker daemon on Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * The Moby daemon on Windows devices is called iotedge-moby. The names are different because it's possible to run both Docker Desktop and Moby in parallel on a Windows device. 

### <a name="iot-edge-daemon"></a>Управляющая программа IoT Edge

The IoT Edge daemon is configured in a similar manner to the Moby daemon. Создайте переменную среды для службы, выполнив указанные ниже действия с учетом своей операционной системы. 

The IoT Edge daemon always uses HTTPS to send requests to IoT Hub.

#### <a name="linux"></a>Linux

Откройте редактор в терминале, чтобы настроить управляющую программу IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Введите следующий текст, заменив **\<proxy URL>** на адрес и порт прокси-сервера. Сохранитесь и закройте редактор. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Обновите диспетчер служб, чтобы получить новую конфигурацию для IoT Edge.

```bash
sudo systemctl daemon-reload
```

Перезапустите IoT Edge, чтобы изменения вступили в силу.

```bash
sudo systemctl restart iotedge
```

Убедитесь, что переменная среды была создана, а новая конфигурация загружена. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Откройте окно PowerShell от имени администратора и выполните следующую команду, чтобы внести изменения в реестр с помощью новой переменной среды. Замените **\<proxy url>** на адрес и порт прокси-сервера. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Перезапустите IoT Edge, чтобы изменения вступили в силу.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Настройка агента IoT Edge

Агент IoT Edge — это первый модуль, который нужно запустить на любом устройстве IoT Edge. Впервые он запускается с учетом данных, указанных в файле IoT Edge config.yaml. Затем агент Edge подключается к Центру Интернета вещей для получения манифестов развертывания и таким образом сообщает, что на устройстве можно развертывать и другие модули.

This step takes place once on the IoT Edge device during initial device setup. 

1. Откройте файл config.yaml на устройстве IoT Edge. В системах Linux он находится в папке **/etc/iotedge/config.yaml**. В системах Windows он находится в папке **C:\ProgramData\iotedge\config.yaml**. Файл конфигурации защищен, поэтому для доступа к этому файлу требуются права администратора. On Linux systems, use the `sudo` command before opening the file in your preferred text editor. On Windows, open a text editor like Notepad as administrator and then open the file. 

2. В файле config.yaml найдите раздел **Edge Agent module spec**. Определение агента IoT Edge включает параметр **env**, в который можно добавить переменные среды. 

3. Удалите фигурные скобки, которые служат заменителями для параметра env, и добавьте новую переменную в новой строке. Помните, что отступы в YAML составляют два пробела. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. По умолчанию среда выполнения IoT Edge использует AMQP для обмена данными с центром IoT. Некоторые прокси-серверы блокируют порты AMQP. Если это так, настройте edgeAgent на использование протокола AMQP через WebSocket. Добавьте вторую переменную среды.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![Определение edgeAgent с переменными среды](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Сохраните изменения в файле config.yaml и закройте редактор. Перезапустите IoT Edge, чтобы изменения вступили в силу. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Настройка манифестов развертывания  

После того как устройство IoT Edge будет настроено для работы с вашим прокси-сервером, необходимо будет объявлять переменные среды во всех последующих манифестах развертывания. You can edit deployment manifests either using the Azure portal wizard or by editing a deployment manifest JSON file. 

Всегда настраивайте два модуля среды выполнения, edgeAgent и edgeHub, для связи через прокси-сервер, чтобы они могли поддерживать соединение с Центром Интернета вещей. If you remove the proxy information from the edgeAgent module, the only way to reestablish connection is by editing the config.yaml file on the device, as described in the previous section. 

Other IoT Edge modules that connect to the internet should be configured to communicate through the proxy server, too. Но модулям, которые перенаправляют свои сообщения через edgeHub, или модулям, которые могут обмениваться данными только с другими модулями на устройстве, не требуются сведения о прокси-сервере. 

This step is ongoing throughout the life of the IoT Edge device. 

### <a name="azure-portal"></a>портала Azure

Когда вы создаете развертывания для устройств IoT Edge с помощью мастера **Настройка модулей**, каждый модуль включает раздел **Переменные среды**, который можно использовать для настройки подключений прокси-сервера. 

Чтобы настроить агент IoT Edge и модули центра IoT Edge, выберите пункт **Настройка дополнительных параметров среды выполнения Edge** в первом шаге мастера. 

![Настройка дополнительных параметров среды выполнения Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Добавьте переменную среды **https_proxy** и в агент IoT Edge, и в определения модуля центра IoT Edge. Если вы добавили переменную среды **UpstreamProtocol** в файл config.yaml на устройстве IoT Edge, добавьте ее также в определение модуля агента IoT Edge. 

![Задание переменной среды https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Все остальные модули, добавляемые в манифест развертывания, должны соответствовать той же схеме. Раздел переменных среды находится на странице настройки имени и изображения модуля.

### <a name="json-deployment-manifest-files"></a>Файлы манифеста развертывания JSON

Если вы создаете развертывания для устройств IoT Edge с помощью шаблонов в Visual Studio Code или путем создания JSON-файлов вручную, вы можете добавить переменные среды в определение каждого модуля напрямую. 

Используйте следующий формат JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

С переменными среды определение модуля должно выглядеть как в следующем примере центра Edge:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Если вы добавили переменную среды **UpstreamProtocol** в файл config.yaml на устройстве IoT Edge, добавьте ее также в определение модуля агента IoT Edge. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о ролях см. в статье о [среде выполнения IoT Edge](iot-edge-runtime.md).

Информацию об устранении ошибок при установке и настройке см. в статье [Распространенные проблемы и их решения для Azure IoT Edge](troubleshoot.md).

