---
title: Настройка устройства для прокси-серверов сети — Azure IoT Edge | Документация Майкрософт
description: Сведения о настройке среды выполнения Azure IoT Edge и модулей IoT Edge с подключением к Интернету для обмена данными через прокси-сервер.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 883f6022f3d0f609de2d8f33b0285d8c40b7bee9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142126"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Настройка устройства IoT Edge для обмена данными через прокси-сервер

Устройства IoT Edge отправляют HTTPS-запросы для обмена данными с центром IoT. Если устройство подключено к сети, которая использует прокси-сервер, необходимо настроить среду выполнения IoT Edge для обмена данными через сервер. Прокси-серверы могут также влиять на отдельные модули IoT Edge, если они отправляют HTTP- или HTTPS-запросы, которые невозможно направить через центр IoT Edge. 

Настройка устройства IoT Edge для работы с прокси-сервером включает следующие основные шаги: 

1. Установка среды выполнения IoT Edge на устройстве. 
2. Настройте управляющие программы Docker и IoT Edge на устройстве таким образом, чтобы они использовали прокси-сервер.
3. Настройте свойства агента Edge в файле config.yaml на своем устройстве.
4. Задайте переменные среды для среды выполнения IoT Edge и других модулей IoT Edge в манифесте развертывания.

## <a name="know-your-proxy-url"></a>Узнайте URL-адрес своего прокси-сервера

Чтобы настроить управляющую программу Docker и IoT Edge на устройстве, необходимо знать URL-адрес прокси-сервера.

URL-адрес прокси-сервера имеет такой формат: **протокол**://**узел прокси-сервера**:**порт прокси-сервера**.

* Используется **протокол** HTTP или HTTPS. Управляющая программа Docker может использовать любой из протоколов, в зависимости от параметров реестра контейнеров, но для управляющей программы IoT Edge и контейнеров среды выполнения следует всегда использовать протокол HTTPS.

* **Узел прокси-сервера** — это адрес прокси-сервера. Если прокси-сервер требует проверки подлинности, вы можете предоставить свои учетные данные как часть прокси-узел следующий формат: **пользователя**:**пароль**\@**узел_прокси** .

* **Порт прокси-сервера** — это сетевой порт, по которому прокси-сервер отвечает на сетевой трафик.

## <a name="install-the-runtime"></a>Установка среды выполнения

