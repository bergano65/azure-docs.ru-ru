---
title: После установки и подготовки Проверьте успешность и устраните неполадки.
description: включить файл
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979724"
---
## <a name="verify-successful-setup"></a>Проверка успешной установки

Проверьте состояние службы IoT Edge. Оно должно быть указано как работает.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

Проверьте журналы службы.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Если вы только что установили среду выполнения IoT Edge, может отобразиться список ошибок с момента запуска **deploy-IoTEdge** и **Initialize-IoTEdge**. Эти ошибки являются ожидаемыми, так как служба пытается запуститься перед настройкой.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

Запустите [средство устранения неполадок](../articles/iot-edge/troubleshoot.md#run-the-check-command) , чтобы проверить наиболее распространенные ошибки конфигурации и сети.

```powershell
iotedge check
```

Пока вы не развернете первый модуль для IoT Edge на устройстве, модуль **$edgeHub** системы не будет развернут на устройстве. В результате автоматическая проверка будет возвращать ошибку для `Edge Hub can bind to ports on host` проверки подключения. Эта ошибка может игнорироваться, если она не возникает после развертывания модуля на устройстве.

Наконец, перечислите выполняемые модули:

```powershell
iotedge list
```

После новой установки единственным модулем, который вы должны увидеть, является **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Советы и устранение неполадок

В устройствах с ограниченными ресурсами настоятельно рекомендуется присвоить переменной среды *OptimizeForPerformance* значение *false* согласно инструкциям в [руководстве по устранению неполадок](../articles/iot-edge/troubleshoot.md).

Если устройству не удается подключиться к центру Интернета вещей, а в сети есть прокси-сервер, выполните действия, описанные в разделе [Настройка устройства IOT Edge для взаимодействия через прокси-сервер](../articles/iot-edge/how-to-configure-proxy-support.md).

# <a name="linux"></a>[Linux](#tab/linux)

На устройствах Linux требуются повышенные привилегии для выполнения `iotedge` команд. После установки среды выполнения выйдите из системы компьютера, а затем снова войдите для автоматического обновления разрешений. До этого момента используйте `sudo` для запуска команд с повышенными привилегиями.

# <a name="windows"></a>[Windows](#tab/windows)

На устройствах Windows, работающих с контейнерами Windows, модуль контейнеров значок Кита был установлен в составе IoT Edge. Подсистема значок Кита разработана для параллельного выполнения с помощью DOCKER Desktop. Вы можете использовать `docker` команды, если вы хотите напрямую взаимодействовать с контейнерами на устройстве. Однако необходимо специально ориентироваться на подсистему значок Кита на случай, если на устройстве также установлен DOCKER.

Например, чтобы получить список всех образов DOCKER, используйте следующую команду:

```powershell
docker images
```

Чтобы получить список всех образов значок Кита, измените ту же команду, указав указатель на подсистему значок Кита:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Универсальный код ресурса (URI) обработчика указан в выходных данных скрипта установки, или его можно найти в разделе Параметры среды выполнения контейнера для файла config. YAML.

![moby_runtime URI в файле config. YAML](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