При установке среды выполнения IoT Edge на устройстве Linux настройте в диспетчере пакетов прохождение через прокси-сервер для доступа к пакету установки. Например, [настройте apt-get для использования прокси-сервера HTTP](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Настроив диспетчер пакетов, следуйте инструкциям в статье [Установка среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) или [Установка среды выполнения Azure IoT Edge в Linux (x64)](how-to-install-iot-edge-linux.md) как обычно.

Если вы устанавливаете среду выполнения IoT Edge на устройстве Windows, необходимо дважды проходить через прокси-сервера. Первое соединение — чтобы скачать файл скрипта установщика, а второе — во время установки, чтобы скачать необходимые компоненты. Можно настроить сведения прокси-сервера в параметрах Windows, или включить сведениями о прокси непосредственно в командах PowerShell. Ниже приведен пример установки windows с помощью `-proxy` аргумент:

1. Команда Invoke-WebRequest требуются данные прокси-сервера для доступа к скрипта установки. Затем команда IoTEdge развернуть потребуются данные прокси-сервера следует загружать файлы установки. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Команда IoTEdge инициализации не проходят через прокси-сервера, поэтому второй этап требуется только данные прокси-сервера для Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Дополнительные сведения о параметрах прокси-сервера см. в статье [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Дополнительные сведения о параметрах установки Windows см. в разделе [среды выполнения Azure IoT Edge, установить на Windows](how-to-install-iot-edge-windows.md).

После установки среды выполнения IoT Edge используйте следующий раздел, чтобы настроить для нее сведения о прокси-сервере. 

## <a name="configure-the-daemons"></a>Настройка управляющих программ

Управляющие программы Кита и IoT Edge, установленной на устройстве IoT Edge должны быть настроены для использования прокси-сервера. Управляющая программа Кита делает веб-запросов для образов контейнеров по запросу из реестров контейнеров. Управляющая программа IoT Edge отправляет HTTPS-запросы для обмена данными с центром IoT.

### <a name="moby-daemon"></a>Управляющая программа Кита

Так как Кита основывается на Docker, обратитесь к документации Docker, чтобы настроить управляющую программу Кита с переменными среды. Большинство реестров контейнеров (включая DockerHub и реестры контейнеров Azure) поддерживают HTTPS-запросы, поэтому необходимо задать параметр **HTTPS_PROXY**. Если вы извлекаете образы из реестра, который не поддерживает протокол TLS, необходимо задать параметр **HTTP_PROXY**. 

Выберите статьи, которая применяется к операционной системе устройства IoT Edge: 

* [Настройка управляющей программы Docker в Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Управляющая программа Кита на устройствах Linux рядом с именем Docker.
* [Настройка управляющей программы Docker в Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Управляющая программа Кита на устройствах Windows, называется Кита iotedge. Имена различаются, так как это можно выполнить на устройстве Windows Docker Desktop и Кита в параллельном режиме. 

### <a name="iot-edge-daemon"></a>Управляющая программа IoT Edge

Управляющая программа IoT Edge настраивается так же, как Кита управляющей программе. Все запросы, которые IoT Edge отправляет в центр IoT, используют HTTPS. Создайте переменную среды для службы, выполнив указанные ниже действия с учетом своей операционной системы. 

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

#### <a name="windows"></a> Windows

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

Откройте файл config.yaml на устройстве IoT Edge. В системах Linux он находится в папке **/etc/iotedge/config.yaml**. В системах Windows он находится в папке **C:\ProgramData\iotedge\config.yaml**. Файл конфигурации защищен, поэтому для доступа к этому файлу требуются права администратора. В системах Linux это означает, что прежде, чем открывать файл в предпочтительном редакторе, нужно выполнить команду `sudo`. В Windows это означает, что сначала нужно запустить текстовый редактор, например Блокнот, от имени администратора, а затем открыть файл. 

В файле config.yaml найдите раздел **Edge Agent module spec**. Определение агента IoT Edge включает параметр **env**, в который можно добавить переменные среды. 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

Удалите фигурные скобки, которые служат заменителями для параметра env, и добавьте новую переменную в новой строке. Помните, что отступы в YAML составляют два пробела. 

```yaml
https_proxy: "<proxy URL>"
```

По умолчанию среда выполнения IoT Edge использует AMQP для обмена данными с центром IoT. Некоторые прокси-серверы блокируют порты AMQP. Если это так, настройте edgeAgent на использование протокола AMQP через WebSocket. Добавьте вторую переменную среды.

```yaml
UpstreamProtocol: "AmqpWs"
```

![Определение edgeAgent с переменными среды](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Сохраните изменения в файле config.yaml и закройте редактор. Перезапустите IoT Edge, чтобы изменения вступили в силу. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Настройка манифестов развертывания  

После того как устройство IoT Edge будет настроено для работы с вашим прокси-сервером, необходимо будет объявлять переменные среды во всех последующих манифестах развертывания. Всегда настраивайте два модуля среды выполнения, edgeAgent и edgeHub, для связи через прокси-сервер, чтобы они могли поддерживать соединение с Центром Интернета вещей. Другие модули IoT Edge, которые подключаются к Интернету, необходимо настроить для прокси-сервера. Но модулям, которые перенаправляют свои сообщения через edgeHub, или модулям, которые могут обмениваться данными только с другими модулями на устройстве, не требуются сведения о прокси-сервере. 

Манифесты развертывания можно создать с помощью портала Azure или вручную, отредактировав JSON-файл. 

### <a name="azure-portal"></a>Портал Azure

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
            "value": "https://proxy.example.com:3128"
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

